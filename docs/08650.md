# 使用选择(2)

> 原文：<https://dev.to/aivarsk/using-select2-2h91>

这开始于一篇黑客新闻评论，关于一篇关于 [select 被彻底破坏](https://idea.popcount.org/2017-01-06-select-is-fundamentally-broken/)的博文，之后同一作者写了为什么 [epoll 也被破坏](https://idea.popcount.org/2017-02-20-epoll-is-fundamentally-broken-12/)。我不知道作者试图解决什么问题，但让我们选择这个:

> 假设您运行一个 HTTP 服务器，为大量短期连接提供服务。您希望每秒接受()尽可能多的连接。只在一个进程中执行 accept()肯定会受到 CPU 的限制。怎么修？

我不是网络编程专家，但在过去的 17 年多时间里，我已经用 C 语言(以及 C++、Python、Java)编写了足够多的网络代码，我也思考过 C10K 问题，阅读过关于服务器、代理、负载平衡器和有效使用 socket API 的论文。从事 [libev](http://software.schmorp.de/pkg/libev.html) 和 [HAProxy](http://www.haproxy.org/) 工作的人肯定有更好的理解，可能会在我的论点中找到漏洞。

## 使用`select`正确的方式

文献通常说`fd_set`被限制为 1024 个描述符，但是有些文献提到可以通过将`FD_SETSIZE`定义为一个更大的数字来增加描述符的数量。上次我检查时，它在 Linux 上不能工作，尽管它在 SunOS 和 AIX 上可以。然而`fd_set`只是一个位数组，你可以根据需要为任意多的位分配内存。结果是，我使用的所有`select`实现都接受了比标准`fd_set`分配的更大的位数组。我仍然不得不将`#define FD_SETSIZE 65536`放在我的代码中，以便 SunOS 使用`select_large_fdset`函数而不是`select`。

```
fd_set *wfds_out = calloc(FDS_BYTES(65536), 1); 
```

Enter fullscreen mode Exit fullscreen mode

为了找出哪些套接字有事件，一些套接字遍历数据结构并检查它们包含的每个套接字。但是更好的方法是将`fd_set`作为`long`的数组进行迭代，一次检查 32-64 位。如果仅设置了任何位，则在嵌套循环中逐个检查它们。一些实现甚至通过“或”位同时检查读、写和异常集——Linux 内核在`fs/select.c`中就是这么做的。

```
unsigned long *wfds = (unsigned long *)wfds_out;
for (unsigned i = 0; i < FDS_LONGS(maxfd); i++, *wfds++) {
  unsigned long bits = *wfds;
  if (bits != 0) {

    unsigned long bit = 1;
    for (unsigned j = 0; j < FDS_BITPERLONG; j++, bit <<= 1) {

      if (bits & bit) {
        int fd = i * FDS_BITPERLONG + j; 
```

Enter fullscreen mode Exit fullscreen mode

一旦收到监听器套接字的通知，最好的方法是多次调用`accept`,直到达到一定的限制(10？50?)或直到返回 EAGAIN 或 EWOULDBLOCK 错误，指示没有挂起的连接。这种方式更有用的工作是在每次迭代中摊销“昂贵的”`select`调用的成本。我发现这是 Abhishek Chandra 和 David Mosberger 写的“Linux 事件调度机制的可伸缩性”。

```
while (1) {
  int client = accept(server, (struct sockaddr *)&sin4, &sin4_len);
  if (client == -1 && (errno == EAGAIN || errno == EWOULDBLOCK)) {
    break;
  } 
```

Enter fullscreen mode Exit fullscreen mode

另一个有用的想法是用间隔控制来换取吞吐量。它的工作原理是限制每秒执行多少个`select`调用，并通过在下一次调用`select`之前休眠来强制执行。在此期间，更多的事件将在套接字上发生，下一个`select`调用将返回更多的事件进行处理。同样，每次迭代都会完成更多有用的工作。其来源是 Eiji Kawai、Youki Kadobayashi、Suguru Yamaguchi 的论文“具有细粒度间隔控制的高效网络 I/O 轮询”。

```
while (1) {
  tnow = usec();
  if (tnow - tprev < 1000) {
    struct timespec spec;
    spec.tv_nsec = 1000 - (tnow - tprev);
    nanosleep(&spec, NULL);
  } else {
    tprev = tnow;
  }

  int nready = select(server + 1, &rfds_out, NULL, NULL, NULL); 
```

Enter fullscreen mode Exit fullscreen mode

## 一切小事

`select`修改传入的描述符集。一个常见的错误是在调用`select`之前，通过遍历数据结构并添加它们包含的所有套接字，从零开始创建描述符集。我发现，由于线程之间的锁定和同步，这需要花费大量的时间。正确的方法是一直维护一个描述符集，并创建它的副本，然后将副本传递给`select`。

`select`返回结果集中文件描述符的数量。您应该对您处理的事件进行计数，并在达到`select`返回的数目时停止。

大多数程序需要存储一些与套接字相关的状态。起初 Map 似乎是一个不错的选择，但是你应该使用一个由 socket 索引的数组(它是一个`int`)。POSIX API 说内核在创建新的文件描述符时必须使用最少的未使用的文件描述符。所以数组将会和你的程序处理的连接一样大，内核将会在数组中找到一个空闲的插槽。

如果您使用由`getrlimit`返回的硬限制，您总是可以预分配连接状态的数组和用于`select`的描述符集。这意味着在运行时没有重新分配，线程之间也没有同步。如果你的连接状态很大，分配指针数组。

## 结果

所以我用我上面描述的大部分想法编写了[样本代码](https://github.com/aivarsk/misc/tree/master/select)。我从头开始写它，没有使用我以前的任何工作，所以它“在我的电脑上工作”,而且有错误。我使用了一个小型的 Linux 虚拟机来运行它，只有 1 个 CPU 和 512 Mb 的内存。我是这样启动服务器的:

```
./accept-server 1025 > accept-server.log 
```

Enter fullscreen mode Exit fullscreen mode

和不同控制台中的客户端:

```
./accept-client 100 127.0.0.1 1025 > /dev/null 
```

Enter fullscreen mode Exit fullscreen mode

它给了我以下结果:

```
awk '{count[$1]++} END {for (word in count) print word, count[word]}' accept-server.log | sort -n
1496608453 40154
1496608454 46292
1496608455 44843
1496608456 47250
1496608457 48005
1496608458 47204
1496608459 45685
1496608460 45992
1496608461 41870
1496608462 44020
1496608463 45392
1496608464 44592
... 
```

Enter fullscreen mode Exit fullscreen mode

第一列是时间戳(从 epoch 开始的秒数)，第二列是接受的连接数。

`accept-server`消耗了 24%的 CPU 时间，`accept-client`消耗了 71%的 CPU 时间。我认为结果相当不错:每秒 4 万次`accept`呼叫。将新的套接字传递给工作线程需要一些时间，但是用一个线程处理数万个`accept`调用仍然是可能的。

## `select` vs 别人

`poll`将每个描述符 8-16 字节传输到内核，相比之下`select`最多 3 位。对于非常稀疏的描述符集，`poll`更有效，如果我有一个监听器线程(`poll`用于几个套接字)和一个单独的连接线程(`select`用于几千个套接字)，我会使用它。

`epoll`使用系统调用(“慢速”)向集合添加或删除描述符。`select`在用户空间做同样的事情。如果每次迭代都必须添加和删除许多描述符，这对于短期连接来说是很典型的，那么`select`可能会更有效。事实上，这是几年前我做的合成测试。只需在 libev 文档中搜索[ev back end _ EPOLL](http://pod.tst.eu/http://cvs.schmorp.de/libev/ev.pod)

## 结束语

我通常忽略关于最快的网络代码的话题，因为对于我处理的问题，应用程序逻辑和数据库访问代码是瓶颈。我见过写得很差的网络代码导致问题，但没有见过调用`accept`成为瓶颈的代码。另一件事是，一旦你达到每秒一定数量的事件，你就应该在几个进程和机器之间分配工作。如果你为谷歌或大型 ISP 工作，或者试图开发一个负载平衡器或代理，情况可能会有所不同。

马丁·汤普森经常谈到机械同情——你必须理解硬件是如何工作的，并在设计软件时考虑到这一点。嗯，你应该同情你的操作系统和它的 API，并找到使用它的最佳方式。

使用 [libev](http://software.schmorp.de/pkg/libev.html) ， [Boost。Asio](http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio.html) 或其他图书馆。我知道。我知道如何高效地使用`select`并不意味着我想写底层代码。