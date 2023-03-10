# 萨斯混合食品

> 原文：<https://dev.to/sarah_chima/sass-mixins-19a>

有时在我们的项目中，有一组 CSS 样式被反复使用。可能是厂商前缀，如`-webkit-`、`-moz-`、`-ms-`，或者可能是媒体查询。随着时间的推移，编写这样的代码变得单调乏味。Mixins 帮助我们解决这个问题。

什么是 Mixin？
mixin 是一个可重用的 CSS 样式块。它让我们可以对 CSS 声明进行分组，以便在整个站点中重用。创建和使用它们非常简单，但是它们非常强大，可以帮助我们在 CSS 中使用许多编程语言中的许多功能。在本文中，我们将探索 mixins 的威力。让我们马上开始。

**如何创建一个 mixin**
我们通过使用`@mixin`指令后跟 Mixin 的名称来创建一个 Mixin。然后，我们希望它包含的样式块包含在名称后面的花括号中。参见下面的示例。

```
 @mixin transform {
        -webkit-transform: scale(1.5);
        -moz-transform: scale(1.5);
        -ms-transform: scale(1.5);
        transform: scale(1.5);
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚创建了一个 mixin。`transform`现在可重复使用。那么我们如何再利用它呢？我们使用`@include`指令将它添加到代码的其他部分，如下所示。

```
 .box {
        @include transform;
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在如果被编译，就变成了

```
 .box {
        -webkit-transform: scale(1.5);
        -moz-transform: scale(1.5);
        -ms-transform: scale(1.5);
        transform: scale(1.5); } 
```

Enter fullscreen mode Exit fullscreen mode

非常简单。随着我们进一步使用它，它变得非常有趣。

**使用参数**
在上面的例子中，注意我们为转换属性使用了一个固定值，即`scale(1.5)`。当使用转换混合时，如果我们想使用不同的值，该怎么办？我们可以通过向 mixin 传递一个参数来做到这一点。是的，我们也可以在 CSS 中使用参数。这允许更大的灵活性。例如，在我们上面的例子中，我们可以使用任何我们想要的转换属性。就这么办吧。

```
 @mixin transform ($value) {
       -webkit-transform: $value;
       -moz-transform: $value;
       -ms-transform: $value;
       transform: $value;
   } 

   .box {
       @include transform(skewY(1.5deg));
   } 
```

Enter fullscreen mode Exit fullscreen mode

这将被编译成

```
 .box {
        -webkit-transform: skewY(1.5deg);
        -moz-transform: skewY(1.5deg);
        -ms-transform: skewY(1.5deg);
        transform: skewY(1.5deg); } 
```

Enter fullscreen mode Exit fullscreen mode

酷吧？

**多个自变量**

我们不仅限于一个论点。在 mixin 中，我们可以使用任意多的参数。参数之间用逗号分隔，我们可以这样做。

```
 @mixin spacing ($margin, $border, $padding) {
        margin: $margin;
        border: $border;
        padding: $padding;
    }

    .box {
        @include spacing( 10px, 1px solid black, 15px )
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将被编译成

```
 .box {
        margin: 10px;
        border: 1px solid black;
        padding: 15px; } 
```

Enter fullscreen mode Exit fullscreen mode

**参数中的默认值**

参数也可以被赋予默认值。如果在使用 mixin 时没有值传递给它，这些值就充当后备。让我们将默认值传递给上面的`spacing` mixin。

```
 @mixin spacing ($margin, $border, $padding: 1rem) {
        margin: $margin;
        border: $border;
        padding: $padding;
    }

    .box {
        @include spacing( 10px, 1px solid black )
    } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们为`$padding`添加了一个默认值。然后当我们使用 mixin 时，我们没有为填充传递任何参数。编译时，结果会怎样？

```
 .box {
       margin: 10px;
       border: 1px solid black;
       padding: 1rem; } 
```

Enter fullscreen mode Exit fullscreen mode

所以填充使用默认值，但是如果传递了一个值，它就使用那个值。需要注意的是，只有在没有默认值的参数之后，才应该添加有默认值的参数。所以在下面的例子中这样做会导致错误。

```
 @mixin spacing ($margin , $padding: 1rem, $border) {
        margin: $margin;
        border: $border;
        padding: $padding;
    }

    .box {
        @include spacing( 10px, 5px, 1px solid black )
    } 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们已经看到了如何创建和使用 mixins，以及如何在参数中使用它。

**SASS 中的变量参数**

有时我们需要一个参数来接受多个值。例如，像填充或边距这样的属性可以取一到四个值。为此，我们使用可变参数。变量参数允许我们以列表的形式传递变量。它们就像常规参数一样，只是在末尾有`...`来表示它可以接受一个列表。Mixins 将这些值视为变量，而不是列表。让我们举个例子。

```
 @mixin margin ($margin...) {
       margin: $margin;
   }

   .box1 {
       @include margin(10px);
   }
   .box2 {
       @include margin(10px 20px);
   }
   .box3 {
       @include margin(10px 20px 10px)
   }
   .box4 {
       @include margin(10px 20px 10px 30px)
   } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译为。

```
 .box1 {
       margin: 10px; }

   .box2 {
       margin: 10px 20px; }

   .box3 {
       margin: 10px 20px 10px; }

   .box4 {
       margin: 10px 20px 10px 30px; } 
```

Enter fullscreen mode Exit fullscreen mode

如果这些变量参数与其他参数一起使用，则变量参数应该放在其他参数之后。

在包含 mixin 时，也可以将这些变量参数传递给 mixin。这可以是列表或地图。看下面一个例子。

```
 $colors1: #F2F2F2, #404040, #00AEEF;
    $colors2: ( text: #404040, border: #00AEEF, background: #F2F2F2);

    @mixin color ($background, $text, $border) {
        background-color: $background;
        color: $text;
        border-color: $border
    }

    .first {
        @include color($colors1...);
    }

    .second {
        @include color($colors2...);
    } 
```

Enter fullscreen mode Exit fullscreen mode

`$colors1`是列表。当使用列表时，参数出现的顺序就是它们被传入 mixin 的顺序。所以我们必须相应地对它们进行排序。另一方面，使用地图时，顺序并不重要。请注意，`background`是在末尾传递的，而不是在开头，但是当它被编译时，它会给出适当的结果。这是结果。

```
 .first {
       background-color: #F2F2F2;
       color: #404040;
       border-color: #00AEEF; }

   .second {
       background-color: #F2F2F2;
       color: #404040;
       border-color: #00AEEF; } 
```

Enter fullscreen mode Exit fullscreen mode

我们应该讨论的最后一件事。

**@内容**

指令允许我们传递没有在 mixin 中定义的 CSS 样式块。这些样式将出现在您在`mixin`声明中放置`@content`的任何地方。这里举个例子会有帮助。这个例子展示了 mixins 如何有效地用于媒体查询。

```
 @mixin small {
        @media (max-width: 480px) {
            border: 1px solid #404040;
            @content;
        }
    }

    @mixin medium {
        @media (min-width: 481px) and (max-width: 768px) {
            @content;
        }
    }

   .box {
        @include small {
            width: 80%;
        }
        @include medium {
            width: 50%;
        }
   } 
```

Enter fullscreen mode Exit fullscreen mode

这编译为

```
 @media (max-width: 480px) {
       .box {
           border: 1px solid #404040;
           width: 80%; } }
   @media (min-width: 481px) and (max-width: 768px) {
      .box {
           width: 50%; } } 
```

Enter fullscreen mode Exit fullscreen mode

使用 mixins 可以做很多事情。这确实是一个强大的功能。

有什么问题或补充吗？留下评论。

感谢您的阅读。:)