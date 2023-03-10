# CSS 变量介绍

> 原文：<https://dev.to/sarah_chima/an-introduction-to-css-variables-cmj>

使像 Sass 这样的预处理程序不那么吸引人的一个特性是使用变量的能力。最近，我写了一篇关于 [SASS 变量](https://dev.to/sarah_chima/sass-variables-2pb)的文章，探讨了变量如何使我们的 CSS 更易于维护。猜猜谁也有使用变量的能力？我们自己的 CSS。这意味着我们不必使用任何预处理器来利用变量。

在 CSS 中，变量被称为**自定义属性**。输入“变量”有时比输入“自定义属性”更容易，所以我将在本文中交替使用它们。本文讨论如何声明和使用 CSS 变量。首先，让我们了解 CSS 变量解决的问题。

### **问题**

值往往会在 CSS 文件中重复，以保持应用程序的外观一致。这些值可以是颜色，甚至是字体大小。记住这些价值观并不总是容易的。我是说，记住颜色的名字比记住颜色代码更容易。如果这些值存储在变量中，并且只需通过变量的名称就可以调用，生活就会变得容易得多。

当您想要更改这些值时，会出现另一个问题。假设您一直在使用一种错误的颜色，并希望更改该颜色的所有实例。虽然查找和替换看起来是完美的解决方案，但它并不总是理想的，尤其是在大型 CSS 文件中。如果颜色存储在一个变量中，你只需要改变它一次，它会影响所有使用它的属性。

**为什么要用 CSS 变量代替？**
但是为什么 CSS 会有变数呢？为什么不用一个预处理器呢？以下是一些原因:

1.  你不需要一个预处理器来使用它们。是的，这是一个合理的理由。
2.  它们层叠。与预处理器不同，您可以在任何选择器中设置变量来设置或覆盖当前值。所以没有使用 CSS 变量的余地。
3.  它们可以与媒体查询一起使用，以创建响应属性。
4.  它们可以在运行时更改。这意味着您可以使用 JavaScript 来访问和操作 CSS 变量。

现在我们已经考虑了所有这些，让我们看看如何使用 CSS 变量。

### **使用 CSS 变量**

要使用 CSS 变量，我们必须知道两件事:

1.  如何声明一个 CSS 变量？
2.  如何使用 CSS 变量？

先说第一个。

**自定义属性的语法**

声明自定义属性的语法非常简单。这里有一个例子。

```
 --primary-color: #00AEEF; 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚声明了一个自定义属性。关于自定义属性的语法，您应该注意以下几点。

1.  自定义属性的名称必须始终以两个破折号开头，即`--`。
2.  自定义属性区分大小写。所以`--primary-color`和`--Primary-color`不一样。

现在我们知道了这一点，让我们继续讨论自定义属性的一个重要特性。

**级联**
自定义属性遵循正常的级联规则，因此相同的属性可以在不同的特异性级别上定义或设置。请注意，自定义属性确实会继承。因此，如果没有为给定元素的自定义属性设置值，它将继承其父元素的值。看一个例子。

```
 :root { --color: blue}
    div { --color: green}
    p{ --color: red}

    * { color: var(--color)} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`div`和`p`元素使用重新分配给`--color`变量的颜色值。不是`div`和`p`元素的子元素的其他元素将使用它们的父元素`:root`伪类的颜色。

级联很重要，因为它使我们能够通过媒体查询为同一个变量设置不同的值。这对于预处理器是不可能的。我们来举个例子。

```
 --width: 80%
    @media screen and (min-width: 768px) and (max-width: 1020px) {
        --width: 60%;
    }
    @media screen and (min-width: 1020px) {
        --width: 40%
    } 
```

Enter fullscreen mode Exit fullscreen mode

变量`--width`被赋予不同的值，无论何时使用该变量，都会使用适合每个屏幕尺寸的值。这使得 CSS 变量非常有用，有助于响应式设计。关于语法已经说得够多了，让我们转到实际使用语法的部分。

**使用 CSS 变量**
要使用声明变量，我们使用`var()`函数。您可能已经在本文的第一个示例中看到了。`var()`函数的作用是获取一个变量并用该变量的值替换它自己。这里有一个例子。

```
 --padding: 10px 20px;

    div {
        padding: var(--padding);
    } 
```

Enter fullscreen mode Exit fullscreen mode

`var(--padding)`将被替换为`10px 20px`。`var`函数的实际语法是这样的:

```
 var(<custom-property-name> [, <declaration-value> ]? ) 
```

Enter fullscreen mode Exit fullscreen mode

其中`custom-property-name`是已经声明的变量的名称，如前面示例中的`--padding`，而`<declaration-value>`是后备值，即如果引用的自定义属性无效时应该使用的值。看下面一个例子。

```
 p {
        font-family: var(--primary-font,  "sans-serif")
    } 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果`--primary-font`的值无效或者可能从未声明过，则使用后备字体`sans-serif`。也可以使用多个值作为后备。这些值由逗号分隔。让我们重写我们之前的例子。

```
 p {
        font-family: var(--primary-font, "Lato", "Roboto", "sans-serif")
    } 
```

Enter fullscreen mode Exit fullscreen mode

然而，在速记值的情况下，比如那些用于边距和填充的值，逗号不用于分隔它们。所以一个合适的退路是:

```
 p {
        margin: var(--margin, 10px 20px 5px)
    } 
```

Enter fullscreen mode Exit fullscreen mode

不需要用逗号分隔它们。

**用 Calc()函数**自定义属性
也可以用 CSS `calc()`函数，这使得使用起来更加有趣。calc 函数用于在 CSS 中执行计算。让我们看看它如何与自定义属性一起使用。

```
 :root {
        --margin: 2rem;
    } 

    div {
        margin: calc(var(--margin) * 2);
    }

    p {
        margin: var(--margin)
    } 
```

Enter fullscreen mode Exit fullscreen mode

### **浏览器支持**

目前 Chrome 49、Edge 16、Firefox 42、Safari 9.1 和 iOS Safari 9.3 支持自定义属性。

要了解更多关于 CSS 变量的信息，这里有一些关于它的好文章。CSS 变量:你为什么要关心？
[CSS 变量——不，真的！](https://medium.com/dev-channel/css-variables-no-really-76f8c91bd34e)
[为什么我对原生 CSS 变量感到兴奋](https://philipwalton.com/articles/why-im-excited-about-native-css-variables/)

有什么问题或补充吗？留下评论。

感谢您的阅读。:)