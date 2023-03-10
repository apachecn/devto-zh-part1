# 用 IntelliJ 进行 Haskell 编程

> 原文：<https://dev.to/shiena/intellij-haskell-2ooc>

虽然 IntelliJ 上有[Haskell 插件](http://plugins.jetbrains.com/plugin?pr=idea&pluginId=6330)，但是很遗憾因为只支持到 IDEA11，所以在最新的 IntelliJ 上无法运行。
但是，最近在[开发网站](http://code.google.com/p/ideah/)的源代码中加入了支持 IDEA12 和 GHC7.4.2 的代码，所以可能会在最近发布！
因为等不到那时候，所以擅自搭建了大楼。

[https://bitbucket.org/shiena/ideah](https://bitbucket.org/shiena/ideah)

随意构建以 r283 为基础进行了以下变更。

*   微修改构建文件
*   Mac 版在 Java7 中无法正常工作，因此修正为 Java6 的 API

另外，动作确认的环境如下所示。

*   苹果 OS X 10.8.3
*   Intellij 理念 12.1.2
*   Haskell 平台

关于安装方法和使用方法，[开发网站的 Wiki](http://code.google.com/p/ideah/wiki/InstallConfig) 提供了参考。

## 2013/12/12 memorial

以 r289 为基础，添加了 IDEA12 用和 IDEA13 用的二进制文件。
因为 IDEA13 的 Mac 版支持 Java7，所以内部版本也是 Java7。 请参考
[selecting the JDK version the ide will run under](https://intellij-support.jetbrains.com/entries/23455956-Selecting-the-JDK-version-the-IDE-will-run-under)更改设置以在 Java7 中启动。
另外，面向 GHC 7.6.3 进行了变更，请注意版本。