# 异步框架真的值得吗？

> 原文：<https://dev.to/legolord208/are-async-frameworks-really-worth-it-4de0>

*这篇“文章”将专门讨论 [Rust](https://www.rust-lang.org/) 和 [Tokio](https://tokio.rs/) ，但是它应该适用于更多的框架*

这与其说是一篇文章，不如说是一个问题。我从事 Synac 已经有一段时间了，我需要在一个线程上处理多个连接。
对于服务器，我选择了东京。它允许我这样做:

```
listener.incoming().for_each(|(conn, addr)| {
    ssl.accept_async(conn).map_err(|_| ()).and_then(move |conn| {
        println!("Client connected!");
    });
});

core.run(); 
```

Enter fullscreen mode Exit fullscreen mode

这样做的问题是，除非你用一种几乎不可能阻塞调用的语言编程(看看你，JavaScript)，否则你通常不能让一切都是异步的。当然，一旦你掌握了窍门，东京就很容易。但是通常你想结合同步，异步，和各种各样的东西。这就是 Tokio 成为问题的地方。对于 synac 客户端，我已经体验过一个只允许阻塞读取的库，所以多线程是不可能的。我不想强迫用户使用内置互斥体，也不想阻止多线程，所以我想出了自己的解决方案。它给了我这样的代码:

```
if let Ok(Some(packet)) = listener.try_read() {
    println!("Packet received: {:?}", packet);
} 
```

Enter fullscreen mode Exit fullscreen mode

*(如果你想知道我是如何施展这个魔法的，我只是做了一些类似于 [read_exact](https://doc.rust-lang.org/std/io/trait.Read.html#method.read_exact) 的事情，但是是在一个结构中。[代码](https://github.com/jD91mZM2/synac-rs/blob/master/src/listener.rs) )*
这最终是一个好的决定，因为我可以在`gtk::timeout_add`内部进行非阻塞读取！而且编写代码也不太难！

现在我想问:什么时候应该使用带内核的异步框架，什么时候应该只写一个非阻塞的监听器？我觉得非阻塞监听器更加灵活，但是 tokio 的存在肯定是有原因的...对吗？