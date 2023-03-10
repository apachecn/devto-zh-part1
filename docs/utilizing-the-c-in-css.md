# 在 CSS 中使用“C

> 原文：<https://dev.to/azinasili/utilizing-the-c-in-css>

*这篇文章最初发表在[石墨 GTC 博客](https://blog.graphitegtc.com/)上。*

对于许多开发人员来说，CSS 是一个棘手的问题。开发人员总是在样式表的末尾附加规则，导致一些意想不到的后果。在对 CSS 存在的原因进行了一些愤怒和诅咒之后，添加了一个`!important`，瞧，一切都正常了。大多数情况下，这些问题是由 CSS 的*级联*特性引起的。由于对 CSS 中“C”的根深蒂固的憎恨，许多聪明人开发了一些方法来帮助减少或完全消除这个特性。这些方法包括 [OOCSS](https://www.smashingmagazine.com/2011/12/an-introduction-to-object-oriented-css-oocss/) 、[smacs](https://smacss.com/)和 [BEM](http://getbem.com/) 。

这篇文章的目的不是反对这些方法；它们在管理你的样式表和保持你的 CSS 一致性方面非常有用。如果我们能够利用 CSS 的层叠特性，那会怎么样？想象一下，如果我们可以编写一个 CSS 规则，并让它传播到下行元素。

为了真正利用样式表的级联性质，我们需要使用一些不太为人所知的 CSS 单元和关键字。我们将采用以下方法:

*   EM(有时是 REM)单位
*   继承
*   当前颜色

## EMs 和 REMs

您可能熟悉使用`PX`单位来调整元素、边距、填充、边框等。我们可以用`EMs`和`REMs`做同样的事情，但是额外的好处是这些单位是相对值而不是绝对值。 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/length#Relative_length_units) 将`EMs`定义为:

> 这个单位表示元素的计算字体大小。如果在 font-size 属性本身上使用，它表示元素的继承字体大小。

MDN 也给出了这个关于`EMs`的花絮

> 这个单元通常用于创建可缩放的布局，即使当用户改变字体大小时，也能保持页面的垂直节奏。CSS 属性 line-height、font-size、margin-bottom 和 margin-top 通常有以 em 表示的值。

注意，MDN 说`EMs` *“表示元素*的继承字体大小，并且*“用于创建可伸缩的布局”*。这正是我们要找的东西！下面是 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/length#Relative_length_units) 对`REMs`的描述:

> 这个单元表示根元素的字体大小(例如元素的字体大小)。当在这个根元素的字体大小上使用时，它代表它的初始值。

两者最大的区别是`REMs`总是基于根元素的`font-size`引用它的值，而`EMs`从元素的`font-size`继承它的单位。如果这看起来令人困惑，不要担心，看看下面的 CSS 可以更好地理解:

```
.element {
  font-size: 20px;
  padding: .5em;
} 
```

在上面的例子中，元素的填充等于 10px。记住`EM`单位是相对于元素的`font-size`的，这意味着我们的填充等式是 20 * .5 = 10。

```
.element {
  font-size: 20px;
  padding: .5em;
}

.element-child {
  font-size: .25em;
  padding: 1em;
} 
```

想象一下这个场景:我们知道`.element`填充等于 10px，但是`.element-child`的值呢？因为`EMs`复合了我们的数学，事情会变得有点棘手。`.element-child`的`font-size`将等于 5px (20 * .25 = 5)。由于父元素的`font-size`为 20px，并且我们将子元素定义为父元素的. 25em，因此我们的复合值等于 5px。在这种情况下，填充要容易得多，因为`EMs`看一下`font-size`值，我们的填充等于 5px。

希望这不会太难处理。`EMs`的棘手之处在于如何计算输出值。因为这很麻烦，所以我为 Sass 创建了一组函数和 mixins，让我们的生活更轻松！你可以在这里找到 [Github 要点。](https://gist.github.com/azinasili/8e41854f58176cbd9cff)

## 继承关键字

我们武库中的另一个工具是关键字`inherit`，这个值允许我们获取定义在父元素上的样式，并将其应用到子元素上。我们可以做到这一点，而不需要将相同的规则定义两次。 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/inherit) 将`inherit`定义如下:

> inherit CSS-value 使为其指定的元素从其父元素获取属性的计算值。它在每个 CSS 属性上都是允许的。

想象以下使用`inherit`关键字的场景:

```
.element {
  background: coral;
  padding: 1em;
}

.element-child {
  color: white;
  padding: inherit;
} 
```

我们的`.element-child`将从`.element`获取填充值，并将其应用于自身。这很好，因为如果`.element`上的`padding`改变，那么`.element-child`的`padding`也会改变！如果我们试图避免多次编写相同的样式，这是完美的。这种技术的一个缺点是关键字只查看父元素的值；我们无法在 DOM 树中向上移动并获得祖先元素的值。

## 当前颜色关键字

这个关键字的作用类似于`inherit`，但有一个很大的不同，`currentColor`可以用来继承通常不继承该值的属性的颜色。 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value#currentColor_keyword) 将`currentColor`定义如下:

> currentColor 关键字表示元素的 Color 属性的计算值。它允许让默认情况下不继承它的属性或子元素属性继承颜色属性。

现在我们已经了解了一些可以用来管理我们的价值观如何级联的技术，让我们把它付诸行动。

## 我们新技术的例子

参见 [CodePen](http://codepen.io) 上阿辛阿希利([@阿辛纳希利](http://codepen.io/azinasili))利用 CSS - Button 示例中的‘C’的笔[。](http://codepen.io/azinasili/pen/GrQQmW/)

上面的例子只是让我们的脚湿湿的。虽然很简单，但它完美地展示了如何使用`EMs`来缩放我们的元素。你会注意到两个按钮，看起来都一样，除了`.btn--large`要大得多。即使按钮变大了，我们也没有调整按钮的填充，甚至没有重置它的`border-radius`，相反，我们只调整了`font-size`。整洁！

因为`EMs`继承了`font-size`的值，所以任何使用`EMs`的属性都将伸缩。由于我们的`.btn--large`比最初的`.btn`大 1.25 倍，这意味着其他值也会大 1.25 倍。这正是我们想要的级联。如果不小心，值的组合可能会导致问题，但是如果有意使用，它可能是以最小的努力创建不同大小的元素的最佳方式。

在 [CodePen](http://codepen.io) 上看到 Azin Asili ( [@azinasili](http://codepen.io/azinasili) )利用 CSS - Cards 中的‘C’的笔[。](http://codepen.io/azinasili/pen/pRLEwR/)

这个卡的例子扩展了级联的一些原则。同样，我们使用`EMs`来调整元素的大小，但是现在我们也使用`currentColor`和`inherit`关键字来设计某些元素的样式。与其硬编码颜色和其他值，我们可以使用方便的关键字让样式级联到我们想要的元素。

参见 [CodePen](http://codepen.io) 上 Azin Asili ( [@azinasili](http://codepen.io/azinasili) )利用 CSS - Pricing 表中的‘C’的笔[。](http://codepen.io/azinasili/pen/NdYdeZ/)

我们的最后一个例子将展示我们新发现的级联知识，并将其应用到更真实的场景中。就像其他例子一样，我们将继续使用`EMs`来确定元素的大小，但是有一个例外，`.price`上的`margin`将使用`REMs`来代替。这是因为我们不希望我们的利润随着`font-size`的变化而增长。注意到中间的桌子比另外两张大吗？我们不仅使用颜色来突出显示特定的选项，而且还调整了整个表格的大小，使其比其他两个表格大。颜色和尺寸的增加都是为了吸引顾客的注意。通常对于较大尺寸的元素，我们必须调整每个`padding`和`font-size`属性的大小。相反，我们使用`EM`和*级联*的力量来为我们做这件事。

## 最后的想法

上述技术是一种收回 CSS 控制权的绝妙方法，然而，这确实需要一点额外的脑力。当您有嵌套元素时，使用`EMs`作为单位值可能会导致问题。这可以用一些简单的数学或者用[函数/混合](https://gist.github.com/azinasili/8e41854f58176cbd9cff)来解决，如果你使用 Sass 或者其他前置/后置处理器的话。无论哪种方式，一点点额外的努力可以走很长的路，使您的样式表对您的项目更加灵活和可伸缩。让我知道你们的想法！