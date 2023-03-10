# 用于封装网站的 CSS

> 原文：<https://dev.to/maxart2501/css-for-an-encapsulated-web-7fo>

> 或者，如何(d)从复杂的编码惯例发展到更简单的规则定义。

CSS 20 岁了，万岁！

我们仍然在大规模地与 CSS 的老问题作斗争。嘘。

然而，事情正在发生变化。公平地说，真正变化的规范已经出现了一段时间，但这种事情需要时间来成为标准和普遍的。

## 问题

关于级联样式表的最大问题是...瀑布。简而言之，过多的规则可能会相互影响，也因为它们都有一个*全局*范围。很快就发现这样的事情不太管用:

| 密码 | 影响 |
| --- | --- |
| `.content { ... }` | "等等，我没有在这里添加这个边界半径，这是怎么回事？"“哦。哦，是的，我已经在别的地方定义了一个`content`类，抱歉。” |
| `#login .header { ... }` | “为什么这一切都没有风格？”“是的，好吧，你必须保留那个`login` ID”“但这不是登录页面！”“呃，好吧，我想……” |
| `article > section > p { ... }` | "这不是这个段落的正确风格，为什么？"“看，我不得不在这里用一个`div`把`section`包起来……” |
| `z-index: 99999999999;` | “我应该在这里再写几个 9？" |
| `color: #fff !important;` | “太好了，现在我这里不能变色了！” |
| `-webkit-text-fill-color: #000;` | “说真的，老兄？” |

名字冲突。特定的种族。出于无奈，标记为`!important`的不重要声明。随处可见的风格。甚至浏览器之间的差异也无济于事。一点也不。

## 第一个方案

随着网页和应用程序变得越来越复杂，出现了一些最佳实践:

*   选择器中没有更多 ID；
*   没有“蛮力”值(如`z-index`)；
*   不再有`!important`或奇异的 CSS 属性；
*   CSS 和 DOM 结构之间没有严格的绑定；
*   保持尽可能低的特异性，理想的是 0 . 1 . 0；
*   为你的类使用命名空间。

这还不够，所以 CSS——所有 web 语言中最简单的一种——也有了自己的编码风格指南。像 OOCSS、SMACSS、Atomic CSS 和 BEM 这样的约定诞生了。最后一个可以说是最成功的一个，所以我将在本文中提到它，尽管相同的概念或多或少适用于其他的。

## 界定风格范围

假设我们有自己的[卡原型](https://codepen.io/MaxArt2501/pen/yowYwq):

[![A common picture card, with header, image, description a buttons](img/bc38ca78776a2acc3777b60455d8d6b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e_V3yCkY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gu9a9g8onedriz74gh4i.png)

我们用这个 HTML 结构来支持这个设计:

```
<div class="content-card">
  <header class="content-card__header">...</header>
  <picture class="content-card__picture">
    <img src="..." class="content-card__image">
  </picture>
  <section class="content-card__abstract">
    <p>...</p>
  </section>
  <footer class="content-card__footer">
    <button type="button" class="content-card__favorite">☆</button>
    <nav class="content-card__actions">
      <a href="#" class="content-card__action">Read later</a>
      <a href="#" class="content-card__action content-card__action--view">View</a>
    </nav>
  </footer>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

看看这些代码的华丽冗长！

使用 BEM 可以让我们清楚地命名我们的类，而不用(太)关心名字冲突、特殊性问题和所有其他问题。但这是以冗长的类名为代价的，并不总是易于阅读。即使像这样使用预处理器规则嵌套技巧也有它的缺点:

```
.content-card {
  ...
  &__action {
    ...
    &--view {
      ...
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我想跟踪类`content-card__action`的规则，我不能只是复制名称并粘贴到我选择的编辑器的搜索框中。不是真正的交易破坏者，但仍然令人讨厌。

## 作用域样式

我们真正需要的是有一个一致的——可能是本地的——系统来本地应用我们的风格。BEM 展示了做这件事的方法，但它仍然是一个*手动*操作，这意味着它容易出错。

解决这个问题的第一个本地方法(早在 2012 年)是以`<style>`元素的 [`scoped`属性](https://css-tricks.com/saving-the-day-with-scoped-css/)的形式出现的:

```
<div>
  <style scoped>
    div { width: 30em; }
    p { color: #333; }
  <style>
  <p>I look black but I'm actually nicer</p>
</div>
<p>I'm pitch black</p> 
```

Enter fullscreen mode Exit fullscreen mode

由于这个原因，内部定义的规则只影响到`<style>`的父节点及其所有子节点。虽然它看起来很酷，也很正确，但它并没有获得太多的动力(Chrome 在 v36 上取消了对它的支持)，因为它没有解决另一个大问题，即*保护*我们的风格免受外部影响。

简而言之，全局和父作用域的样式规则仍然可以覆盖我们的局部规则。

## 【几乎】最终解决

当 web 开发意识到我们需要将我们的界面分成*个组件*并牢记这一概念进行开发时，路径就被设定了——尽管不容易遵循。

Web 组件是一个实际上早于作用域样式的概念，因为它们是在 2011 年引入的。但是在被广泛采用和 API 成熟之前，它们还有很长的路要走。但是和[影子 DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Shadow_DOM) 的概念一起，出现了*封装样式表*。

这意味着不仅我们组件的样式表有本地效果，而且外部的样式表对我们的组件也没有影响！

这主要意味着我们的类不再需要名称空间，因为不再有名称冲突或风格溢出的风险。简而言之，我们可以[为我们的图片卡减少 HTML](https://codepen.io/MaxArt2501/pen/YxgwYV) 如下:

```
<div class="content-card">
  <header class="header">...</header>
  <picture class="picture">
    <img src="..." class="image">
  </picture>
  <section class="abstract">
    <p>...</p>
  </section>
  <footer class="footer">
    <button type="button" class="favorite">☆</button>
    <nav class="actions">
      <a href="#" class="action">Read later</a>
      <a href="#" class="action action--view">View</a>
    </nav>
  </footer>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

那已经好多了！但是，如果我们充分利用封装，我们可以做得更多。这意味着我们也可以抛弃在规则中只使用特定性 0.1.0 的选择器的惯例。

那么，我们应该避免使用类吗？不尽然:我们只能在必要的时候使用它们。因为现在不仅我们的样式表被封装了，我们界面的整个*组件化*的概念自然带来了写*小东西*的习惯:小 JavaScript 模块，小 HTML，小 CSS。

这意味着我们从语义上选择的 HTML 元素可能是整个组件中唯一的:我们可能只有一个`header`，一个`footer`，或者多个`li`，但是对于一种类型的元素。

最后我们可以[进一步缩减我们的价格](https://codepen.io/MaxArt2501/pen/prYgKB) :

```
<content-card>
  <header>...</header>
  <picture>
    <img src="...">
  </picture>
  <section>
    <p>...</p>
  </section>
  <footer>
    <button type="button">☆</button>
    <nav>
      <a href="#">Read later</a>
      <a href="#" class="view">View</a>
    </nav>
  </footer>
</content-card> 
```

Enter fullscreen mode Exit fullscreen mode

现在这就更好了，可读性和清晰。我们甚至从有效载荷中剔除了一些有意义的字节。如果语义意义(对开发人员来说)消失了，我们可以再次使用 ID，因为它们也被封装了。

## 但是我*想让*从母体改变一个组件的外观！

毫无疑问，这是正当的需要。不幸的是，这个问题仍然没有一个明确的解决方案。

有人提议在 CSS 中引入一个[“穿透阴影”的后代组合符](https://github.com/w3c/webcomponents/wiki/Shadow-piercing-Combinators-in-the-Wild)(即`>>>`或`/deep/`组合符)，但是[从那时起就被否决了](https://www.w3.org/wiki/Webapps/WebComponentsApril2015Meeting)，因为它被认为“太强大”了。事实上，它会将 CSS 封装简化为仅仅是限定范围的 CSS。

改变定制元素外观的唯一方法是使用 CSS“变量”(或者更好的[“定制属性”](https://developer.mozilla.org/en-US/docs/Web/CSS/--*) ):

```
/* styles.css */
:root {
  --base-button-color: rebeccapurple;
}

/* base-button's style */
button {
  background-color: var(--base-button-color, midnightblue);
} 
```

Enter fullscreen mode Exit fullscreen mode

这有点不方便，因为它被认为太“细粒度”，这也可能导致名称冲突(再次！)说到命名所说的自定义属性。这就是为什么 Tab Atkins 提出了`@apply`规则，这实质上意味着 CSS 中的*混合。虽然这听起来很棒，但它没有解决这个问题中的其他问题，该提案已经被其倡导者放弃，我们可能不会看到它被具体化。*

链接文章提到了最终可以解决问题的`::part`和`::theme`伪元素，但是还有很长的路要走。最后，我们现在必须坚持自定义属性。

## 如何使用封装的 CSS 呢？

Web 组件有一个复杂的 API，可能会让一些开发人员望而却步。这也是为什么诞生了像 [Polymer](https://www.polymer-project.org/) 这样的库。即使聚合物甚至 Web 组件对于您的开发需求来说毫无疑问，也有一些框架可以让您利用封装来实现风格。

当 Web 组件甚至只是影子 DOM 被排除在外时，可以模拟样式封装。这通常意味着为每个组件生成一个具有随机名称的属性，并将其附加到组件的每个样式规则和 dom 元素的*上。所以，从下面的标记和样式表开始:* 

```
<div>
  <h2>Hello!</h2>
  <p>I will get a random attribute even though I'm unstyled</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
h2 { font-size: 150%; } 
```

Enter fullscreen mode Exit fullscreen mode

它们在处理时会被翻译成这样:

```
<!-- Inside the <head> -->
<style type="text/css">
  h2[data-v-4c74d97c] { font-size: 150%; }
</style>

<!-- In the page -->
<div data-v-4c74d97c>
  <h2 data-v-4c74d97c>Hello!</h2>
  <p data-v-4c74d97c>I will get a random attribute even though I'm unstyled</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

另一个组件将获得一个不同的生成属性，这就是样式规则的作用域。

在应用样式时，模拟样式封装要慢一些，但是它可能不会对你的页面产生显著的影响。所以让我们看看最常见的前端库是如何处理样式封装的。

### 视图

就 CSS 而言，Vue *本身*是不可知的，但 Vue 作为一个生态系统提供了 vue-loader，这是一个用于 Webpack 的加载器，它利用 PostCSS 插件来实现模拟封装。通过使用 [vue-cli](https://github.com/vuejs/vue-cli) 和基于 Webpack 的脚手架模板，加载器被包含进来。

为了封装您的样式，您必须使用带有`scoped`属性的 [`<style>`来定义组件的样式。现在这有点不方便，因为它是旧规范中元素范围样式的相同语法，*没有*有相同的效果。](https://vue-loader.vuejs.org/en/features/scoped-css.html)

事实上，后代组件将*而不是*按照作用域样式进行样式化，这与最初的提议`<style scoped>`相反，因为它们将错过为祖先定义的生成属性。但是封装。

### 棱角分明

[Angular](https://angular.io/) 在封装方面更加复杂，因为它[让我们在根本没有封装、仿真(这是默认的)甚至利用 Shadow DOM 的原生封装之间做出决定](https://angular.io/guide/component-styles#view-encapsulation)。我想这也是可能的，因为 Angular 没有用*而不是*替换页面中的主机元素，事实上*复制了 Web 组件的功能。*

### 反应/预动作

尽管 [React](https://reactjs.org/) 及其不太知名(但功能不那么强大)的替代产品如 [Preact](https://preactjs.com/) 没有提供 CSS 的原生解决方案，但有大量的库允许对我们的组件进行样式化，从 CSS 模块到样式化组件，从 Glamor 到 JSXStyle。

这些库都是伟大的 CSS-in-JS 主题的一部分，它们都基于封装风格的概念，尽管大部分是基于模拟系统。

## 结论

CSS 有很长的路要走。它可能是最简单的语言之一，使用起来很简单，但这并不意味着管理起来很简单。随着大规模 web 应用程序的出现，这个问题变得很明显，但解决方案却没有。

经过漫长而艰难的岁月，通过样式封装，我们成功地大幅缩小了问题的规模。这不仅看起来是朝着正确方向迈出的一步，而且*感觉*喜欢。所以下一个项目使用什么库并不重要，建议是选择一个允许样式封装的库。