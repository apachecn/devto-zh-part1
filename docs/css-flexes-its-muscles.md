# CSS 展示了它的肌肉

> 原文：<https://dev.to/kayis/css-flexes-its-muscles>

## 什么？

遗憾的是，由于网络还不能与原生移动应用相提并论，而且移动设备的使用正在超越台式机，我最终不得不涉足原生移动业务。

幸运的是，2 年前脸书宣布了 React-Native(RN)，它基本上允许你编写基于 JavaScript 的移动应用，目标是本地 UI 元素而不是 HTML 元素。

为此，他们甚至[重新实现了 CSS 的](https://facebook.github.io/yoga/)部分，所以 Web 开发人员现在可以“做应用”,不仅可以使用他们大部分的 JS 和 React 知识，还可以使用我们所有人~~讨厌~~非常喜欢的 CSS。

所以，现在我在做花哨的原生应用，就像我想成为的潮人一样，我也被迫学习 Flexbox。为什么？嗯，正如我所说，他们只实现了 CSS 的*部分*而不是全部。他们实现的部分是 **Flexbox** 。

早在 2003 年，当我开始用 HTML 和 CSS 做我的第一个静态网页时，我了解了 [Box-Model](https://wiki.selfhtml.org/wiki/CSS/Box-Modell) ，这可能是我在做前端 Web 开发时学到的最好的东西。

了解这一点在我的脑海中创建了一个关于我所写的所有标记和样式的视觉模型。由于我很长时间没有手动编写*，而是切换到 PHP 动态生成，后来又切换到 JavaScript，这对我帮助很大。*

 *但是现在事情发生了变化，CSS 得到了一个新的布局引擎，叫做 *Flexbox* 。在我被迫与 RN 一起使用它之前，我并没有过多考虑这个问题。

我觉得我在 CSS 框架的帮助下做的 90%的事情，比如最近的 Bootstrap，现在都可以用几行 CSS 来完成。

## 如何？

想要对齐`<div>`中的一些元素，使它们之间的间距相等，并且周围有一半的空间？

```
div {
  display: flex;
  justify-content: space-around;
} 
```

就是这样。

RN 和 Web 的区别在于，RN 默认为*原生元素*启用它，而在 Web 上你必须用`display: flex;`手动启用它。`flex-direction`也有不同的默认值。在 RN 上，默认是`column`，可能是因为大多数移动设备都是智能手机，并且是纵向模式。在 Web 上默认为`row`。

你也可以用 Flexbox*订购*你的元素，这非常好。例如，当你需要一个独立于页眉和页脚位置的框时。

```
<main>
  <header>...</header>
  <footer>...</footer>
  ...
</main> 

main {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

main > header {
  order: -1;
}
main > footer {
  order: 1;
} 
```

默认情况下，Flexbox 容器的子容器将得到`order: 0`，所以除了`<footer>`和`<header>`之外添加的所有东西都将是`0`并在它们之间获得。`justify-content: space-between`将所有内容均匀隔开，但不在页眉上方或页脚下方添加任何空间。

## 为什么？

当人们谈论 Web 开发时，他们经常嘲笑人们告诉他们“我知道 HTML 和 CSS，我是一名程序员！”，因为“hurr，这些都不是真正的编程语言”，我觉得这样不好。尤其是 CSS，是一个很难掌握的东西，就像我说的，从头开始学习它帮助了我作为一名 Web 开发人员的整个职业生涯。

像 Flexbox 这样的东西，以及它的兄弟 CSS [Grid](https://developer.mozilla.org/en-US/docs/Web/CSS/grid) ，将会改变网站的风格，使它们更加灵活。所以尽快学习它会让每个 Web 开发人员的生活更轻松。*