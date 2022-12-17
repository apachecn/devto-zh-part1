# SASS 运算符

> 原文：<https://dev.to/sarah_chima/sass-operators-56f>

您学习和使用的 Sass 特性越多，您就越喜欢 Sass。Sass 的另一个令人惊叹的特性是能够以许多有趣的方式使用算术运算符和其他运算符。我们将在本文中讨论所有这些。让我们马上开始吧。

### 赋值运算符

冒号`:`是赋值操作符。它用于定义变量并为变量赋值。这里有一个例子。

```
 $font-size: 24px; 
```

Enter fullscreen mode Exit fullscreen mode

### 算术运算符

这些是我们知道的普通算术运算符。它们是:

| 操作员 | 描述 |
| --- | --- |
| + | 添加 |
| - | 减法 |
| / | 分开 |
| * | 增加 |
| % | 剩余物 |

这些运算符的功能与它们在普通算法中的功能相同。但是，需要注意的是，不能对不同单位的值执行算术运算。我们将用例子来解释这一点。先说加法`+`和减法`-`。

**加减法**
以下是加减法的有效运算。

```
 .header-small {
       /*addition*/
       font-size:  24px + 2px; 
       width: 4em + 2; 

       /*subtraction*/        
       height: 12% - 2%;
       margin: 4rem - 1;
   } 
```

Enter fullscreen mode Exit fullscreen mode

这被编译成

```
 .box-small {
        /*addition*/
        font-size: 26px;
        width: 6em;

        /*subtraction*/
        height: 10%;
        margin: 3rem; } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这些值要么具有相同的单位，要么根本没有单位。有单位的值应该在运算符的左侧，即应该在最前面。让我们尝试使用不同的单位，看看结果。

```
 .box-big {
        font-size:  22px + 4em; // Error: Incompatible units: 'em' and 'px'.
        width: 30% - 20px; // Error: Incompatible units: 'px' and '%'.
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下我们都会出错。也可以加减颜色。这里有一个例子。

```
 $primaryColor: #202020;

    .box {
        background-color: $primaryColor + #123456;
        color: $primaryColor - #100110;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这被编译成

```
 .box {
       background-color: #325476;
       color: #101f10; } 
```

Enter fullscreen mode Exit fullscreen mode

这是如何工作的？Sass 对 RGB 颜色代码的每个对应部分执行操作。所以在上面代码的加法部分，发生了这样的事情。

```
 20+12=32(red color) 20+34=54(green color) 20+56=76(blue color) 
```

Enter fullscreen mode Exit fullscreen mode

如果您添加两个颜色值，并且它超过了颜色范围，结果将是颜色范围上的最后一个值`#FFFFFF`。同样的，如果你减去的比颜色范围多，你会得到`#000000`。我认为 Sass 足够善良，不会抛出一个错误。:)T2 也可以用于连接，我们很快就会看到。

**乘法**
在 Sass 中，乘法运算符的用法与加法和减法相同，只是一个值不能有单位。因此，下面的代码在编译时会产生有效的 CSS。

```
 .box-small {
        height: 16px * 4;
    } 
```

Enter fullscreen mode Exit fullscreen mode

它的 CSS 是

```
 .box-small {
       height: 64px; } 
```

Enter fullscreen mode Exit fullscreen mode

而这个会导致错误。

```
 .box-small {
        height: 16px * 4px; //Error: 64px*px isn't a valid CSS value.
    } 
```

Enter fullscreen mode Exit fullscreen mode

**分部**

如果我们使用`/`操作符而不把值放在括号里，它被认为是 CSS 中正斜杠的正常用法，不执行任何操作。看看下面的例子。

```
 .box-medium {
        font-size: 30px / 5px;
        width:  24px/ 4;

    } 
```

Enter fullscreen mode Exit fullscreen mode

这被编译成

```
 .box-medium {
       font-size: 30px / 5px;
       width: 24px/ 4; } 
```

Enter fullscreen mode Exit fullscreen mode

没有区别，因为它被视为正常的 CSS。所以为了让 SCSS 做计算，我们把我们的值放在括号里。

```
 .box-medium {
        font-size: (30px / 5px);
        width:  (24px/ 4);

    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 .box-medium {
       font-size: 6;
       width: 6px; } 
```

Enter fullscreen mode Exit fullscreen mode

执行操作。注意，如果您使用`/`而没有将数值放在括号中，您可以使用不同的单位，但是当它们在括号中时，您只能在一个数值上使用相似的单位或没有单位。

余数运算符的使用方式与加法和减法运算符相同。接下来，让我们考虑一下`+`操作符的另一种用法。

### 字符串操作

`+`操作符可用于连接字符串，即将两个字符串连接在一起。需要注意的一些事项是:

1.  如果带引号的字符串出现在不带引号的字符串之前，则结果字符串会被加上引号。
2.  如果不带引号的字符串出现在带引号的字符串之前，则结果是不带引号的字符串。

让我们用例子来证明这一点。

```
 p:before {
       content: "I am a string with" +quotes;
       font: Arial + ", sans-serif";
   } 
```

Enter fullscreen mode Exit fullscreen mode

这被编译成

```
 p:before {
       content: "I am a string withquotes";
       font: Arial, sans-serif; } 
```

Enter fullscreen mode Exit fullscreen mode

当与 Mixins 一起使用时，它相当有趣。这里有一个例子。

```
 @mixin introduction($name) {
        &:before {
            content: "I'm a supercool person called " +$name;
        }
    } 

    p {
        @include introduction(Sarah);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这被编译成

```
 p:before {
       content: "I'm a supercool person called Sarah"; } 
```

Enter fullscreen mode Exit fullscreen mode

就像您可以对其他编程语言所做的一样。接下来我们来考虑其他运营商。

### 比较运算符

在 Sass 中，有一些运算符可用于将一个值与另一个值进行比较。它们是:

| 操作员 | 情况 | 描述 |
| --- | --- | --- |
| == | x == y | 如果 x 和 y 相等，则返回 true |
| ！= | x！= y | 如果 x 和 y 不相等，则返回 true |
| > | x > y | 如果 x 大于 y，则返回 true |
| < | x < y | 如果 x 小于 y，则返回 true |
| >= | x >= y | 如果 x 大于或等于 y，则返回 true |
| <= | x <= y | 如果 x 小于或等于 y，则返回 true |

这种比较可以用来帮助 Sass 做出决定。这里有一个例子。

```
 @mixin spacing($padding, $margin) {
        @if ($padding > $margin) {
            padding: $padding;
        } @else {
            padding: $margin;
        }  
    }

    .box {
        @include spacing(10px, 20px);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这被编译成

```
 .box {
        padding: 20px; } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们使用了`>`操作符来测试给定的填充是否大于边距。然后根据返回值设置填充值。其他比较运算符也可以以同样的方式使用。让我们转到最后一组操作符。

### 逻辑运算符

逻辑运算符包括:

| 操作员 | 情况 | 描述 |
| --- | --- | --- |
| 和 | x 和 y | 如果 x 和 y 为真，则返回真 |
| 或者 | x 或 y | 如果 x 或 y 为真，则返回真 |
| 不 | 不是 x | 如果 x 不为真，则返回真 |

让我们用一个例子来说明如何使用它们。我们将使用一个逻辑运算符来决定哪种背景色应该应用于按钮。

```
 @mixin button-color($height, $width) {
        @if(($height < $width) and ($width >=35px)) {
            background-color: blue;
        } @else {
            background-color: green;
        }       
    }

    .button {
        @include button-color(20px, 30px)
    } 
```

Enter fullscreen mode Exit fullscreen mode

这被编译成

```
 .button {
       background-color: green; } 
```

Enter fullscreen mode Exit fullscreen mode

因为两个条件都不满足，所以`background-color`被设置为`green`。如果使用了`or`，它将被设置为蓝色，因为至少满足一个条件。

这些操作符都很有价值，如果使用得当，可以让 CSS 变得非常有趣。

有什么问题或补充吗？请留言评论。

感谢您的阅读。:)