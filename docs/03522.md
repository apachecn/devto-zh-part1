# 使用 Travis 和 npm 自动部署

> 原文：<https://dev.to/jeffreymfarley/deploy-atomically-with-travis--npm-68b>

# 使用特拉维斯& npm 自动部署

我认为我是一名软件开发人员，因为我很懒。

第二次或第三次我不得不执行同样的任务时，我发现自己在说，“唉，我不能告诉计算机怎么做吗？“β

因此，想象一下当我们团队的部署过程看起来像这样时我的反应:

1.  `git pull`
2.  `npm run build`创建缩小的包
3.  `git commit -am "Create Distribution" && git push`
4.  导航到 GitHub
5.  创建新版本

我认为步骤 1-3 很容易放入 shell 脚本中，步骤 4-5 可能是可脚本化的，但这就是全部吗？还需要做什么？

*   `package.json`中的版本从未更新过，如果能与 GitHub 版本保持同步就好了。
*   这个脚本可以在 CI 构建之后运行，而不需要人工手动运行吗？

## GitHub 和β版本

我迈向完全自动化的第一步是挖掘 GitHub 版本的细节。首先，标签和发布不是一回事。

### 标签

标签是底层版本控制系统 Git T1 的一部分。它们有两种类型:

*   轻量级-指向现有提交的指针
*   带注释的——带有名称、时间戳和校验和散列的完整对象

但是，它们不包含任何文件或对存储库中代码的更改。

### 发布

[Releases 是标签](https://help.github.com/articles/about-releases/)的 GitHub 扩展。它们提供标签的所有功能，同时还提供:

*   当时代码库的压缩档案
*   任何相关的发布二进制文件(如`.jar`或`.dll`)

### 这符合我们的用例吗？

不是 100%。在我们的例子中，缩小的文件必须作为更大架构中 Django 插件的一部分存在于存储库中。我们无法真正利用 GitHub 版本，因为它们的文件存在于代码库之外。

还有，`package.json`版本好像有个鸡和蛋的问题。我在标记之前不知道版本号是多少，但是我不能用标记存储任何代码更改。

## npm 版来了——救援

node 的聪明人已经为我解决了这个问题:

```
npm version [major | minor | patch] -m "message" 
```

Enter fullscreen mode Exit fullscreen mode

运行[命令](https://docs.npmjs.com/cli/version)时，npm 将:

1.  读取当前的 git 标签
2.  根据凹凸类型(主要、次要或补丁)凹凸`package.json`中的版本
3.  运行`package.json`中指示的`version`脚本
4.  `git add`
5.  `git commit -m "message"`
6.  `git tag <new tag number>`

完美！如果我们在版本脚本中运行`npm run build`，缩小后的文件将和更新后的`package.json`一起打包。

## 太好了，我怎样才能让特拉维斯这么做呢？

我们使用 Travis 作为我们的持续集成服务器，但这一步可以很容易地移植到 CircleCI、AppVeyor 或许多其他 CI 服务。

### 默认用例是发布

如果您开始阅读 Travis 文档，很明显他们的主要用例是构建资产并将其推送到 GitHub 版本。既然我们已经确定那不适合我们，是时候开始试验和谷歌搜索了。

### 天真的第一步

在`package.json`中，我编写了版本脚本来构建缩小的资产:

```
"scripts": {
   ...
   "version": "npm run build && git add ."
   "postversion": "git push && git push --tags"
} 
```

Enter fullscreen mode Exit fullscreen mode

在`travis.yml`中，我在部署
之前让它调用了`npm version`

```
before_deploy:
- npm version patch
deploy:
  provider: releases
  skip_cleanup: true
  on:
    branch: master 
```

Enter fullscreen mode Exit fullscreen mode

然后在 Travis 中运行它:

```
> foo@0.6.2 postversion /home/travis/build/foo/bar
> git push && git push --tags
fatal: You are not currently on a branch.
To push the history leading to the current (detached HEAD)
state now, use
    git push origin HEAD:<name-of-remote-branch> 
```

Enter fullscreen mode Exit fullscreen mode

### 分离头模式

谷歌了一下，发现[特拉维斯总是以分离头部模式](https://github.com/travis-ci/travis-ci/issues/1701)运行。这是一种安全措施，但确实削弱了这种自动部署应该如何工作。

我可能很懒，但我喜欢挑战。

基本上，我们必须让 Travis 在`master`的提示处执行版本步骤，然后将其推送到我们的分支。等等，它怎么会有资格这么做呢？

### API 键

通常，Travis 对存储库执行只读操作。当它必须写回存储库时，它需要知道使用哪个帐户。我们可以在`travis.yml`文件中指定凭证，但是这样一来，在线查看该文件的每个人都会看到它。正确、安全地执行此操作需要以下(一次性)设置:

1.  在[https://github.com/settings/tokens](https://github.com/settings/tokens)创建新代币
2.  复制字符串
3.  在 Travis 中打开设置
4.  创建一个新的环境变量`GITHUB_API_KEY`
5.  粘贴值中的字符，并确保取消选中“在构建日志中显示值”

好了，凭证可以交换了，但是我们还没有解决分离头的问题。

## 编写 Travis 脚本

一些谷歌搜索结果( [1](https://gist.github.com/willprice/e07efd73fb7f13f917ea) ， [2](https://github.com/JemsFramework/di/blob/trunk/.travis.yml) )告诉我，编写一个由 Travis 执行的 shell 脚本是解决分离头问题的方法。

#### `.travis.yml`