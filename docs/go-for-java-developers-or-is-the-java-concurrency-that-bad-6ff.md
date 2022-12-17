# 去找 Java 开发人员(或者 Java 并发性有那么差？！)

> 原文：<https://dev.to/napicella/go-for-java-developers-or-is-the-java-concurrency-that-bad-6ff>

我绝不是围棋专家，事实上恰恰相反。我目前正在努力熟悉它。我开始熟悉语法、内存和并发模型。像往常一样，我试着将它与我已经知道的东西进行对比，比如 Java。所以我在这个有趣的[演讲](https://www.youtube.com/watch?v=_c_tQ6_3cCg)中被绊住了，在这个演讲中，伟大的[萨玛](https://twitter.com/Sajma)用一些例子介绍了 Go 并发模型。演讲和例子的幻灯片在这里是。在演讲的不远处，一个问题冒了出来:想想用 Java 等其他语言实现同样的事情需要什么？
**真的有那么难吗？我不太确定，我的意思是，Java 没有“select”语句，也没有内置通道，但是在 Java 中复制这些例子应该不难，不是吗？所以我认为用 Java 实现这些例子会很有趣。**

## 去并发

在开始举例之前，这是一个精简的演讲摘要(顺便说一下，这是一个很酷的演讲，所以我真的建议你观看它)。

1.  走向并行
    *   并发模型是基于通信顺序过程(霍尔，1978 年)
    *   并发程序被构造为独立的进程，这些进程按顺序执行，并通过传递消息进行通信。
    *   “不要通过共享内存来交流，通过交流来共享内存”
    *   Go 原语:go 例程、通道和 select 语句
2.  围棋套路
    *   这是一个轻量级线程(它不是一个线程)
    *   通道提供 go 例程之间通信(类似于 Java 中的同步队列)
    *   在 go 程序中选择多路通信

## 举个例子

在示例中，我们必须构建一个查询 google 服务(web、图像和视频服务)的假设客户端。理想情况下，我们希望并行查询这些服务并聚合答案。所有例子的代码都在[github.com/napicella/go-for-java-programmers](https://github.com/napicella/go-for-java-programmers)中。
那么让我们开始吧。

### 第一个例子:并行查询 Google 搜索

这是 go 代码的样子:

```
 func Google(query string) (results []Result) {
            c := make(chan Result)
            go func() { c <- Web(query) }()
            go func() { c <- Image(query) }()
            go func() { c <- Video(query) }()

            for i := 0; i < 3; i++ {
                result := <-c
                results = append(results, result)
            }
            return
        } 
```

Enter fullscreen mode Exit fullscreen mode

Java 呢？我的解决方案包括使用 CompletableFuture，如下所示。

```
 public void google(String query) throws ExecutionException, InterruptedException {
        CompletableFuture<String>[] futures = new CompletableFuture[] {
            supplyAsync(() -> web(query)),
            supplyAsync(() -> image(query)),
            supplyAsync(() -> video(query))
        };

        List<String> result = new ArrayList<>();

        allOf(futures)
            .thenAccept((ignore) -> Arrays.stream(futures)
                                          .map(this::safeGet)
                                          .forEach(result::add)).get();
        // Note: calling get is necessary only because I want to print the result 
        // before returning from the function
        System.out.println(result);
    }

    protected String safeGet(Future<String> future) {
        try {
            return future.get();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

        return "";
    } 
```

Enter fullscreen mode Exit fullscreen mode

网络、图像和视频服务只是随机睡眠的模拟。
那么，java 代码和 go one 有什么区别呢？java 代码有点冗长，代码不像 Go 那样使用线程间的消息传递，除此之外，它们看起来真的很相似。

让我们来看第二个例子。

## 第二个例子:超时

如果我们不想等待速度慢的服务器呢？我们可以使用暂停！我们的想法是等到所有的服务器都回复我们的请求或者超时。

```
 func Google(query string) (results []Result) {
    c := make(chan Result, 3)
    go func() { c <- Web(query) }()
    go func() { c <- Image(query) }()
    go func() { c <- Video(query) }()

    timeout := time.After(80 * time.Millisecond)
    for i := 0; i < 3; i++ {
        select {
        case result := <-c:
            results = append(results, result)
        case <-timeout:
            fmt.Println("timed out")
            return
        }
    }
    return 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看它在 java 中是什么样子的:

```
 public void googleWithTimeout(String query) throws ExecutionException, InterruptedException {
        // This is the first difference with the go example, the result array must 
        // be a synchronized list.
        // Go channel are completely thread safe, so it's totally okay to funnel 
        // data from multiple go routines to an array.
        List<String> result = Collections.synchronizedList(new ArrayList<>());

        // this is not safe since it's possible that all the threads in the thread 
        // pool (default to ForkJoin) are busy, so the timer won't start
        CompletableFuture<Void> timeout = runAsync(() -> timeout(TIMEOUT_MILLIS));

        anyOf(
            allOf(runAsync(() -> result.add(web(query))),
                  runAsync(() -> result.add(image(query))),
                  runAsync(() -> result.add(video(query)))),
            timeout
        ).get();

        System.out.println(result);
    }

    protected Void timeout(int millis) {
        try {
            Thread.sleep(millis);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        return null;
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 java 示例中，与 go 有一个实质性的区别:任务共享结果数组，因此为了让 Java 代码工作，我们需要一个同步数组。另一方面，go 通道是完全线程安全的，所以将数据从多个 Go 例程传递到一个数组是完全可以的。正如评论中提到的，超时的使用并不完全安全，确实有可能线程池中的所有线程(默认为 ForkJoin)都很忙，所以计时器不会启动。我们显然可以用不同的 ExecutorService 运行一个线程，或者只是手动创建一个线程并运行它。

```
 protected CompletableFuture<Void> timeout(int millis) {
        ScheduledExecutorService executorService = Executors.newScheduledThreadPool(1);

        final CompletableFuture<Void> timeout = new CompletableFuture<>();
        executorService.schedule(() -> {
            timeout.complete(null);
        }, millis, TimeUnit.MILLISECONDS);

        return timeout;
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 第三个例子:使用复制搜索服务器减少尾部延迟。

在 go:

```
func Google(query string) (results []Result) {
    c := make(chan Result, 3)
    go func() { c <- First(query, Web1, Web2) }()
    go func() { c <- First(query, Image1, Image2) }()
    go func() { c <- First(query, Video1, Video2) }()
    timeout := time.After(80 * time.Millisecond)
    for i := 0; i < 3; i++ {
        select {
        case result := <-c:
            results = append(results, result)
        case <-timeout:
            fmt.Println("timed out")
            return
        }
    }
    return 
```

Enter fullscreen mode Exit fullscreen mode

其中函数首先定义如下:

```
func First(query string, replicas ...Search) Result {
    c := make(chan Result, len(replicas))
    searchReplica := func(i int) { c <- replicas[i](query) }
    for i := range replicas {
        go searchReplica(i)
    }
    return <-c
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看 Java 中的

```
 public void googleWithReplicatedServers(String query) throws ExecutionException, InterruptedException {
        List<String> result = Collections.synchronizedList(new ArrayList<>());

        // Unfortunately this does not work as expected because the inner anyOf 
        // won't stop the other calls, so the result might end up having 
        // duplicates, i.e [some-image, some-image, some-video]
        anyOf(
            allOf(
                anyOf(runAsync(() -> result.add(web(query))), runAsync(() -> result.add(webReplica(query)))),
                anyOf(runAsync(() -> result.add(image(query))), runAsync(() -> result.add(imageReplica(query)))),
                anyOf(runAsync(() -> result.add(video(query))), runAsync(() -> result.add(videoReplica(query))))
            ),
            timeout(TIMEOUT_MILLIS)
        ).get();

        System.out.println(result);
    } 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，代码并不完全工作，因为当一个 future 完成时会将结果添加到数组中，但另一个的执行将继续导致结果重复。
让我们更正一下:

```
 // replicate servers and use the first response - fixing the problem mentioned 
    // earlier by using supplyAsync + thenAccept instead of runAsync
    public void googleWithReplicatedServers2(String query) throws ExecutionException, InterruptedException {
        List<String> result = Collections.synchronizedList(new ArrayList<>());

        anyOf(
            allOf(
                anyOf(supplyAsync(() -> web(query)),
                      supplyAsync(() -> webReplica(query))).thenAccept((s) -> result.add((String) s)),
                anyOf(supplyAsync(() -> image(query)),
                      supplyAsync(() -> imageReplica(query))).thenAccept((s) -> result.add((String) s)),
                anyOf(supplyAsync(() -> video(query)),
                      supplyAsync(() -> videoReplica(query))).thenAccept((s) -> result.add((String) s))
            ),
            timeout(TIMEOUT_MILLIS)
        ).get();

        System.out.println(result);
    }

    // same as above, but this time we use the function 'first', which is really 
    // just a wrapper around CompletableFuture.anyOf
    public void googleWithReplicatedServers3(String query) throws ExecutionException, InterruptedException {
        List<String> result = Collections.synchronizedList(new ArrayList<>());

        anyOf(
            allOf(
                first(query, Google::web, Google::webReplica).thenAccept((s) ->  result.add((String) s)),
                first(query, Google::image, Google::imageReplica).thenAccept((s) ->  result.add((String) s)),
                first(query, Google::video, Google::videoReplica).thenAccept((s) ->  result.add((String) s))
            ),
            timeout(TIMEOUT_MILLIS)
        ).get();

        System.out.println(result);
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

除了我从 CompletableFuture 中获得了一些乐趣之外，Go 的明显优势实际上是，并发模型是在语言本身中构建的，这简化了不同代理之间的通信。另一方面，我不确定他们为什么放弃 OOP 支持，比如类。我是说，OOP 有什么问题？