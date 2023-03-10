# 在 GitHub 页面中使用 Cobalt

> 原文：<https://dev.to/booyaa/using-cobalt-with-github-pages-2lke>

事实证明，使用[钴](https://github.com/cobalt-org/cobalt.rs)和你的个人 GitHub 页面设置起来有点棘手。与你的回购 GitHub 页面相反，你的个人 GitHub 页面必须包含`master`分支中的内容。存储库/项目 GitHub 页面可以位于默认分支的子目录中，即`docs`

这不是对 Cobalt 的批评，而是我自己展示了我缺乏 git 能力。

这个 how to 的一般要点是，将您的 Cobalt 项目放在`source`分支中，然后使用`cobalt import --branch master`将呈现的内容传输到 master。

## 指令

*   创建您的用户/个人页面报告(USERNAME.github.io)
*   遵循初始化回购的说明

```
git checkout -b source cobalt init
# do cobalty stuff..
cobalt build
# commit, maybe even push to source
cobalt import --branch master
git checkout master
# commit, definitely push 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看看我的设置，你可以在这里找到它。

## 我还没解决的事情

*   在`cobalt import`期间，`build`文件夹被复制到`master`分支。该文件夹不包含任何文件。我目前的工作是将它添加到`master`分公司的`.gitignore`。

## 感谢...

*   [Johann Hofmann](http://johannh.me) 允许我复制他博客的风格(和他的 travis-ci 自动部署设置)。
*   [钴](https://github.com/cobalt-org/cobalt.rs)用于创建这种易于使用的静态站点生成器。