# 我们如何减少我们的余烬建造时间？

> 原文：<https://dev.to/gokatz/how-we-cut-down-our-ember-build-time-ehh>

旅程开始于大约 4 个月前，当时我的 ember 构建只花了 2 分钟(最坏的情况下)来重建(增量重建)。你知道这真的很痛苦😰当您需要等待 2 分钟才能在浏览器中获得更改时。所以我决定深入到构建过程中，以了解哪个构建任务花了很长时间才完成。其实还有很多！其中一些是:

*   连接应用程序和测试文件的源映射
*   合并树和
*   很少自定义构建任务。

当检查数量时，一项任务几乎花费了总构建时间的一半😱那就是连接测试文件。敲门的第一个问题是

`"Why the test files need to be concatenated during my development, where I know I will not touch a single test file at all?" 🤔`

是的，答案是**“不需要！”**。我非常放松，现在可以减少一半的构建时间😌。但是，怎么做呢？Zoooob！走向社区，这条线帮助了我。如前所述，您可以通过向您的构建文件(ember-cli-build.js)添加一个选项`tests: false`来消除测试文件操作，这样一个示例构建文件将看起来像

```
let EmberApp = require('ember-cli/lib/broccoli/ember-app');

module.exports = function(defaults) {

  let buildOptions = {};

  if ( process.env.EMBER_ENV === 'development') {
    // only during development mode
    buildOptions.tests = false;
  }

  let app = new EmberApp(defaults, buildOptions);

  return app.toTree();
}; 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我们把我们的建筑砍掉了将近一半，但在最坏的情况下也就一分钟。但比之前的好得多。日子过得不错，在上周的另一个晴朗的日子里，我意外地注意到连接应用程序文件的源地图的任务占用了总构建时间的一半。又是手边的工具。实际上，我对这些数字并不感到惊讶，因为我们的代码库很大，连接这些文件会比平时花费更多的时间。只是为了看一看，我检查了我们应用程序的源地图文件，我再次震惊地发现，大约 15%的文件来自我们存放模拟数据文件的 mirage 文件夹。同样的问题，

`why those mirage files have to be included in the build when the test files itself eliminated (in the previous process)? 🤔🤔`

幸运的是，答案又是**“是的，不需要包括”！**再次减少 15%的`sourceMapConcat`时间。太好了，但是如何排除那些文件。幸运的是，`ember-cli-mirage`可以通过一个配置选项(`config/environment.js`)显式地排除这些文件。我们需要将标志`excludeFilesFromBuild`设置为 true，以便开发环境从构建中排除那些文件(因为键本身是不言自明的)。因此，您的配置应该是这样的，

```
 if (environment === 'development') {
    ENV['ember-cli-mirage'] = {
      excludeFilesFromBuild: true
    };
  } 
```

Enter fullscreen mode Exit fullscreen mode

还没完；)是的，如果你们使用引擎来延迟加载资产，你们也会得到一些奖励。从字面上看，发动机被定义为

`Engines allow multiple logical applications to be composed together into a single application from the user’s perspective.`

由于我们的代码库很大，我们需要模块化和惰性加载资产。为此，我们更喜欢回购引擎。关于这一点，你应该已经猜到了！聪明！这是提问时间，

`why we have to worry about the engines file (isolated application) if we are not gonna touch the engine related modules at all? 🤔🤔🤔`

这个问题的答案取决于产品/应用的使用案例和性质。在我的例子中，我可以自由地将这些文件从源-映射连接过程中排除，因为我的引擎几乎与应用程序的其余部分隔离开来，而应用程序的其余部分是用它们自己的范围开发的。如何排除引擎文件？

**哈...首先，为什么引擎文件(实际上是一个插件)包含在源地图中？**

*因为我们在引擎的`index.js`文件中提到它是一个开发插件，以同时/即时在应用程序中反映出开发目的的变化。*

是的，你找到了答案，如果你在开发过程中不改变引擎文件，就把这些引擎作为一个开发插件😉如下:

```
let EngineAddon = require('ember-engines/lib/engine-addon');
module.exports = EngineAddon.extend({
  name: 'chatbox',
  isDevelopingAddon() {
    return false;
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就是今天的快讯😊还有很长的路要走😉在以后的帖子里和大家见面。关于引擎，我们应该提供一些配置设置，帮助我们在运行时操作引擎配置(包括 in-repo 和独立引擎)。欢迎就此发表评论。顺便说一句，由于整篇博文都有点倾向于测试和测试用例，我必须告诉你们所有人，我喜欢测试用例，因为它和开发过程处于同一水平😇。