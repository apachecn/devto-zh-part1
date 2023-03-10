# 创建特定大小的空文件

> 原文：<https://dev.to/adamkdean/creating-empty-files-with-specific-sizes-pm5>

来自未来的提示(2020 年 2 月 3 日):只要记住 DD 代表磁盘破坏者。小心使用它，不要从网上复制粘贴 dd 摘录。注意安全。

如果您正在进行速度测试，您可能会发现自己需要大文件来测试您的连接速度和稳定性。

在 Linux 上，您可以这样用`dd`来做，创建一个 100 MB 的文件(或大约):

```
dd if=/dev/zero of=output.png bs=1M count=100 
```

Enter fullscreen mode Exit fullscreen mode

在 Windows 服务器上，您可以使用`fsutil`来做到这一点，就像这样:

```
fsutil file createnew output.png 104857600 
```

Enter fullscreen mode Exit fullscreen mode

请记住，1024 字节表示一千字节，1024*1024 字节表示一兆字节，以此类推。