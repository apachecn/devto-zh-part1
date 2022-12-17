# 全局 Git 忽略

> 原文：<https://dev.to/grahamcox82/global-git-ignores>

这是一个我认为非常有价值的特性，但是我不记得在我开始使用它的时候看到过大量的文档。(原来它在官方文档中，但只是很小的一部分)

Git 有能力配置一组它应该忽略的文件。您可以在三个不同的级别上做到这一点:

*   本地工作副本
*   贮藏室ˌ仓库
*   本地工作站

第一个是通过在签出中编辑`.git/info/excludes`文件来完成的。这允许您在本地工作区中拥有您想要忽略的某些文件，但是您不想将这种忽略提交给每个人的存储库。可能是只有您使用的 IDE 的 IDE 项目文件，也可能是您有保留的习惯。org 文件。这其实并不重要。

第二种方法是在您签出的任何地方编辑`.gitignore`文件，然后提交它们。这给出了适用于整个存储库的规则，而不仅仅是您的签出。举例来说，也许是建造人工制品。

第三个不太明显，但本质上与第一个相同，只是适用于您机器上的每个库。这通过首先执行:
来完成

```
$ git config --global core.excludesfile ~/.gitignore 
```

Enter fullscreen mode Exit fullscreen mode

然后编辑`~/.gitignore`文件。我的看起来像:

```
.DS_Store
.*.swp
*.iml
.idea 
```

Enter fullscreen mode Exit fullscreen mode

不包括 Mac。DS_Store 文件、Vim 交换文件和 IntelliJ 项目文件。

这样做的一个缺点是，我不知道如何在更局部的范围内忽略一些东西，尽管到目前为止这还不是一个问题。