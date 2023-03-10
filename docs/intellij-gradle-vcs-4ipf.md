# 陷入了 IntelliJ、Gradle 和 VCS

> 原文：<https://dev.to/shiena/intellij-gradle-vcs-4ipf>

在 IntelliJ 上使用 Gradle 和 VCS 时，如果符合以下构成就会发生的问题。

*   Gradle 的构成必须是平面风格的多项目
*   在 VCS 中使用了 Git 或 Mercurial

关于平面风格，请参阅之前投稿的[Gradle 上的多项目](http://qiita.com/shiena/items/371fe817c8fb6be2bb1e)。

如果采用这种结构，则可能会将各模块作为存储库的根目录进行处理，
无法检测变更后的文件或在 Annotate 中执行命令。
为了解决这种困扰的状况，按照以下步骤设定储存库的根目录。

1.  `Settings`-打开>`Version Control`
2.  `Directory`栏将`<Project>`的`VCS`栏设为`<none>`
3.  用`+`按钮在`Directory`栏中添加存储库的根目录，在`VCS`栏中设定`Git`或`Mercurial`的使用方

通过以上设定，VCS 的功能可以正常工作。