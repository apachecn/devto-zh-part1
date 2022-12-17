# 在您的路径上找不到 rsync

> 原文：<https://dev.to/adamkdean/rsync-could-not-be-found-on-your-path-4i3c>

如果您尝试在 Windows 中使用 vaggar，并且您选择了使用 MinGW 而不是 Cygwin，您可能会在启动 vaggar 实例时遇到以下错误消息:

> 在您的路径上找不到 rsync。确保 rsync 已正确安装在您的系统上，并且在 PATH 中可用。

如果你知道怎么做，这很容易解决。

首先，安装 Cygwin。如果您选择 x64 版本，它将默认安装到`c:\cygwin64\`。当你安装它的时候，你可以选择你想要安装的组件，搜索`rsync`，进入`net`，点击写着`auto`的标签，直到它给你一个版本号，比如`3.0.0`等等。然后继续安装 Cygwin。

接下来，一旦完成，为了使用 MinGW 而不是切换到 Cygwin，我们需要将 bin 目录添加到我们的路径中。导航到 Cygwin 文件夹，然后导航到 bin 文件夹。验证路径。对我来说是`c:\cygwin64\bin`。

1.  右键单击“我的电脑”图标，然后选择“属性”。
2.  单击“高级”选项卡，然后单击“环境变量”按钮。
3.  您应该会看到一个有两个文本框的对话框。顶部的框显示了您的用户设置。底部的框显示您的系统设置。虽然你可能被告知不要碰你的系统设置，但我还是建议你这么做。我试过用户设置，没用。如果你这样做，你打破了它，我没有责任。这是我的免责声明。别傻了。
4.  单击底部框中的路径条目，然后单击“编辑”按钮
5.  滚动到字符串的末尾，并在末尾添加先前找到的 bin 文件夹的路径。一定要先加一个分号。它可能看起来像这样:

`c:\windows;c:\windows\system32`然后你把它改成`c:\windows;c:\windows\system32;c:\cygwin64\bin`等等。

现在，如果你去你的 MinGW 终端，`echo $PATH`，你应该在那里有新的路径。如果没有，请尝试重新启动 MinGW 终端。您可以通过键入`rsync --version`来正确测试它的工作情况:

```
$ rsync --version
rsync  version 3.1.0  protocol version 31
Copyright (C) 1996-2013 by Andrew Tridgell, Wayne Davison, and others.
Web site: http://rsync.samba.org/
Capabilities:
    64-bit files, 64-bit inums, 64-bit timestamps, 64-bit long ints,
    no socketpairs, hardlinks, symlinks, IPv6, batchfiles, inplace,
    append, ACLs, no xattrs, iconv, symtimes, prealloc

rsync comes with ABSOLUTELY NO WARRANTY.  This is free software, and you
are welcome to redistribute it under certain conditions.  See the GNU
General Public Licence for details. 
```

Enter fullscreen mode Exit fullscreen mode

现在再试一次`vagrant up`。任务完成了吗？