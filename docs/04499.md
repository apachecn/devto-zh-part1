# 运行本地 NPM 可执行文件

> 原文：<https://dev.to/hkly/running-local-npm-executables-cle>

通常，npm 命令行可执行文件是全局安装的，因此您可以从计算机上的任何项目中运行它。然而，有时您会想要运行本地安装的包。祝您身体健康

当带有可执行文件的 npm 包被本地安装时，它们被链接到您的项目中的`./node_modules/.bin`。要调用它们，您必须输入指向包名的完整路径。

例如，如果您已经为单个项目本地安装了`gulp`，那么从项目目录`./node_modules/.bin/gulp`中调用它看起来就像这样

为了让生活更简单，您可以将这段脚本添加到您的`.bashrc` :

```
npm-run() {
  $(npm bin)/$*
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的是`npm bin`返回可执行文件所在的路径，然后您想要调用的包名被插入到`$*`所在的位置。

所以现在，要运行`gulp`，你所要做的就是`npm-run gulp`。

你甚至可以用一个更短的名字代替`npm-run`来定义函数，比如`nr`，或者任何你想要的名字:)

易如反掌！