# 为什么我使用 SASS 而不仅仅是 CSS

> 原文：<https://dev.to/roelofjanelsinga/why-i-use-sass-rather-than-just-css-4p93>

[![Why I use SASS rather than just CSS](img/7c0b333ca2608bb20bce6c5304ffa780.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xBmsupcz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ml10ioswuux61x10mcz.jpg)

# 为什么我使用 SASS 而不仅仅是 CSS

## CSS 来的少

当我第一次从事网页设计和开发时，CSS 是让 HTML 页面看起来更好的工具。这是在响应式网页设计开始成为网页设计的新行业标准之前。网站被缩小，在移动设备上坏掉，但这在当时很正常。我第一次开始学习 CSS 中的媒体查询是在我被介绍到 Twitter Bootstrap 时，我一直使用到今天。我还在用这个，因为现在我更多的是一个网页开发者，而不是网页设计师，所以大部分 CSS 已经不是我做的了。无论如何，Twitter Bootstrap 让我开始使用 CSS3 提供的更多功能。在实习期间，我开始寻找让编写 CSS 更高效的方法，因为我发现自己不断地复制和粘贴样式。这是我发现较少的地方。LESS 给了我当时想要的东西，嵌套 CSS。这在很大程度上帮助我减少了复制和粘贴 CSS。

## 少来斗嘴

然而，在我用了几个月之后，我觉得还没有完全好起来。Jerke 和一个和我一起工作的人，让我开始了 SASS。我知道我在这里找到了我一直在寻找的东西。SASS 提供嵌套 CSS，也提供函数和混合。这帮助我(几乎)再也不会复制 CSS 了。最近我用得更多的一个很棒的东西是函数。这些帮助我精确地计算边距、宽度和高度应该是多少。显然，自从我发现了 Flexbox 之后，这已经很少使用了，但是它仍然有它的应用。以前，当我开始使用 LESS 时，我使用一个名为 Panda 的程序来编译我的 CSS 文件。这一切都改变了，当我切换到萨斯。这就是格朗特出现的地方。Grunt 不断地观察和编译我的 SASS 文件，因此我可以立即测试我所做的更改。

## 单个文件

但是回到使用 SASS 而不是 CSS。使用 SASS(和 LESS)的一个好处是，我可以很容易地将所有“模块”(文件)包含在一个主文件中。这样我可以让 Grunt/Gulp/Webpack 监视所有文件中的变化，当它检测到这一点时，它编译一个新文件。这有助于保持网站上文件加载的效率，但它不会以效率换取易用性。我的意思是，当我使用普通的 CSS 文件时，我需要创建多个文件来区分不同的功能。显然，这不是我想要的工作方式。使用 SASS，它只加载一个文件，这个文件是由无限数量的独立文件组成的。我可以很容易地管理这些不同的文件，而 Grunt 为我做所有的编译工作。这样就不会影响易用性。

## Mixins

我前面提到的关于 SASS 的另一件重要的事情是在我的文件中使用 mixins 的能力。这意味着我可以在 SASS 文件中创建标准的“类”,并轻松地将它们包含在其他元素的样式中。例如，我想做一个橙色的按钮。在普通的 CSS 中，你可以创建两个不同的类，一个是。按钮“和一个存在”。橙色按钮”。button 类可以组成形状、字体和边框样式。orange-button 类可以使按钮变成橙色，并实现自定义的悬停样式。mixin 做的事情更简单。可以定义一个 mixin，它有两个参数，color 和 hover-color。然后在 orange-button 类中，可以使用:button(orange，a-darker-orange)调用 mixin。这减少了代码，在我看来，更容易快速设计不同元素的样式。

## 易于管理

使用 SASS 让我的网站设计又变得有趣多了。在我开始使用 SASS 之前，我讨厌它，因为我知道我需要在另一个文件中工作。我不得不将这个文件包含在 HTML 文件中，这太乏味了。然后我甚至没有谈论我已经在使用的巨大的 CSS 文件。然后，我不得不尝试找到正确的类或 ID，并希望这实际上不会改变任何事情。使用 SASS 使得对特定元素的处理变得更加容易管理，特别是 Grunt 通过在后台编译我的主 SASS 文件来支持我。在使用 SASS 的时候，它减少了我在造型上的挫败感。
它让我的工作变得更简单、更高效，让我有机会真正按照自己的意愿构建文件，而不必在我的 HTML 文件中加载另一个文件。