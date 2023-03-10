# 一只有趣的小虫子

> 原文：<https://dev.to/dvdmuckle/a-fun-little-bug>

今天我在调试我一直在做的 shell 时遇到了一个有趣的小 bug，同时还使用了我的 VT420。

我的终端有一段时间很奇怪，有时会在输入密码等事情上无法退格。在这种情况下，使用我的 shell，它将打印`^H`而不是实际删除一个字符。

那么，如果运行`ls -l foo ^H^H^H^H^H^H^H^H^H^H^H^H^H^H^H`会发生什么？

你得到了美妙的`: No such file or directory`。但应该是列出了`foo`的内容。这是怎么回事？

由此可以推断出`ls`中的一些代码。

```
printf("%s: cannot access %s: No such file or directory\n", command, filename); 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，因为`filename`字符串中有退格字符，C 将把它打印出来，*包括*退格字符，这样就删除了关于正在运行的命令和文件名的打印。

谢天谢地，这不是我的 shell 的问题，而是我的终端的问题。修改了退格键的固件设置，包括密码输入的退格。