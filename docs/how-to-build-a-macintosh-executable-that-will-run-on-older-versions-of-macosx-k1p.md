# 如何构建一个可以在旧版本 MacOSX 上运行的 Macintosh 可执行文件。

> 原文：<https://dev.to/highperformancecoder/how-to-build-a-macintosh-executable-that-will-run-on-older-versions-of-macosx-k1p>

这花了我一整天的时间钻研 MacOSX，在互联网上缺乏合适的建议，这就是我写这篇文章的原因。

## -mmacosx-version-min 编译器标志

第一个提示是使用-mmacosx-version-min 编译器标志，它采用 10.9(对于 Mavericks)或 10.12(对于 Sierra)这样的值。如果您要编译自包含源代码中的所有内容，只需将这个编译器标志添加到 CFLAGS 变量中，然后构建即可。我通过实验发现 Mavericks 大概是最早支持 C++11 标准库的 OSX。

## 检查可执行文件或库的最低版本

使用 otool -l *可执行文件或库*，然后查找标签 LC_VERSION_MIN_MACOSX

## ma cosx _ DEPLOYMENT _ TARGET 环境变量

如果不指定上述编译器标志，那么 clang 编译器将检查 MACOSX_DEPLOYMENT_TARGET 环境变量的值，并使用该值作为编译器的目标。这是一种设置部署目标的有用方式，无需编辑一堆文件(假设您正在编译一堆第三方库)。

如果未设置环境变量，则使用当前的 OSX 发布版本。

## MacPorts

MacPorts 的问题在于它覆盖了 MACOSX_DEPLOYMENT_TARGET 并将其设置为您当前机器的值。

在大量浏览 MacPorts 使用的 TCL 脚本后，我发现可以将其作为配置选项添加到/opt/local/etc/MAC ports/MAC ports . conf

```
macosx\_deployment\_target 10.9 buildfromsource always 
```

需要第二个选项来防止 macports 下载预构建的二进制包。

最后一个提示是，如果你在设置上述选项之前已经构建了一些包，那么你可以通过
重新构建端口

```
port upgrade --force installed 
```