# Webpack 2 配置的艺术

> 原文：<https://dev.to/alflennik/the-fine-art-of-the-webpack-2-config>

#### ***[喜欢这条](https://blog.flennik.com/the-fine-art-of-the-webpack-2-config-dc4d19d7f172)？查看[blog.flennik.com](https://blog.flennik.com)了解更多类似的内容！*T9】**

在不断变化的 webdev 世界中，怀疑主义和保守主义将保护你免受时尚、平台跳跃综合症、无休止的重构和被抛弃的、不受支持的工具所困扰。Next Big Thing 调用僵尸开发人员盲目地将他们腐烂的拳头砸向他们最新型号的 MacBook Pros，绝望地从 framework1 迁移到 framework2，再到这个，再到那个。不评价是否有必要。不尊重他们现有工具的能力，或者每六个月从零开始重建一切的痛苦，愚蠢地追求酷。

我们热爱现有的解决方案。我们*喜欢*大口大口。我们理解。它帮了我们大忙。我们在这上面投入了很多时间，我们把它放在我们的简历上。我们知道，在未来的许多年里，它在某些方面比任何其他选择都做得好。

当然，最终，带着巨大的痛苦，有时我们不得不承认炒作是有根据的。也许这项闪亮的新技术真的改变了一些事情。

以下是我与 Webpack 关系的时间表:

*   **第 1-50 天:**完全无知，除了名字:*“听起来很蠢，我不喜欢。”*
*   **第 50-100 天:**社区中有更多积极的议论:*“哇，人们不会停止谈论这件事的...每个人都一定是愚蠢的。”*
*   **第 101 天:**我想我至少会看一眼:*“这不是更复杂吗，你从所有这些配置中得到了什么？”*
*   第 102 天:我的天，这比吞咽简单多了。
*   **第 103 天:**我爱 Webpack！

Webpack 的天才之处在于它同时做两件事。首先，它捆绑。你把`import`写在你的 js 里，现在你需要一个捆绑器。它也做 css，很好。你减少了 HTTP 请求。你的文件很好地组合在一起，就是这样。

但是宝贝，这仅仅是开始。

Webpack 的秘密武器是**,它使 babel、typescript、sass、less、jade 和其他所有编译文件类型像普通 js 一样易于使用。**您已经在使用 Webpack 捆绑您的 JS，但现在您可以免费获得更多。以前，如果你想添加 Jade、PostCSS 模块、sass、两个重叠的 JS 框架、一个开发服务器和一个定制的构建过程，那么从你开始工作的那一天起，管理这种复杂性就成了一项全职工作。现在，您为您的框架提供一个加载器和插件列表，就大功告成了。复杂性完全消失了，整整 80 行配置就完成了。就麻烦而言，你还不如使用 jQuery，也就是说，它非常非常简单。

## 想看看这个动作吗？

您想看看实际完成的代码吗，这样您就可以跟随文章了？在 Github 上查看 [Au7](https://github.com/alflennik/au7) 的 [webpack 配置](https://github.com/alflennik/au7/blob/master/webpack.config.js)，Github 是我构建的 Aurelia、Framework7 和 webpack 的变种组合，旨在为 webdev 提供简单和强大的独特组合。

## 啊，升级了

之前，Webpack 有两大问题。首先，API 有点奇怪。他们的感叹号语法(顺便说一下，第一项是最后评估的)和问号语法会立即引起新来者的困惑和敌意。

```
css?modules-true!postcss-loader!sass 
```

Enter fullscreen mode Exit fullscreen mode

这不是干净的代码，对不起。

虽然 Webpack 提供了一些令人难以置信的好处，但它会让你想起杰出的 NASA 工程师建造巨大的火箭，计算轨道，控制燃烧和重力弹弓...但是把浴室放在离生活区 60 米的地方。

当然，另一个问题是文档，同时
管理得太高级*和*太基本而没有帮助。

这两个问题都被 Webpack 2 彻底粉碎了。

新网站显然是在考虑这些批评的基础上建立的，因为它仔细而系统地向您介绍了使用该工具所必需的好处、概念、陷阱和选项。它很全面，跨越了太基础和不够高级之间的界限，并提供了一个可用的搜索功能。

该 API 虽然大部分仍然是向后兼容的，但提供了更合理的命名约定，符合已建立的实践。您得到的不是感叹号，而是这个:

```
use: [
    { loader: 'style-loader' },
    { loader: 'css-loader' }
] 
```

Enter fullscreen mode Exit fullscreen mode

除了问号语法，每个加载器都接受自己的 options 对象和键值对。非常干净，非常有力。它仍然最后评估第一项，但我可以接受。

将这与完全合理的语法变化结合起来，比如将单词加载器改为规则，并要求 css-loader 中的单词加载器完全写出以保持一致性，这样就有了一个更加用户友好的界面。Webpack 正迅速变得难以立即批评。

那么，我们如何在保持如此重要的简单性的同时释放 Webpack 的全部功能呢？

## 告别段落-长命令

如果您和我一样，您在某个地方有一个文本文档，其中列出了您的项目的多个段落长的命令，您可以将这些命令复制并粘贴到终端中，以使它执行一些操作。或者更好，也许你拿着向上的箭头，试图找到你上周使用的那个命令，费力地穿过`git status`、`git status`、`git log`、`cd`、`ls`、`cd`、`ls`，一个无休止的命令游行，它在哪里？！

Webpack 也可能是这样，甚至通过一个简单的设置，你也可以得到类似于
的东西

```
NODE_ENV=development  webpack-dev-server  -d  --config  webpack.dev.config.js  --progress  --open 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是相当可读的，但没有人愿意一天写二十次。

启蒙在这里，它的名字叫 npm 脚本。将这些乱七八糟的东西放到 package.json 中，现在您只需要键入`npm run dev`，整个大命令就会神奇地执行。好处不仅仅是节省了击键次数。

通过在命令上加一个名字，你暗示了目的。这样别人就容易理解了。不需要火箭科学家就能弄清楚`npm run build:ios`和`npm run build:web`的区别。这很好。即使你有能力破译一个复杂的命令，你真的想吗？[懒惰。懒是好的。](https://blog.codinghorror.com/how-to-be-lazy-dumb-and-successful/)

其次，它加强了一致性。有一些官方认可的公开命令直接绑定到你的应用程序上。没有人需要问你如何运行你的项目，或者争论使用什么样的论点。

最后，更简单。简单的代码易于阅读，易于文档化，并且使用起来很有趣。

在其他新闻中，我现在输入`npm run dev`的速度比我自己的名字还快。我想这意味着我正在变成一个代码。

## 你需要一个文件

好吧。默认建议的使用[单独配置](https://github.com/gaearon/library-boilerplate/issues/2)的方法不会削减它。

这个想法是，您有两个文件，webpack.dev.js 和 webpack.prod.js，然后您从一个文件复制并粘贴配置到另一个文件，并进行一两处更改...等等。我刚才说复制粘贴了吗？糟糕的开发者！糟糕的开发者！

通常，当您包括 webpack.common.js 时，webpack 配置实际上被分为三个文件，这是由 webpack.dev.js 和 webpack.prod.js 导入的。您的大多数配置数据将是通用的，然后 dev 和 prod 文件将导入数据，进行一些必要的更改并导出配置。

如果你聪明的话，这是一个很好的解决方案。如果你有复杂的配置。但是我的配置很简单。另外，我很笨。

老实说，我没有聪明到在心里把代码流分成三个文件...当开发和生产之间的区别是一个插件时，您最终会得到样板文件(在两个文件中重复),并且您的数据已经以一种不再匹配 Webpack 文档的方式被抽象。相当大的牺牲。

当本文第一次上线时，我推荐了一种使用命令 cross-env 以跨平台方式设置环境变量的解决方案，并且我在推特上感谢了该库的创建者 Kent C. Dodds，他很快高兴地告诉我，他的库尽管有各种用途，但不再是这里的最佳选择。这让我有点窃笑，这个信息的来源是库的创建者本人，但他是对的，我自己也证实了这一点。有更好的方法。

不，`NODE_ENV=production webpack`，由于它的简单性和所有跨平台问题以及在 Windows 上的失败，不再被推荐，以前的解决方法`cross-env NODE_ENV=production webpack`也是如此。Webpack 2 几乎没有大张旗鼓，也没有什么文档，它解决了在 Webpack 配置中注入环境变量的问题，而且这个解决方案比绝大多数教程建议的要简单得多。

命令行选项`--env`允许你控制尽可能多的变量，比如`--env.production`或`--env.platform=web`，这些变量可以通过 webpack config 访问。您输入一个简单的检查，比如:

```
const isProduction = env.production === true 
```

Enter fullscreen mode Exit fullscreen mode

现在您有了一个方便的`isProduction`布尔值，可以在您的配置中使用。

但是，要使它工作，您必须对您的设置做一个更改。典型的 Webpack 配置是这样导出配置对象的:`module.exports = { objectGoesHere }`。然而，env 变量需要由 Webpack 传递到您的文件中。所以你必须把`module.exports`变成一个函数。看起来是这样的:

```
module.exports = function (env) {
  return { objectGoesHere }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们可以做两个大的改变来进一步改善这一点。首先，我们需要为`env`提供一个默认值，因为如果命令行没有设置环境变量，那么每当我们试图使用`env.production`或任何类似的键时，都会导致错误。第二，我们可以使用一个光滑的箭头功能，因为我们牢牢地在 ES6 的土地上。下面是它的样子:

```
module.exports = (env = {}) => {
  // Use your env variables here
  return { objectGoesHere }
} 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，由于关于这个特性的文档很少，您可以这样设置您的变量:`--env.production`不带等号将 env.production 设置为 true。`--env.platform=web`将`env.platform`设置为`web`，好了，你明白了。

如此简单，如此简短，我们只剩下这样的命令:

```
webpack  -p  --env.production  --env.platform=web  --progress 
```

Enter fullscreen mode Exit fullscreen mode

很容易看出，与相同命令的前一版本相比，这个 API 是多么简单:

```
cross-env  NODE_ENV=production  PLATFORM=web  webpack  -p  --progress. 
```

Enter fullscreen mode Exit fullscreen mode

人们抱怨网络的变化性？太疯狂了。看看事情是如何改善的吧！

其中一些功能内置在 webpack-config-utils 中——对于一个更复杂的设置来说，这是一条可行之路。

多亏了 Sean T. Larkin，报道这个 Webpack 2 的特性现在成了首要任务。为胜利而开源！

## 把代码放进东西里

既然您的代码已经被方便地缩小到一个文件中，那么您需要将条件数据引入到您的`module.exports`中。一些教程在文件的开头创建插件数组，将它们放入一个名为`getPlugins()`的函数中，然后在配置文件的插件部分调用这个函数。这是可行的，但是还有一个更好的方法。

那就是自执行代码块。

顺便说一句，如果你想让你的女朋友对你翻白眼，试着告诉她自执行代码块有多酷。

您可能知道它们是大多数 js 库的前几个字符，即包装代码的`(function(){})()`,将变量排除在全局范围之外。这是一个后跟`()`的函数，导致它执行。这是一个大多数开发人员都会理解的已知模式，由于该函数没有命名，所以不能在其他地方调用。你知道这段代码在这里执行，而且只在这里执行。这是一个结合了 return 的工具，我们可以在管理 js 对象时使用它来有条件地输出数据。一个例子:

```
{
  testVar: (function(){
     return "success"
  })()
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，`testVar`被设置为`success`。现在我们在一个函数中，我们可以使用 if 语句，循环，数组连接，语言的所有工具，然后返回我们需要的数据。

对于其他人来说，函数需要像`(functionGoesHere)`一样包装在`()`中才能自执行工作，这是不是很奇怪？[感谢丹·韦尔曼，他在推特上解释了原因！](https://twitter.com/danwellman/status/828689444379168769)

接下来是一个 Webpack 示例，由于 Node 的 ES6 支持，我们可以通过使用 ES6 语法——完全删除单词`function`的`(() => {})()`箭头函数——来使它更加清晰。

假设我们想要为生产启用隐藏的源地图。

```
devtool: (() => {
  if (isProduction) return 'hidden-source-map'
  else return 'cheap-module-eval-source-map'
})() 
```

Enter fullscreen mode Exit fullscreen mode

我是说，这很干净。你可以读它，但不清楚它是干什么的。这就是我们想要的配置。非常简单。

相比之下，`getPlugins()`解决方案将插件列表移到配置文件的头部。这很好，但是如果你是代码库的新手，并且正在检查哪些插件在使用中，你会先去哪里找呢？您将检查插件部分并找到一个函数调用，而不是您想要的数据。是的，您将看到`plugins: getPlugins()`，并且知道您可以使用 find 和函数名来查看插件，但是这是您不需要采取的多一个步骤。如果你改变了函数的名字呢？你确定这个函数没有在其他地方被调用吗？我是说，这似乎不太可能，但你确定吗？不，这条路更好。

## Webpack Blocks -禁止做什么

对 Webpack 的批评是有道理的，因为有足够多的样板文件，以至于你最终要从一个项目到另一个项目复制和粘贴整个配置。我希望有一种更简单的方法来告诉 Webpack 用它们各自的默认加载器加载所有的文件类型。这将省去十几个项目中 40 行复制和粘贴的麻烦，如果我发现我忘记在文件加载器规则中包含 woff 而不得不返回并更新所有这些代码，那就糟了。Webpack 对 css 闪烁其词，假装不知道它是什么，做什么。拜托，网络包，我们都知道你没那么无辜。我知道你去过那里。我知道你已经做到了。

然而，解决方案当然不是 Webpack 块或 easy_webpack，它们用 10 到 15 个 npm 包代替了您的 80 行配置(和您的细粒度控制),这些 NPM 包神奇地组合在一起生成您的配置。

尽管 Webpack 的样板文件可能存在问题，但这些解决方案剥夺了您的控制权和灵活性。

想象一下，如果你安装了你的包并运行了你的命令，却看到了大量的错误！在终点站。也许你遇到了一些不兼容或者错误地组合了本该是独占的包。你要花多长时间才能弄明白？或者，更有可能的是，假设你按下 go，一切正常，除了你的 sass 没有出现在你的包中。当您无法直接访问您的配置时，您可以采取什么措施来解决这个问题？

哇，想到这个我真的吓到自己了。在这里出冷汗。

是的，API 可能更好，在真空中。但是 Webpack 现在有一套优秀的文档。而且 Github 和 Stack Overflow 上有上百个指南和上千个问答。而且这种知识可以在不同的工作之间轻松转移。

此外，你几乎总是希望以一种新颖的方式来设置你的项目的一部分，这是以前的作者无法预料或允许的。是的，有了这些工具，这是可能的。但是现在你必须学习 Webpack APIs，并为自己编写一个专用的配置。所以现在你要学两个 API 而不是一个。

然而，最糟糕的是，这些解决方案并不能解决你的问题。你仍然需要复制大量的样板文件。代码刚刚从你的 Webpack 配置移到了你的 package.json 中。你知道你会怎么做。您将复制并粘贴它。

最终，我们喜欢追求完美，但有时我们不得不满足于近乎完美。也许解决这 40 条线的方法就是简单地学会与它们共存。

## 你怎么看？

建造 Au7 和 T2 并与社区分享它是一次美妙的经历。将这些框架放在一起是如此的容易和简单，光是想想就让我兴奋。对于 web 开发来说，这是一个激动人心的时代，Webpack 可能是催化剂。它提供的简单性使得以前不可能的项目成为可能。毕竟，作为工程师，我们处理的主要问题是复杂性。复杂性决定了我们可以在不引入一连串错误的情况下增加什么功能。它控制着我们工作的速度，因此也控制着我们项目的预算。它决定了我们的项目是否需要一周的工作...或者一个 50 人的团队工作四年。

在推特上联系我，我的账号是@alflennik。

如果你对跨平台开发感兴趣，对在每个平台上重用相同代码感兴趣，请查看 [Au7](https://github.com/alflennik/au7) 。

黑客快乐。