# 从 Less 迁移到 Sass——真的是个好主意吗？

> 原文：<https://dev.to/igorsantos07/migrating-from-less-to-sassreally-a-good-idea>

我使用 Laravel 启动了我的[新项目](http://konato.igorsantos.com.br)(稍后将详细介绍)，由于它预打包了一个名为 [Elixir](http://laravel.com/docs/5.1/elixir) ( *facepalm:他们错误地将其命名为 [Erlang platform](https://en.wikipedia.org/wiki/Elixir_%28programming_language%29)* )的 Gulp 助手，我决定将原来的主题 CSS 文件迁移到更少的文件中。

你问，为什么[少了](http://lesscss.org/)？我也不知道。在我的上一个项目( [ProtoBoard](https://github.com/igorsantos07/protoboard) ，现在是一个被遗弃的宠物)中，我使用了[手写笔](https://learnboost.github.io/stylus/)，尽管我也不记得原因了——可能是为了使用 Sass 的一些语法(没有分号),而不用担心遭到反对或 Sass 的古怪，或者可能是因为我认为我仍然需要 Ruby 来编译东西。在那之前，在我以前的 Ruby 生活中，我曾经使用过 [Sass](http://sass-lang.com/) ，但是当时除了已经废弃的 TurbineCSS(一个我非常喜欢的项目，当最初的维护者放弃它时，我采用了 docs 站点很多年)之外没有其他选择。

现在，在对 Sass 进行了几次强有力的推荐之后，事实上我的样式表在我 5 年前的机器上编译花费了巨大的~ 7 秒( *Sass 有 C 编译器，所以它一定很快，对吧？我决定迁移到 Sass。在这个时候，我仍然没有大量使用较少的功能，我只是从原始主题缩进了一些规则，在这里和那里设置颜色变量，并添加一个 mixin，所以我认为这不会是一个痛苦的过程。*

事实上，它不是。此外，如前所述，一些萨斯的怪癖。出于以下原因，我可能会坚持少用一些:

*   我使用了几个颜色函数，比如 Less 的`darken()`、`lighten()`和`fade()`。在 Less world 中，它们接收一种颜色(十六进制、HSL 或名称)和一个数字，可以是十进制或百分比。然而，在 Sass 世界中，亮度函数使用百分比，而不透明度函数(淡入()/淡出())使用 0 到 1 之间的小数；百分比和小数不能互换。*这里的标准在哪里？我一个也没看见。*萨斯，你能决定你要用什么类型的百分比数字吗？thnx*T6】*

> [![code screenshot comparing Sass to Less](img/49bbeefb191174d723019845f0b7ecb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cU09P2H3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ACNvLqBMJgKAaWJQlgLaiRA.jpeg)
> *Mixins:Less vs Sass，使用颜色名称作为参数。如果在 Sass 中使用 darkeng(goldenrod，20%)，它会工作得很好，但是通过使用 golden rod 作为 mixin 的参数并将颜色名称传递给`darken()`，它会出现一个关于“颜色必须是颜色”的编译错误。去想想。*

*   虽然像 darken()这样的函数在两种语言中都可以很好地处理颜色名称，但是如果您将颜色名称传递给 Sass mixin 并在函数中使用它，它会对您大喊大叫。那又怎样，萨斯？有些论点在传递的时候会发生内部变化？WTF
*   在我看来，使用 Sass 来编译我的 CSS 给了*100 毫秒的优势(这个过程大约需要 7 秒)。然而，在我的机器上，在分支之间运行一个简单的基准测试(如下)实际上速度更快了。无论如何，速度差异肯定是无关紧要的。*
*   Sass 实际上做了一个更大的 CSS 文件。然而，由于我也运行一些后处理器(即 Autoprefixer 和 minifier)，这种差异在生产中也是无关紧要的。下面的基准测试也强调了大小差异。
*   我觉得 Sass(在它大多数被接受的 SCSS 语法中)有点冗长，因为必须使用`@mixin`和`@include`来声明 mixins，而不是透明地将它们作为类来使用。不过，这当然是个人喜好的问题。另一方面，少插值感觉怪怪的——变量:`@var`；插值:`@{var}`(？！).
*   当您包含另一个文件时，Sass 更加简单； [Less 允许你](http://lesscss.org/features/#import-options)简单地引用文件而不实际输出到最终文件中，或者强制解释为 Less/CSS。这有时很方便，可能会使 CSS 文件更精简。

最后，我发现使用 Sass 比使用 Less 真的没有优势。这两种语言似乎有几乎相同的特性，尽管 Sass 似乎更具可编程性(并不是说我把这看作一种优势；那东西从根本上来说还是一种造型语言，对吧？).两种语言在速度和文件大小上产生相似的结果(如果缩小；但是你总是要把你的文件编译成产品，对吗？).因此，我坚持使用 Less，因为它有一个更大的前端社区(我认为),并且不会在百分比和(你可以在这里判断我)颜色名称上和我开玩笑。

如果使用 Sass 还有其他好处，你能告诉我吗？我什么也没找到...除了用相同的前缀写变量之外，我的项目语言(PHP)使用 lol

* * *

### 平台详情

上面的测试是在一台 5 年前的机器上运行的，一台 8GB 内存的高端 Core2Duo(我的大多数开发应用程序都是打开的)。代码可以在这里看到:

*   Sass 分支:[源文件](https://bitbucket.org/konato/web/src/34a007329228ff8833091c284c48d2bdcf0603ea/resources/assets/sass/?at=sass)，[编译 CSS](https://bitbucket.org/konato/web/src/34a007329228ff8833091c284c48d2bdcf0603ea/public/css?at=sass) ， [gulpfile](https://bitbucket.org/konato/web/src/34a007329228ff8833091c284c48d2bdcf0603ea/gulpfile.js?at=sass&fileviewer=file-view-default)
*   主分支(少):[源文件](https://bitbucket.org/konato/web/src/685c92fc520cc40755d7c6360793264349c87bfb/resources/assets/less/?at=master)、[编译 CSS](https://bitbucket.org/konato/web/src/685c92fc520cc40755d7c6360793264349c87bfb/public/css?at=master) 、 [gulpfile](https://bitbucket.org/konato/web/src/685c92fc520cc40755d7c6360793264349c87bfb/gulpfile.js?at=master&fileviewer=file-view-default)

是的，我知道，这些源文件看起来很乱。它们是我购买的一个主题的原创，已经有一个问题提醒我将它们合并在一起。

* * *

下面是使用`gulp`和`gulp --production`的基准测试结果。大小表示最终的 CSS 文件大小，粗体显示的时间是 5 次编译运行之间的平均值(每个值如下所示)。

gulpfiles 可以在之前突出显示的分支中看到，以及由此产生的开发 CSS 文件。

> 少(制作)-108.007 kb ~ 6.906s
> 6.78s | 7.21s | 6.75s | 6.96s | 6.83s
> 
> less(dev)-149.122 kb ~ 5.366s
> 5.07s | 5.41s | 5.35s | 5.55s | 5.45s
> 
> 萨斯(量产)-108.110 kb ~ 7.44s
> 7.21s | 7.73s | 7.77s | 7.39s | 7.1s
> 
> sass(dev)-151.576 kb ~ 5.494s
> 5.73s | 5.27s | 5.24s | 5.67s | 5.56s