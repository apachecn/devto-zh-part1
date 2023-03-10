# 如何在高 DPI 显示器上查看 CHM 文件？

> 原文：<https://dev.to/reg__/how-to-view-chm-files-on-high-dpi-monitor-36g9>

使用像 4K 这样的高分辨率显示器，需要将 DPI 缩放比例设置为 100%以外，这是 Windows * * *中的一大难题——它会给许多应用程序带来麻烦。这就是为什么我想尽可能长时间地坚持使用全高清显示器。其中一个不支持 DPI 的应用是微软自己的 CHM 文件浏览器(微软编译的 HTML 帮助)。CHM 是一种常用于软件帮助/文档的文件格式。它是在 Windows 98 中作为旧 HLP (WinHelp)的替代品推出的。虽然现在我们几乎在网上阅读所有的东西，但是一些程序和图书馆仍然在使用它。

在分辨率为 200% DPI 的 4K 显示器上，CHM 文档完全不可读:

[![](img/09df222ad05b49209996e35c7068d3c1.png)T2】](http://asawicki.info/files/hh_vs_DPI_1.png)

我在谷歌上搜索解决方案。一些消息来源称，该应用的工具栏中有字体按钮，可以增加字体大小，但在我的情况下不起作用。[该页面](https://bugs.mysql.com/bug.php?id=53901)表示该按钮的可用性可以在创建 CHM 文件时进行配置。[这一页](https://geekycoder.wordpress.com/2010/01/12/solutions-to-enlarge-the-text-size-of-chm-without-tears/)提到了一些 CHM 格式的替代阅读器(火狐插件，以及独立应用)。

我知道，在高 DPI 下表现不佳的应用程序可以被配置为在“兼容模式”下工作，此时 Windows 只需重新调整其窗口即可。我发现这个默认 CHM 阅读器的可执行文件是 c:\Windows\hh.exe，但是我在这个文件的属性中找不到这个设置。我想可能是因为该文件位于系统目录中，并且由系统拥有，管理员和普通用户没有足够的权限，所以我提出了以下实际可行的解决方案:

1.  将文件 c:\Windows\hh.exe 复制到其他地方，例如复制到 D:\Soft\hh.exe
2.  右键单击文件 D:\Soft\hh.exe。选择属性-->“hh . exe 属性”窗口出现。
3.  转到兼容性选项卡。选中“覆盖高 DPI 缩放行为”。缩放执行人:"。将下面的组合框设置为“系统(增强)”。单击确定->窗口关闭。
4.  使用命令行参数启动该可执行文件，以浏览您选择的 CHM 文件，例如:`D:\Soft\hh.exe "d:\AGS_SDK-5.1.1\ags_lib\doc\amd_ags.chm"` - >文档应该在浏览器中以适合阅读的字体大小打开。每个像素都被缩放为 2x2 像素。

[![](img/54884e698947d9cb5c071337171130e5.png)T2】](http://asawicki.info/files/hh_vs_DPI_2.png)