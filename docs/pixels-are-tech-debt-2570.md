# 像素是技术债务

> 原文：<https://dev.to/ssalka/pixels-are-tech-debt-2570>

(本文原载于[媒体](https://medium.com/podible-engineering/pixels-are-tech-debt-ff4ff4fdeb4c))

如果你是一名软件工程师，那么像我一样，你会花很多时间阅读和编写代码。你读的代码越多，你开始观察到的模式就越多。你写的代码越多，你就越能理解为什么选择一种模式而不是另一种。但是有些模式比其他模式更好，而有些则糟糕透顶。

### 一个完全错误的简单设计

想象一下，给你一个新网站布局的设计，你需要拿出一个`PageLayout`组件来实现这个设计。根据规范，完成的页面在桌面上应该是这样的:

```
+--------------------------------------------------------+
| Logo                                          Settings |
+-------------------------------+------------------------+
|                               |       Page Title       |
|                               +---------+--------------+
|            Main               |   Nav   |     Page     |
|            Page               |  Links  |  Description |
|           Content             |   ...   |     ...      |
|              .                +---------+--------------+
|              .                |                        |
|              .                |         (empty)        |
|              .                |                        |
|                               +---------+              |
|                               |  Share  |              |
|                               | Buttons |              |
+-------------------------------+---------+--------------+ 
```

Enter fullscreen mode Exit fullscreen mode

好吧，布局有点奇怪，但乍一看，实现起来很容易:

*   在页面顶部放一个全角的`div`——左边浮动徽标，右边浮动用户设置
*   设置两个主栏——一个在左边(用于可滚动页面内容),一个在右边(用于...其他一切)
*   嗯（表示踌躇等）...网站标题、链接和描述的排列方式与整个页面相同！让我们重复使用我们的`PageLayout`组件——我们会让它保持干燥！！！
    *   边缘情况:我们只需要确保我们从不传递一个`PageLayout`实例作为页面描述，否则组件呈现可能会进入无限递归并破坏站点，很可能使用户很难关闭窗口。
*   唷，很高兴这一切都结束了...这些分享按钮呢？周围什么都没有？我们就加`position: absolute;`到此为止吧。

耶什...不像最初看起来那么简单。但是，在这上面花了几个小时，所以是时候将代码签入版本控制了！

### 不应被批准的拉取请求

让我们快速回顾一下到目前为止已经完成的工作:

上面的方法产生了想要的布局吗？是的，如果我们对 CSS 的组织方式做一些假设的话(提示:这样做通常是不安全的)。

对于子元素将在哪里呈现有任何模糊之处吗？不，除了分享按钮，但是如果需要的话，我们可以让消费者覆盖样式。

该组件展示了任何有用的抽象吗？好吧，外部布局被重用为右列的一部分，因此它使开发人员不必编写一些额外的`div`元素...

好吧，总体来说还可以更好，但是它展示了给定设计的[必要和充分条件](https://en.wikipedia.org/wiki/Necessity_and_sufficiency)——如果你问一个逻辑学家，这是某件事被认为是可接受的或正确的全部条件。

那么，有什么问题呢？

### 视角的突然转变改变了一切

一旦新页面投入生产，每个人都喜欢它。营销团队在您的部署后看到了大量数据，来自各种 SaaS 分析平台的初步读数表明，用户接受重新设计，并变得更加投入。

鉴于所有这些成功，设计团队决定是时候进入下一个阶段了。下一关是什么样子的？差不多，不过现在也在手机上:

```
+-----------------------------------------+
| Logo          Page Title       Settings |
+-----------------------------------------+
|                                         |
|                                         |
|                                         |
|                                         |
|                                         |
|                                         |
|                                         |
|                  Main                   |
|                  Page               +---+
|                 Content             | S |
|                    .                | h |
|                    .                | a |
|                    .                | r |
|                    .                | e |
|                                     +---+
|                                         |
|                                         |
|                                         |
+-----------------------------------------+
|                                         |
|             Page Description            |
|                                         |
+-----------------------------------------+
|             N a v   L i n k s           |
+-----------------------------------------+ 
```

Enter fullscreen mode Exit fullscreen mode

好的，看起来很干净...但是**没有什么**像最初的设计——导航移到了页脚，分享按钮在一个完全不同的地方，栏目不见了！！我该怎么把我刚才做的翻译成*那个*？😱

从这里，我看到了三条潜在的前进道路:

1.  添加一堆条件逻辑来重新定位元素
2.  有两个独立的实现-一个用于桌面，一个用于移动
3.  重新设计以更好地适应两种布局

选项 3 听起来工作量很大...让我们稍后回到那个。

选项 1 怎么样？将桌面布局重新排列成第二个可能会带来灾难，甚至可能会导致你最喜欢的组件的死亡。

大量的 CSS 规则和类名需要调整，几乎覆盖了页面上的每个子元素(可能除了包含徽标和设置菜单的标题行)。看起来我们可以保留共享按钮的`position: absolute`,但这没有多大意义。

用 Bootstrap 这样的 CSS 框架来实现你的布局？这类事情的全部意义在于使这些怪异的样式规则变得不必要，所以即使这样，仅仅通过破坏现有的布局来摆脱也是不合适的。

所以第一种选择不可行，那么第二种选择呢？当然，这在一开始会更容易，但是从长远来看，没有人会愿意支持同一件事情的两个完全不同的实现。当然，这在现实世界中确实经常发生，对于企业规模的团队来说，这通常是一个不错的选择。尽管如此，Podible 的整个团队都挤在我们纽约 WeWork 办公室的一个房间里，所以自然地，我们希望尽可能避免这种情况。

这让我们回到第三种选择。

[![XKCD: How Standards Proliferate](img/5f39a21c21785cf3c5daf89a88b1d761.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RJjtUme5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/standards.png)

### 再看一个老问题

让我们重新审视一下这两种布局——桌面和移动页面都有类似盒子的结构，但根据查看设备的类型，内容似乎位于完全不同的位置。我们可以用什么方法重做`PageLayout`组件来适应这两种安排？

*   独立的第三方无模板前期工作，但是当您需要进行隐藏在模板深处的调整时会发生什么呢？OSS 的贡献总是有价值的，但是如果项目没有得到很好的维护，你可能会掉进一个很深的兔子洞。
*   Bootstrap - Responsive，但是仍然需要 JS 的大量帮助
*   flexbox——变得越来越热，但是会产生一堆额外的 div 元素，它们只充当实际内容的容器
*   CSS 网格——现在似乎有很多关于 CSS 网格的讨论。让我们看看炒作到底是怎么回事！

在开始编写代码之前，先看一个简化的例子，看看如何用 CSS grid 实现一个简单的布局。我最喜欢的方法之一是使用 [`grid-template-areas`](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Grid_Template_Areas) 和`grid-area`——这些 CSS 规则允许你相对容易地创建相当古怪的布局:

```
.wacky-grid {
  display: grid;
  grid-template-areas:
    ".    .      area-1"
    ".    area-2 .     ";
}

.top-right {
  grid-area: area-1;
}

.bottom-center {
  grid-area: area-2;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以`.wacky-grid`容器将变成一个 2×3 的网格，有两个命名的网格区域`area-1`和`area-2`。[网格区域](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-area)是一个矩形的单元格区域，可以通过名称或行&列的起点/终点来指定。

CSS grid 的一个关键概念是*你可以把它的内容放在任何你想要的地方*。在一个`.wacky-grid`元素下，具有`top-right`类名的子元素将被放置在`area-1`网格区域，具有`bottom-center`类名的子元素将被放置在`area-2`网格区域。标有`.`的单元格将保持空白。至于空格，我只是为了可读性而添加的——规则可以等价地写成:

`grid-template-areas: ". . area-1" ". area-2 .";`

...但是这使得最终结果看起来不太清楚。😉

`WackyGrid`的实现可能看起来像这样:

```
const WackyGrid = () => (
  <div className="wacky-grid">
    <div className="top-right">
      I'm up in the corner!
    </div>
    <div className="bottom-center">
      I'm down in the middle!
    </div>
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

看，马，没有集装箱！😁每个孩子都被安置在相应的牢房里。额外的好处:如果你有额外的孩子没有任何特定于网格的 CSS 规则，他们将得到自动放置在剩余单元格中的。

注意，网格单元格不能直接样式化——只有实际的 HTML 元素可以。所以，如果你有一个想要涂成黑色的空单元格，你仍然需要一个 div 来放在那里。

好了，现在该进入正题了...

* * *

### 轻松分支布局

如果 CSS 网格可以教会你什么的话，那就是你可以停止过多地担心文档的 HTML 结构，开始更多地考虑如何以一种智能的方式安排更重要的数据。不需要每次想在列中包含一行时都添加包装元素。

⚠️ **免责声明** ⚠️
在你回到你的团队说，“这篇文章说我们应该对所有事情都使用 CSS 网格！！!"后退一步，想想什么时候你会更喜欢 CSS grid 而不是其他工具，比如 flexbox 或第三方库/框架。我发现这个形象很好地概括了事情:
[![Flexbox vs Grid](img/4065645be9a6354f9cc86ae6747eb0e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OH-SIjBf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-ap-southeast-2.amazonaws.com/tmp.inlight.com.au/assets/Article/flexbox-vs-grid.png)

撇开 PSA 不谈，让我们为初始布局指定一个网格:

**PageLayout.scss**

```
.page-layout {
  display: grid;
  grid-template-areas:
    "header  header  header"
    "main    title   title"
    "main    nav     description"
    "main    share   .";
} 
```

Enter fullscreen mode Exit fullscreen mode

不错！超级简单。但是我们如何区分桌面布局和移动布局呢？`grid-tempate-areas`规则非常适合描述这种微妙之处:

```
.page-layout {
  display: grid;
}

.page-layout-desktop {
  grid-template-areas:
    "header  header  header"
    "main    title   title"
    "main    nav     description"
    "main    share   .";
}

.page-layout-mobile {
  grid-template-areas:
    "header"
    "main"
    "description"
    "nav";
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这开始有意义了。尽管如此，还是有一些模糊的地方——我们可以非常清楚地看到，网格区域名称没有完全匹配。特别是移动布局中缺少了`share`网格区域！

(如果没有分享按钮，我可能会说在手机上使用 flexbox 会更容易。然而，为了这篇博文，让我们继续关注有趣的新东西🙃)

我们可以通过将元素和类名映射到网格区域来解决这些歧义:

```
// applies regardless of device type
.page-layout {
  .logo, .user-settings {
    grid-area: header;
  }

  .logo {
    justify-self: start;
  }

  .user-settings {
    justify-self: end;
  }

  nav {
    grid-area: nav;
  }

  main {
    grid-area: main;
  }

  .description {
    grid-area: description;
  }
}

// desktop-specific placement
.page-layout-desktop {
  .page-title {
    grid-area: title;
  }

  .share-buttons {
    grid-area: share;
    align-self: end;
  }
}

// mobile-specific placement
.page-layout-mobile {
  .page-title {
    grid-area: header;
    justify-self: center;
  }

  .share-buttons {
    grid-area: main;
    align-self: center;
    justify-self: right;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这一部分有点冗长——当然还有其他放置子元素的方式——但是这给了我一个很好的主意，每个元素将出现在页面的什么地方。但是还有最后一件事会极大地影响网格最终的形状:

```
.page-layout-desktop {
  grid-template-rows: 1fr 1fr 25vh auto;
  grid-template-columns: 5fr 2fr 3fr;
}

.page-layout-mobile {
  grid-template-rows: 1fr auto 2fr 1fr;
  // not necessary to specify columns, since there's only one
} 
```

Enter fullscreen mode Exit fullscreen mode

它所需要的只是一些好的行和列的尺寸——如果没有这些规则，3 个桌面行/列将以相同的高度/宽度呈现，移动主区域将只占屏幕的 1/4。

如果你对这个[神秘的`fr`单元](https://css-tricks.com/introduction-fr-css-unit/)感到疑惑，它是*分数*的简称，是 CSS 网格特有的。虽然你可以只使用传统的单位来实现你的网格布局，比如`%`、`em`或者`vh` / `vw`，但是我要说它可能是 CSS 网格最有用的单位，因为它有助于消除一些不必要的计算，比如`repeat(4, 1fr)`对`repeat(4, 25%)`。尽管如此，这仍然是一个任意的选择——使用对您的用例最有意义的[单元(但是](https://blog.alexdevero.com/css-units-ultimate-guide/)[小心`px`！](https://engageinteractive.co.uk/blog/em-vs-rem-vs-px))。

最后，我们将通过简单地以合理的、布局独立的顺序列出页面内容来实现`PageLayout`组件(为简洁起见，省略了子道具):

**PageLayout.jsx**

```
const PageLayout = ({ type = 'mobile' }) => (
  <div className={`page-layout page-layout-${type}`}>
    <Logo />
    <NavLinks />
    <PageTitle />
    <PageDescription />
    <Main />
    <ShareButtons />
    <UserSettings />
  </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的样式表将把所有的子元素放入相应的网格区域。好了，这并不难！😄

### 好吧，但是为什么呢？

现在你可能会说，“这看起来不错，但是除了将元素移动到陌生的地方，我还能做什么有用的事情吗？”

你当然可以。

在 Podible，我们相信在工作中总是使用正确的工具，CSS 网格有效地解决了许多大多数第三方工具只能部分解决的问题。我们对网格布局做的实验越多，越多的领域证明它们是有用的。以我们最新的产品为例——嵌入式播放器:

[![Embeddable Player Preview](img/bf93f1057daabd8db29a1aed60bbd0af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hgNKcUZq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ATNKy_7ANo0qBlwMN38Pslg.png)

[(互动版)](https://play.podible.co/embeddable-player/episode/4482314)

现在它只是根据宽度做了一些小的调整，但是设置是这样的，我们将来可以很容易地给这个组件添加新的布局。

现在就这些——在下面的评论中让我知道你接下来打算如何使用 CSS grid。感谢阅读！

* * *

想加入行动吗？ [Podible 正在招聘全栈工程师](https://angel.co/podible/jobs/318994-senior-full-stack-software-engineer)！