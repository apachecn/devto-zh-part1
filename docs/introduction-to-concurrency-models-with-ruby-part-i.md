# Ruby 并发模型介绍。第一部分

> 原文：<https://dev.to/exaspark/introduction-to-concurrency-models-with-ruby-part-i>

在这第一篇文章中，我想描述 Ruby 中进程、线程、GIL、EventMachine 和纤程之间的区别。什么时候使用哪个模型，哪些开源项目使用它们，优缺点是什么。

[![What is concurrency?](img/fd7a445b3cfe1c28a3626bd8b90654d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bRLQyu1D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5778/1%2AcKZ7MlP2o3M-IeB1SVcFoA.jpeg)

## 流程

运行多个进程实际上与并发性无关，而是与并行性有关。尽管并行性和并发性经常被混淆，但它们是不同的东西。我喜欢这个简单的类比:

*   并发:让一个人只用一只手玩很多球。不管看起来如何，这个人一次只接住/扔出一个球。

*   平行:让多个人同时玩他们自己的球。

### 顺序执行

想象一下，我们有一个数字范围，我们需要将它转换成一个数组，并找到特定元素的索引:

```
# sequential.rb
range = 0...10_000_000
number = 8_888_888
puts range.to_a.index(number) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ time ruby sequential.rb
8888888
ruby test.rb  0.41s user 0.06s system 95% cpu 0.502 total 
```

Enter fullscreen mode Exit fullscreen mode

执行这段代码大约需要 500 毫秒，使用 1 个 CPU。

### 并行执行

我们可以通过使用多个并行进程和拆分`range`来重写上面的代码。使用标准 Ruby 库中的`fork`方法，我们可以创建一个子进程并执行代码块中的代码。在父进程中，我们可以用`Process.wait` :
等待所有子进程结束

```
# parallel.rb
range1 = 0...5_000_000
range2 = 5_000_000...10_000_000
number = 8_888_888
puts "Parent #{Process.pid}"
fork { puts "Child1 #{Process.pid}: #{range1.to_a.index(number)}" }
fork { puts "Child2 #{Process.pid}: #{range2.to_a.index(number)}" }
Process.wait 
```

Enter fullscreen mode Exit fullscreen mode

```
$ time ruby parallel.rb
Parent 32771
Child2 32867: 3888888
Child1 32865:
ruby parallel.rb  0.40s user 0.07s system 153% cpu 0.309 total 
```

Enter fullscreen mode Exit fullscreen mode

因为每个进程只使用一半的`range`并行工作，所以上面的代码运行得更快，消耗了不止一个 CPU。执行过程中的流程树可能看起来像:

```
# \ - 32771 ruby parallel.rb (parent process)
#  | - 32865 ruby parallel.rb (child process)
#  | - 32867 ruby parallel.rb (child process) 
```

Enter fullscreen mode Exit fullscreen mode

### 优点:

*   进程不共享内存，所以你不能从一个进程改变另一个进程的日期。这使得编码和调试更加容易。

*   [Ruby MRI](https://en.wikipedia.org/wiki/Ruby_MRI) 中的进程是利用不止一个单核的唯一方式，因为有一个 GIL(全局解释器锁，在下面的帖子中可以找到更多信息)。比如说，如果你在做一些数学计算，这可能会有用。

*   派生子进程可能有助于避免不必要的内存泄漏。一旦进程完成，它就释放所有的资源。

### 缺点:

*   由于进程不共享内存，它们会使用大量内存——这意味着运行数百个进程可能会有问题。请注意，由于 Ruby 2.0 `fork`使用 OS [写时复制](https://en.wikipedia.org/wiki/Copy-on-write)，这允许进程共享内存，只要它没有不同的值。

*   流程创建和销毁都很慢。

*   进程可能需要进程间通信。比如 [DRb](https://ruby-doc.org/stdlib-2.4.1/libdoc/drb/rdoc/DRb.html) 。

*   当心[孤儿](https://en.wikipedia.org/wiki/Orphan_process)进程(父进程已经完成或终止的子进程)或[僵尸](https://en.wikipedia.org/wiki/Zombie_process)进程(已经完成执行但仍占用进程表空间的子进程)。

### 例子:

*   [Unicorn](https://bogomips.org/unicorn/)server——它加载应用程序，派生主进程，以产生多个接受 HTTP 请求的工作进程。

*   用于后台处理的 Resque——它运行一个 worker，该 worker 在一个分叉的子进程中顺序执行每个作业。

## 螺纹

尽管 Ruby 从 1.9 版开始使用本地操作系统线程，但是在一个进程中，任何时候都只能有一个线程在执行，即使你有多个 CPU。这是因为 MRI 具有 GIL，这也存在于其他编程语言中，如 Python。

### GIL 为什么会存在？

有几个原因，例如:

*   避免 C 扩展中的竞争情况，不需要担心线程安全。

*   更容易实现，不需要让 Ruby 数据结构线程安全。

早在 2014 年，Matz 就开始考虑[逐渐移除 GIL](https://twitter.com/yukihiro_matz/status/495219763883163648) 。因为 GIL 实际上并不保证我们的 Ruby 代码是线程安全的，也不允许我们使用更好的并发性。

### 竞赛条件

下面是一个带有竞争条件的基本例子:

```
# threads.rb
@executed = false
def ensure_executed
  unless @executed
    puts "executing!"
    @executed = true
  end
end
threads = 10.times.map { Thread.new { ensure_executed } }
threads.each(&:join) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ ruby threads.rb
executing!
executing! 
```

Enter fullscreen mode Exit fullscreen mode

我们创建了 10 个线程来执行我们的方法，并为每个线程调用 join，所以主线程将一直等待，直到所有其他线程都完成。代码打印了两次`executing!`，因为我们的线程共享同一个`@executed`变量。我们的 read ( `unless @executed`)和 set ( `@executed = true`)操作不是原子的，这意味着一旦我们读取了值，在我们设置新值之前，它可能会在其他线程中被更改。

### GIL 和阻塞 I/O

但是拥有不允许同时执行多个线程的 GIL，并不意味着线程就没有用。线程在遇到阻塞的 I/O 操作时释放 GIL，比如 HTTP 请求、数据库查询、磁盘读写甚至`sleep` :

```
# sleep.rb
threads = 10.times.map do |i|
  Thread.new { sleep 1 }
end
threads.each(&:join) 
```

Enter fullscreen mode Exit fullscreen mode

```
$ time ruby sleep.rb
ruby sleep.rb  0.08s user 0.03s system 9% cpu 1.130 total 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，所有 10 个线程都休眠了 1 秒钟，并且几乎同时结束。当一个线程命中`sleep`时，它将执行传递给另一个线程，而不阻塞 GIL。

### 优点:

*   使用的内存比进程少；有可能运行成千上万个线程。它们创造和毁灭的速度也很快。

*   当有缓慢的阻塞 I/O 操作时，线程是有用的。

*   如果需要，可以从其他线程访问内存区域。

### 缺点:

*   需要非常小心的同步以避免竞争条件，通常通过使用锁定原语，这有时可能导致死锁。所有这些都使得编写、测试和调试线程安全代码变得非常困难。

*   对于线程，你必须确保不仅你的代码是线程安全的，而且你使用的任何依赖也是线程安全的。

*   您生成的线程越多，它们切换上下文花费的时间和资源就越多，花费在实际工作上的时间就越少。

### 例子:

*   [Puma](https://github.com/puma/puma) 服务器——允许在每个进程中使用多个线程(集群模式)。与 Unicorn 类似，它预加载应用程序并派生主进程，其中每个子进程都有自己的线程池。线程在大多数情况下工作良好，因为每个 HTTP 请求都可以在一个单独的线程中处理，并且我们不会在请求之间共享很多资源。

*   用于后台处理的 Sidekiq-默认情况下运行一个 25 线程的进程。每个线程一次处理一个作业。

## 事件机

[EventMachine](https://github.com/eventmachine/eventmachine) (又名 EM)是一个用 C++和 Ruby 编写的 gem。它使用[反应器模式](https://en.wikipedia.org/wiki/Reactor_pattern)提供事件驱动的 I/O，基本上可以让您的 Ruby 代码看起来像 Node.js:)在幕后，EM 在运行事件循环期间使用 Linux [select()](http://man7.org/linux/man-pages/man2/select.2.html) 来检查文件描述符的新输入。

使用 EventMachine 的一个常见原因是当您有大量 I/O 操作，并且不想手动处理线程时。从资源使用的角度来看，手动处理线程可能很困难，或者通常过于昂贵。默认情况下，使用 EM 可以用一个线程处理多个 HTTP 请求。

```
# em.rb
EM.run do
  EM.add_timer(1) do
    puts 'sleeping...'
    EM.system('sleep 1') { puts "woke up!" }
    puts 'continuing...'
  end
  EM.add_timer(3) { EM.stop }
end 
```

Enter fullscreen mode Exit fullscreen mode

```
$ ruby em.rb
sleeping...
continuing...
woke up! 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子显示了如何通过执行`EM.system` (I/O 操作)并传递一个块作为回调来运行异步代码，一旦系统命令完成，就会执行回调。

### 优点:

*   对于慢速网络应用程序，如 web 服务器和代理，使用单线程是有可能获得很好的性能的。

*   它允许您避免复杂的多线程编程，上面已经描述了它的缺点。

### 缺点:

*   每个 I/O 操作都应该支持 EM 异步。这意味着你应该使用特定版本的`system`、DB 适配器、HTTP 客户端等。这可能导致猴子打补丁的版本，缺乏支持和有限的选择。

*   每个事件循环节拍在主线程内完成的工作应该很少。此外，也可以使用 [Defer](http://www.rubydoc.info/github/eventmachine/eventmachine/EventMachine.defer) ，它在线程池的独立线程中执行代码，但是，这可能会导致前面讨论的多线程问题。

*   由于错误处理和回调，很难对复杂的系统进行编程。回调地狱在 Ruby 中也是可能的，但是可以用纤程来防止，见下文。

*   EventMachine 本身就是一个巨大的依赖:Ruby 中的 17K LOC(代码行)和 C++中的 10K LOC。

### 例子:

*   [Goliath](https://github.com/postrank-labs/goliath/)–单线程异步服务器。

*   [AMQP](https://github.com/ruby-amqp/amqp)–rabbit MQ 客户端。然而，宝石的创造者建议使用非 em 版本的[兔子](http://rubybunny.info/)。请注意，将工具迁移到 EM-less 实现是大势所趋。比如 [ActionCable](https://github.com/rails/rails/tree/master/actioncable) 的创作者决定用低级 [nio4r](https://github.com/socketry/nio4r) ， [sinatra-synchrony](https://github.com/kyledrake/sinatra-synchrony) 的创作者用[赛璐珞](https://github.com/celluloid/celluloid)重写等等。

## 纤维

纤程是 Ruby 标准库中的轻量级原语，可以手动暂停、恢复和调度。如果你熟悉 JavaScript，它们与 ES6 生成器非常相似(我们也写过一篇关于[生成器和 Redux-Saga](https://engineering.universe.com/what-is-redux-saga-c1252fc2f4d1) 的文章)。在单条线中运行成千上万的纤维是可能的。

通常，纤程与 EventMachine 一起使用，以避免回调并使代码看起来同步。所以，下面的代码:

```
EventMachine.run do
  page = EM::HttpRequest.new('https://google.ca/').get

  page.errback { puts "Google is down" }
  page.callback {
    url = 'https://google.ca/search?q=universe.com'
    about = EM::HttpRequest.new(url).get

    about.errback  { ... }
    about.callback { ... }
  }
end 
```

Enter fullscreen mode Exit fullscreen mode

可以改写成:

```
EventMachine.run do
  Fiber.new {
    page = http_get('http://www.google.com/')
    if page.response_header.status == 200
      about = http_get('https://google.ca/search?q=universe.com')
      # ...
    else
      puts "Google is down"
    end
  }.resume
end

def http_get(url)
  current_fiber = Fiber.current
  http = EM::HttpRequest.new(url).get
  http.callback { current_fiber.resume(http) }
  http.errback  { current_fiber.resume(http) }
  Fiber.yield
end 
```

Enter fullscreen mode Exit fullscreen mode

因此，基本上，`Fiber#yield`将控制返回给恢复纤程的上下文，并返回传递给`Field#resume`的值。

### 优点:

*   纤程允许您通过替换嵌套的回调来简化异步代码。

### 缺点:

*   不要真的解决并发问题。

*   它们很少在应用程序级代码中直接使用。

### 例子:

*   [em-synchrony](https://github.com/igrigorik/em-synchrony)——一个库，由谷歌的性能工程师伊利亚·格里戈利克编写，它将 EventMachine 与 Fibers 集成在一起，用于不同的客户端，如 MySQL2、Mongo、Memcached 等。

## 结论

没有灵丹妙药，所以请根据您的需求选择并发模型。例如，需要运行 CPU 和内存密集型代码并拥有足够的资源——使用进程。必须执行多个 I/O 操作，如 HTTP 请求–使用线程。需要扩展到最大吞吐量——使用 EventMachine。

在本系列的第二部分中，我们将看看诸如 Actors (Erlang，Scala)、通信顺序进程(Go，Crystal)、软件事务内存(Clojure)以及当然还有 Guilds(一种可能在 Ruby 3 中实现的新的并发模型)等并发模型。敬请期待！

*最初发表于[媒体](https://engineering.universe.com/batching-a-powerful-way-to-solve-n-1-queries-every-rubyist-should-know-24e20c6e7b94)。*