# SASS 变量

> 原文：<https://dev.to/sarah_chima/sass-variables-2pb>

如果你不知道什么是 SASS，你应该读一下这篇文章。
Sass 预处理器最棒的地方之一是使用变量的能力。这不仅仅限于 Sass，甚至最近 CSS 中也引入了变量。然而，我们在这里关注的是如何在 Sass 中使用它们。如果你不知道什么是变量，它们只是用来存储数据。首先，让我们理解为什么变量是可怕的。
**NB** :我将在本文中交替使用 Sass 和 SCSS，因为 SCSS 是 Sass 的更新版本，我将使用 SCSS 的语法。

**为什么变量？**
假设你在一个 CSS 文件中多次使用了一种特定的颜色，你想改变这种颜色。你会怎么做？查找并替换？如果你使用了一个变量，你就不必这么做了。你只需要更新变量的值，这将自动改变它在任何地方。
任何在代码中重复出现的属性，比如颜色或尺寸，通常都可以通过变量来声明。这使得开发人员更容易、更快地进行更改。变量是可重用的，因此使您的代码易于维护和更改。

### **使用变量**

在 Scss 中，变量是通过使用前面带有美元符号的任何变量名来声明的，并且像 css 属性一样进行设置。

```
 /* SCSS */
    $height: 2rem; 
```

Enter fullscreen mode Exit fullscreen mode

`$height`可以在你代码的任何地方使用。

```
 /* SCSS */
    $height: 2rem;

    .button {
        height: $height;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将被编译成

```
 .button {
        height: 2rem; } 
```

Enter fullscreen mode Exit fullscreen mode

让我们再做一些，这次用颜色。

```
 /* SCSS */
    $gray: #a1a1a4;
    $blue: #00aeef;
    $red: #c05d5d;

    .button-success {
        background-color: $blue;
    }

    .button-danger {
        background-color: $red;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将被编译成

```
 .button-success {
        background-color: #00aeef; }

    .button-danger {
        background-color: #c05d5d; } 
```

Enter fullscreen mode Exit fullscreen mode

如果我想改变这些颜色的颜色代码，我只需简单地改变一次，它就会在我使用过的任何地方更新。

**变量可以存储什么？**

SCSS 接受八种可以存储为变量的数据类型。根据 Sass 文档，它们是:

*   数字(例如 2.4、15、10px)
*   带引号和不带引号的文本字符串(例如“foo”、“bar”、“baz”)
*   颜色(例如蓝色、#04a3f9、rgba(255，0，0，0.5))
*   布尔值(例如，真、假)
*   空值(例如 null)
*   由空格或逗号分隔的值列表(例如 2.5 em 3em 0.4 em，Helvetica，Arial，sans-serif)
*   从一个值映射到另一个值(例如(key1: value1，key2: value2))
*   函数引用

这是可以用 SCSS 变量存储的所有东西。如果你不明白为什么有些数据类型可以存储为变量，你迟早会发现它们的相关性。如果你想知道如何使用数据类型，查看 [Sass 文档](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#Variables_____variables_)。

在我们进一步讨论之前，我认为在使用变量时我们应该考虑一些重要的东西:范围。

### **SASS 变量的范围**

变量的作用域仅仅意味着变量在哪里可用。在 SCSS，有两个作用域:全局作用域和局部作用域。如果一个变量具有全局范围，这意味着它可以在任何地方使用。局部范围由`{}`界定。因此，如果一个变量是在一个`{}`中声明的，因此有一个局部范围，那么它只能在这个范围内使用。我们用例子来说明这一点。

```
 /* SCSS */
    $width: 30px;

    .button {
        $fontSize: 16px;
        font-size: $fontSize;
        width: $width;
    }

    .dropdown {
        width: $width;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将被编译成

```
 .button {
        font-size: 16px;
        width: 30px; }

    .dropdown {
        width: 30px; } 
```

Enter fullscreen mode Exit fullscreen mode

这些变量中哪些具有全局范围？你说`$width`？如果你做了，你是正确的。这就是为什么它同时适用于`.button`和`.dropdown`级。
另一方面，`$fontSize`变量有一个局部范围。它只在`button`和嵌套在其中的其他选择器中可用。如果我们试图在`dropdown`中使用`$fontSize`，我们会得到一个错误`Undefined variable: "$font-size"`。

局部变量优先于全局变量。也就是说，如果同一个变量在全局范围内声明，也在局部范围内使用，则该变量的值将是在局部范围内分配给它的值。所以在上面的例子中，让我们在`.dropdown`中重新声明`$width`，下拉菜单的宽度是多少？让我们找出答案。

```
 /* SCSS */
    $width: 30px;

    .dropdown {
        $width: 50px;
        width: $width;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将被编译成

```
 .dropdown {
       width: 50px; } 
```

Enter fullscreen mode Exit fullscreen mode

这就解释了。宽度成为其局部范围的宽度。

**该！全局标志**
可以让局部声明的变量具有全局作用域。我们使用`!global`标志来实现这一点。让我们回到之前的例子，尝试使用在`.dropdown`的`.button`中声明的变量，但是这次使用了`!global`标志。

```
 /* SCSS */
    $width: 30px;
    .button {
        $fontSize: 16px !global; 
        font-size: $fontSize;
        width: $width;
    }

    .dropdown {
        font-size: $fontSize;
        width: $width;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将被编译成

```
 .button {
       font-size: 16px;
       width: 30px; }

   .dropdown {
       font-size: 16px;
       width: 30px; } 
```

Enter fullscreen mode Exit fullscreen mode

这次没有返回错误。`!global`标志使得`$fontSize`可以在任何地方使用。接下来让我们考虑另一面旗帜。

**该！默认标志**

当在给一个变量赋值时使用了`!default`标志时，我们只是说如果这个变量没有在别处赋值，就使用这个值。它使一个值作为一个后备。如果我们正在创建将被重用的自定义变量，这将非常方便。我们来举个例子。

```
 $brand-color: #00aeef !default;
    $brand-background: #000000 !default; 
```

Enter fullscreen mode Exit fullscreen mode

所以如果这个变量没有在别处被重新赋值，这些默认值将被使用。

也可以使用 SCSS 函数`variable-exists()`检查变量是否存在。这在讨论函数时会更好理解。

用 SCSS 变量可以做很多事情。你用得越多，你就越能看到它们的用途。

有什么问题或补充吗？留下评论。

感谢您的阅读。:)