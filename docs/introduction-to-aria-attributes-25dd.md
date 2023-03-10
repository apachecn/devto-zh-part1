# ARIA 属性介绍

> 原文：<https://dev.to/magnificode/introduction-to-aria-attributes-25dd>

让所有使用网络的人都能访问网络是我们工作中非常重要的一部分。最近，我开发了一个强调可访问性的组件库，并且学到了相当多的 ARIA 角色和属性，帮助浏览器和屏幕阅读器正确地识别和定义各种组件。

### 什么是咏叹调？

> “WAI-ARIA 是一个可访问的富互联网应用程序套件，它定义了一种使 Web 内容和 Web 应用程序对残障人士更容易访问的方法。它特别有助于使用 Ajax、HTML、JavaScript 和相关技术开发的动态内容和高级用户界面控件。”—[https://www.w3.org/WAI/intro/aria.php](https://www.w3.org/WAI/intro/aria.php)

ARIA 的核心目标是帮助残障人士，尤其是依赖屏幕阅读器或不能使用鼠标的人。选项卡定位和页面上正确定义的组件有助于实现这一点。

### 怎么用？

上面链接的规范详细介绍了 ARIA 角色和属性的广泛范围。这些属性被添加到各种 DOM 节点，就像添加任何其他属性一样。一个相对常见的例子是图标。当你使用某种你不想让屏幕阅读器试图破译的技术时，你应该告诉屏幕阅读器跳过这个节点。

```
<i className="super-fancy-icon" aria-hidden="true"></i> 
```

Enter fullscreen mode Exit fullscreen mode

所有不同的 ARIA 属性都是这样实现的。其中一些属性需要一个 ID 来引用，其他的则不需要前缀`aria-`。让我们通过一些例子来看看实际情况。

### `role`

[https://www.w3.org/TR/wai-aria-1.1/#usage_intro](https://www.w3.org/TR/wai-aria-1.1/#usage_intro)

ARIA `role`属性是在 DOM 中的一个元素上设置的，正常的 HTML5 隐含语义没有应用于该元素(例如，`<nav>`元素隐含了一个‘nav’角色)。对于更复杂的功能，必须应用`role`,以便辅助技术能够正确地解释，并且不会将该元素与类似的元素混淆。这里有一个例子:

```
<ul role="menubar">
  <li role="menuitem">About</li>
  <li role="menuitem">Work</li>
  <li role="menuitem">Contact</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

元素猫有各种角色，这里列出了这些角色[。以下是对上述问题的解释:](https://www.w3.org/TR/wai-aria/roles#widget_roles)

#### `menubar`

[https://www.w3.org/TR/wai-aria/roles#menubar](https://www.w3.org/TR/wai-aria/roles#menubar)

> 通常保持可见的菜单显示，通常水平显示。

#### `menuitem`

[https://www.w3.org/TR/wai-aria/roles#menuitem](https://www.w3.org/TR/wai-aria/roles#menuitem)

> 菜单或菜单栏包含的一组选项中的一个选项。

有很多其他的 ARIA 属性可以帮助定义导航或者菜单，而这些属性是辅助技术传统上不能识别的。

### `aria-hidden`

[https://www . w3 . org/TR/wai-aria-1.1/# aria-hidden](https://www.w3.org/TR/wai-aria-1.1/#aria-hidden)

这个比较常见。我相信您已经看到了利用这种 ARIA 属性的技术或方法。尤其是在需要隐藏可视呈现内容的情况下。这通常会在你使用某种图标系统(SVG 可以变得[非常容易访问](https://css-tricks.com/accessible-svgs/))或者图像替换之类的东西时出现。

```
<span className="screen-reader-text icon-codepen" aria-hidden="true" >CodePen</span> 
```

Enter fullscreen mode Exit fullscreen mode

该规范建议在使用该属性时要小心，因为您选择了对辅助技术隐藏可视呈现的内容。因此，绝对要确保输出的内容可以在网站的其他地方被这些技术访问到。

### `aria-selected`

[https://www.w3.org/TR/wai-aria-1.1/#aria-selected](https://www.w3.org/TR/wai-aria-1.1/#aria-selected)

使用`aria-selected`，您告诉辅助技术页面上的一个组件(或多个组件)被选中。这里一个相对常见的用例是选项卡或手风琴。

```
<div className="accordion">
    <a href="#" className="accordion-header" aria-selected="true">Selected Accordion Header</a>
    <p className="accordion-content">Super fancy tab content!</p>
  </div>
  <div className="accordion">
    <a href="#" className="accordion-header" aria-selected="false">Not Selected Accordion Header</a>
    <p className="accordion-content">Super fancy tab content!</p>
  </div>
  <div className="accordion">
    <a href="#" className="accordion-header" aria-selected="false">Not Selected Accordion Header</a>
    <p className="accordion-content">Super fancy tab content!</p>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，第一个 accordion 项被选中，并可能被展开(查看下一节对 aria-expanded 的解释)。当用户在手风琴中导航时，应该切换`aria-selected`属性。

### `aria-expanded`

[https://www.w3.org/TR/wai-aria-1.1/#aria-expanded](https://www.w3.org/TR/wai-aria-1.1/#aria-expanded)

就像被选中的一样，这一个很容易解释。`aria-expanded`向辅助技术表明项目是展开还是折叠。我们将扩展上面的例子。

```
<div className="accordion">
    <a href="#" className="accordion-header" aria-selected="true" aria-expanded="true">Selected Accordion Header</a>
    <p className="accordion-content">Super fancy tab content!</p>
  </div>
  <div className="accordion">
    <a href="#" className="accordion-header" aria-selected="false" aria-expanded="false">Not Selected Accordion Header</a>
    <p className="accordion-content">Super fancy tab content!</p>
  </div>
  <div className="accordion">
    <a href="#" className="accordion-header" aria-selected="false" aria-expanded="false">Not Selected Accordion Header</a>
    <p className="accordion-content">Super fancy tab content!</p>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

类似地，当辅助技术在手风琴中导航时，应该切换`aria-expanded`属性。

### `aria-labelledby`

[https://www.w3.org/TR/wai-aria-1.1/#aria-labelledby](https://www.w3.org/TR/wai-aria-1.1/#aria-labelledby)

`aria-labelledby`和下一项，`aria-describedby`非常相似。这两个 ARIA 属性都告诉屏幕阅读器如何描述页面上的元素。传递给该属性的值是一个以空格分隔的 id 列表，这些 id 链接到页面上的静态文本，这些文本将被引用来帮助描述元素。

```
<main className="main-content" aria-labelledby="main-content-title">
  <h1 id="main-content-title">How to get started with accessibility.</h1>
  <p>WAI-ARIA is a technical specification that provides a framework to improve the accessibility and interoperability of web content and applications. This docu...</p>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中,`aria-labelledby`属性值指向`#main-content-title`,它是描述相关元素内容的静态文本的一小段。

*注意*，不要在试图描述图标或单个元素时使用`aria-labelledby`。 [aria-label](https://www.w3.org/TR/wai-aria-1.1/#aria-label) 属性用于定义标记当前元素的字符串，而`aria-labelledby`属性引用一个 ID。

### `aria-describedby`

[https://www.w3.org/TR/wai-aria-1.1/#aria-describedby](https://www.w3.org/TR/wai-aria-1.1/#aria-describedby)

与`aria-labelledby`属性非常相似，`aria-describedby`为屏幕阅读器提供了与某个元素相关的内容的更多细节。它的工作方式与前者完全一样，通过解析一个用空格分隔的 id 列表来描述元素。

```
<main className="main-content" aria-labelledby="main-content-title" aria-describedby="main-content-excerpt">
  <h1 id="main-content-title">How to get started with accessibility.</h1>
  <p id="main-content-excerpt">In this article we'll explore the basics regarding accessibility, and some of the fancy attributes you can start using today.</p>
  <p>WAI-ARIA is a technical specification that provides a framework to improve the accessibility and interoperability of web content and applications. This docu...</p>
</main> 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，非常相似，但只是识别为一个更长的描述。

### 咏叹调做好了没有？

没错。就我最近遇到的基本 ARIA 属性而言，这是一个非常坚实的开端。下次你在你的站点上构建一个组件的时候，花点时间想想屏幕阅读器会如何解释这个元素，也许这些 ARIA 属性会帮助你前进，创建一个更易访问的 web！