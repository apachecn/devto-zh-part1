# 使用 Go 频道构建爬虫

> 原文：<https://dev.to/jorinvo/use-go-channels-to-build-a-crawler-8jl>

前几天我做了一个爬虫，检查你网站上的链接，看看有没有可以从 HTTP 更新到 HTTPS 的链接。你可以在 qvl.io/httpsyet 的[找到它。](https://qvl.io/httpsyet)

我提出了一个使用通道抽象协调的实现，我想在本文中分享它。

让我们从需求开始:

该程序应该从我们传递给它的 URL 开始，找到网站上的所有链接，并递归遍历所有子页面。所有以`http://`开头的外部链接都应该进行测试，如果它们也能与`https://`一起工作的话。所有这些都应该同时发生。

请注意，我在本文中跳过了验证和一些设置。

爬虫是一个功能单一的设置`struct`。

```
type Crawler struct {
    Sites    []string  // At least one URL.
    Out      io.Writer // Writes one site per line.
}

func (c Crawler) Run {} 
```

Enter fullscreen mode Exit fullscreen mode

*上面类似于有一个以设置为参数的函数，
，但是它允许软件包的用户写`httpsyet.Crawler{}.Run()`而不是`httpsyet.Run(httpsyet.Crawler{})`。*

现在，让我们看看如何使用通道来实现该架构:

首先，我们希望输出所有可以更新的 URL。我们不是简单地从`Run`函数同步返回它们，而是将它们写入一个`io.Writer`，在`Crawler`中指定为`c.Out`。由于爬行应该是并行发生的，并且不能保证并发使用`io.Writer`是安全的，所以使用通道
更好地抽象了对输出的写入

```
results := make(chan string)
defer close(results)
go func() {
  for r := range results {
    if _, err := fmt.Fprintln(c.Out, r); err != nil {
      c.Log.Printf("failed to output '%s': %v\n", r, err)
    }
  }
}() 
```

Enter fullscreen mode Exit fullscreen mode

在我们启动并行工作器进行爬行之前，我们需要一种向工作器发送新站点的方法。然而，单个通道是不够的，因为工作人员自己从链接中收集更多的站点，这些链接也需要排队等待爬行，而使用单个通道将无法知道是否有更多的站点需要爬行。此外，我们需要确保每个 URL 只爬行一次，以免在链接引用当前或父站点时陷入循环。

我想到的是一个函数`makeQueue`，它返回三个通道:

```
func makeQueue() (chan<- site, <-chan site, chan<- int) {
    queueCount := 0
    wait := make(chan int)
    sites := make(chan site)
    queue := make(chan site)
    visited := map[string]struct{}{}

    go func() {
        for delta := range wait {
            queueCount += delta
            if queueCount == 0 {
                close(queue)
            }
        }
    }()

    go func() {
        for s := range queue {
            u := s.URL.String()
            if _, v := visited[u]; !v {
                visited[u] = struct{}{}
                sites <- s
            } else {
                wait <- -1
            }
        }
        close(sites)
        close(wait)
    }()

    return queue, sites, wait
} 
```

Enter fullscreen mode Exit fullscreen mode

让我解释一下这是做什么的:

*   `sites`是可读通道。这是工人们应该从 T2 爬过去的通道。
*   是一个可写的通道，所有需要被抓取的站点都应该被发送到这个通道。网站不应该被直接发送到`sites`，因为`queue`内部跟踪`visited` *集合*中的所有网站，以确保每个网站只被抓取一次。
*   我说`visited`是一个*集合*，但是 Go 没有*集合*数据类型。然而，可以使用空的`struct{}`作为值类型的`map`。
*   `wait`是另一个可写通道。在内部`makeQueue`保持一个计数器，记录还有多少站点将被排队。通过向`wait`发送值，我们可以改变那个计数器。对于我们抓取的每个站点，我们发送`wait <- -1`，对于我们排队的所有新站点，我们发送`wait <- len(sites)`。一旦计数器到达`0`，我们就知道不会再有网站需要抓取了。
*   因为我们有`wait`通道，它跟踪我们何时完成爬行，一旦完成，所有通道都可以在内部关闭。`makeQueue`的调用者从来不用关闭任何通道。呼叫者可以简单地在`sites`上*移动*，直到`wait`计数器到达`0`。

接下来，我们希望启动一些并行抓取网站的工作程序。这是一个很常见的场景，在 Go 标准库中有一个工具，叫做 [`WaitGroup`](https://golang.org/pkg/sync/#WaitGroup) 。

```
var wg sync.WaitGroup
for i := 0; i < 10; i++ {
  wg.Add(1)
  go func() {
    defer wg.Done()
    // Pass channels to each worker
    worker(sites, queue, wait, results)
  }()
}

wg.Wait() 
```

Enter fullscreen mode Exit fullscreen mode

在这个设置中，我们启动`10` workers 并阻塞，直到它们全部退出。

每个 worker 看起来是这样的(这里去掉了错误处理和日志记录):

```
func (c Crawler) worker(
  sites <-chan site,
  queue chan<- site,
  results chan<- string,
  wait chan<- int,
) {
  for s := range sites {
    urls, shouldUpdate := crawlSite(s)

    if shouldUpdate {
      results <- fmt.Sprintf("%v %v", s.Parent, s.URL.String())
    }

    wait <- len(urls) - 1

    go queueURLs(queue, urls)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

内部*工作线程在`sites`通道上*运行，这意味着所有工作线程运行直到`sites`关闭。如前所述，一旦等待计数器到达`0`，则`sites`在`makeQueue`自动关闭。
工作人员将可以更新到 HTTPS 的链接发送到`results`频道。每个工人将在网站上找到的链接排队。排队本身发生在一个单独的 Goroutine 中，它只是将链接发送给`queue`。通过在排队前更新等待计数，队列知道还有更多站点要来。

请注意，`wait <- len(urls) - 1`结合了当前站点的递减计数(`-1`)和所有待排队站点的递增计数。如果我们在向上计数之前先向下计数，那么等待计数器可能会在我们实际完成之前到达`0`。

我们也可以使用一个[缓冲通道](https://gobyexample.com/channel-buffering)来代替后台排队，但是这将迫使我们设置一个固定的缓冲区大小。通过使用 Goroutines 在后台进行排队，工作人员可以继续搜索站点，我们将需要排队的站点缓存在内存中。

启动爬虫程序所缺少的最后一部分是对站点进行初始排队。这需要在所有其他设置完成之后，但就在`wg.Wait()`调用之前进行:

```
for _, u := range urls {
  queue <- site{
    URL:    u,
    Parent: nil,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没有涵盖实现的所有细节，我鼓励你看看 Github 上的源代码[。本文主要关注在并发场景中使用通道来处理通信，这需要比单个通道多一点的通道，确切地说是 4 个通道和 1 个`WaitGroup`。](https://github.com/qvl/httpsyet)

如果您有任何问题，请让我知道，我真的很有兴趣听到这种情况下的其他解决方案！

*注:本文原载于 [jorin.me](https://jorin.me/use-go-channels-to-build-a-crawler/) 。*