# CSS 101:选择器

> 原文：<https://dev.to/damcosset/css-101-selectors-78m>

## 简介

CSS(层叠样式表)是一种允许你描述你的标记应该具有的样式的语言。很长一段时间，我把 CSS 当成一个非常简单的东西，把你的文本变成某种颜色，在这里添加一点边框，也许是一个可爱的动画。

然后，我开始遇到真正擅长 CSS 的开发人员，嗯，远不止这些...所以，我花了一些时间来变得更好，我们从基础开始。

## 选择器

我们可以通过给每个元素添加一个样式属性来设置标记的样式:

```
<p style="color:red;">Paragraph</p>
<span style="color: red; font-size=14px;">Span element</span> 
```

Enter fullscreen mode Exit fullscreen mode

我让你想象一下，你要这样写多少不同的规则...更不用说应用任何种类的更改了...

在 CSS 中，我们可以使用不同的选择器。它们允许我们将不同的元素分组，并将相同的样式规则应用于这个组。我们将探索:

*   元素选择器
*   类别和 ID 选择器
*   属性选择器

## 元素选择器

对于标记，我将在整篇文章中使用 HTML。我们可以根据元素对我们的风格进行分组。例如，给每个*元素一个特定的样式。*

 *h1 和 p 标签被分组并共享相同的规则。我没有为段落创建三个不同的规则，也没有为标题创建两个规则，而是为每一个都创建了一个规则。

您还可以通过在 CSS 中用逗号分隔不同的元素来将它们组合在一起:

参见 [CodePen](https://codepen.io) 上达米安·科赛特( [@Tagada85](https://codepen.io/Tagada85) )的笔[元素选择器](https://codepen.io/Tagada85/pen/aVLzLB/)。

在这种情况下，不要忘记逗号！如果这样做，它不会对元素进行分组，而是选择后代元素。

## 类和 ID 选择器

### 类

为了在选择元素时有更大的灵活性，可以为一个元素指定一个或多个类。每个类都用空格分隔。我们使用*类的*属性。

```
<p class="danger"></p>
<h1 class="danger title"></h1> 
```

Enter fullscreen mode Exit fullscreen mode

在 CSS 中，使用句点(。)来指示您正在定位一个特定的类名。您可以一个接一个地链接几个类。

```
.danger {
 /* Will style the paragraph */
}

.danger.title{
 /* Will style the h1*/
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，顺序并不重要。如果指定 *.title.danger* ，效果也是一样的。它也不需要完全匹配。如果你添加第三个类到你的头，它仍然会遵循*块中的规则。*

参见 [CodePen](https://codepen.io) 上达米安·科赛特( [@Tagada85](https://codepen.io/Tagada85) )的笔[类选择器](https://codepen.io/Tagada85/pen/QOqwPw/)。

### ID

ID 与类非常相似。主要区别在于，一个 ID 只能用于一个元素。一个元素只能有一个 ID，因为 ID 不能有空格分隔的值。我比班级更有分量。如果你有一个元素，它的 ID 和类改变了相同的东西(比如颜色)，那么在 ID 规则中描述样式。最后，在 CSS 声明中，ID 由井号(#)定位。

参见 [CodePen](https://codepen.io) 上达米安·科赛特( [@Tagada85](https://codepen.io/Tagada85) )的笔 [ID 选择器](https://codepen.io/Tagada85/pen/KyXgqj/)。

## 属性选择器

Class 和 ID 是 CSS 能够以特殊方式处理的特殊属性。但是在我们的标记中，我们可以使用更多的属性。CSS 允许我们以不同的方式来满足您的需求

*   简单属性选择器

您编写 CSS 规则来检查元素是否有指定的特殊属性。我们甚至不在乎价值。例如，我想将所有具有*键*属性:
的 *p* 元素作为目标

```
p[key] {
  color: red;
}

/* OR, all elements with a key attribute, not just p */
[key]{
  color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们在 CSS 中使用 *[]* 来定位属性。所以这是第一个，很笼统。

*   确切的属性值

这一次，我们要寻找**确切的**属性值。

```
 /* If the href is https://google.com, it won't match */
  a[href="google.com"] {
    color: purple;
  }

  /* The following are NOT equal */
  [class="danger warning"] {
    color: red;
  }

  .danger.warning {
    color: orange;
  } 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们正在寻找一个精确的匹配。因此，如果我们使用括号符号，元素需要有危险和警告、*这两个类，并且只有这两个*。与点符号完全不同...

*   部分属性值

最后，如果你真的想更好地控制你正在使用的属性，你需要使用下面的属性选择器。

```
[attribute~="val"]{
  /* Attribute contains val in a space-separated list of words */
  /* Would match "friend" in "my friend Joe" */
}

[attribute*="val"] {
  /* Select any element with an attribute whose value CONTAINS val */
}

[attribute^="val"] {
  /* Select any element with an attribute whose value BEGINS with val */
}

[attribute$="val"] {
  /* Select any element with an attribute whose value ENDS with val*/
}

[attribute|="val"]{
  /* 
  Select any element with an attribute whose value starts with val followed by a dash (val-) 
  OR whose value is exactly equal to val.
  */
}

[attribute="val"i] {
  /* Add a i after the value to make it case insensitive ( The value will be case insensitive, NOT the attribute name. */
} 
```

Enter fullscreen mode Exit fullscreen mode

示例:

参见 [CodePen](https://codepen.io) 上 Damien Cosset ( [@Tagada85](https://codepen.io/Tagada85) )的笔[部分属性选择器](https://codepen.io/Tagada85/pen/aVLBBq/)。

## 结论

这就是了。快速概述使用 CSS 选择元素的不同方式。当然，这些工具可以做很多不同的事情。但应该足够你玩了。*