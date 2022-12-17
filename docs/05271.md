# 设计 SparkPost:为什么我们选择使用 CSS 模块

> 原文：<https://dev.to/sparkpost/styling-sparkpost-why-we-chose-to-use-css-modules>

### 造型小柱

在一个由 50，000 行代码组成的应用程序中设计东西是非常可怕的。当你知道你触摸的任何东西都可能会损坏其他东西时，就很难有信心修改或删除现有的 CSS。这导致我们的团队大部分时间采用更安全的方法，用更多的 CSS 类和更多的特性(有时！重要)。随着时间的推移，它会变得臃肿不堪，充满污垢，难以维护。

[![css gif css modules](img/d98ef0e1b43a3ba02d4572fb1ba9253b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AGHVKMmc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.sparkpost.com/uploads/2017/08/css-gif.gif)

由于我们现在正在迁移我们的应用程序以作出反应，我们有独一无二的机会从零开始完全重组和重建我们的风格。有了[所以](https://github.com/paypal/glamorous) [很多](https://github.com/styled-components/styled-components) [不同](https://github.com/tkh44/emotion) [方式](https://github.com/cssinjs/jss) [到](https://github.com/Khan/aphrodite) [风格](https://github.com/rofrischmann/fela) [反应](https://github.com/smyte/jsxstyle) [组件](https://github.com/MicheleBertoli/css-in-js)在 2017 年，为我们的新设计系统找到最佳解决方案相当困难，这个系统叫做火柴盒。

我们决定我们建造的东西应该是:

*   快速建造
*   组件范围
*   可重用(即使没有 React 或 JavaScript)

我们决定使用 [CSS 模块](https://github.com/css-modules/css-modules)，原因如下:

### 萨斯

虽然 CSS-in-JS 似乎非常流行，尤其是在 React 社区中，但我们决定使用允许我们使用常规 [Sass](http://sass-lang.com/) 的东西。我们还没有准备好抛弃 CSS，把自己锁在一个完整的 JS 解决方案中，这个解决方案似乎缺乏 Sass 提供的成熟度。例如媒体查询、关键帧、伪元素、编辑器支持。

我们很擅长斗嘴。我们知道我们会很舒服地使用它，我们知道我们会很快使用它。不必花时间重新培训我们自己或未来加入我们团队的任何人是一个额外的好处。

但是 Sass 本身并没有提供我们想要的模块性——这就是 CSS 模块的用武之地。

### 无全局范围

虽然像 [BEM](http://getbem.com/) 、 [SMACSS](https://smacss.com/) 和 [ITCSS](https://www.xfive.co/blog/itcss-scalable-maintainable-css-architecture/) 这样的方法在封装样式和控制 CSS 的全球级联性质方面非常有效，但我们想更进一步。我们希望我们的开发人员能够编写 CSS，而不必学习或遵守严格的命名约定。

就其核心而言，CSS 模块实际上是一种自动化 BEM 的非常简洁的方式。它生成的类名对我们来说是唯一的，并且是本地化的，所以我们再也不需要考虑用什么来命名我们的类了。

例如，`<TextField />` React 组件中使用的 CSS 类`.Input {}`编译成如下所示的类名:`.TextField __Input___ 1Vvpx`，并附加了一个唯一的散列以确保其样式保持本地。

### 复用性

如果你读过我写的关于我们如何设计入职体验的帖子，你可能会记得我们重视设计的一致性。有了 CSS 模块，我们可以在其他地方使用我们的 Sass 函数、mixins 和组件样式，即使没有 React 或 JS 依赖。我们希望使用我们在不同项目中构建的任何东西来帮助确保我们品牌的设计语言是一致的。

不涉及太多细节，我们通过向 CSS 模块的配置传递一个定制的 [generateScopedName](https://github.com/gajus/babel-plugin-react-css-modules#configuration) 函数来实现，该函数将在编译时[自动生成我们的类名](https://github.com/SparkPost/matchbox/blob/master/config/rollup/scopeClasses.js)。

我们的类名编译自:
，而不是默认的模板:`[name]___[local]___[hash:base64:5]`

```
.TextField {}
.error .Input {}
.Input {} 
```

Enter fullscreen mode Exit fullscreen mode

变成类似…
的东西

```
.TextField {}
.TextField--error .TextField__Input {}
.TextField__Input {} 
```

Enter fullscreen mode Exit fullscreen mode

相当整洁。如果没有这个惟一的散列，其他人可以使用普通`.css`文件中的样式。

### CSS 又牛了

老实说，我们又开始享受设计的乐趣了。构建组件而不用担心应用程序的另一端会出问题，这是每个前端开发人员都非常欣赏的。

如果你正在阅读这篇文章，寻找如何设计你的 React 应用的建议，考虑 CSS 模块的灵活性。虽然我们已经找到了一套适合我们的工具，但根据您的需求，其他解决方案可能值得考虑——例如，如果您正在使用 React native，请查看 [CSS-in-JS 工具](https://github.com/MicheleBertoli/css-in-js)。

### 火柴盒

我们正在开放地构建我们的 React 组件和样式，所以请随意[为你自己](https://github.com/SparkPost/matchbox)检查代码，甚至可能用它来设计你的下一个项目。目前，火柴盒只是一个组件库。在未来，我们希望看到火柴盒成为一个完整的设计系统，包括品牌和内容指南、组件和一个 UI 工具包。如果您对此有任何疑问，或者您想了解任何其他信息，请告诉我们！

*本帖最初发表于[sparkpost.com](https://www.sparkpost.com/blog/why-chose-css-modules/)T3】*