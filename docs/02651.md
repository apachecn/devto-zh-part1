# 萨斯遗产的好处

> 原文：<https://dev.to/sarah_chima/the-goodness-of-sass-inheritance-5hm>

随着样式表变得越来越大，组织和维护大型样式表变得越来越困难和复杂。Sass 附带了许多特性，使得维护这样的样式表更加容易。如果你是 Sass 的新手，你一定要读一下这篇文章。

Sass 的一个令人惊叹的特性是选择器能够从另一个选择器继承样式。为此，我们使用了`@extend`指令。当你使用它的时候，它是非常令人惊奇的。让我们用一个简单的例子来说明。

```
 .foo {
        height: 30px;
    }

    .bar {
        @extend foo;
        width: 10px;
    } 
```

Enter fullscreen mode Exit fullscreen mode

挺好用的。这类似于 mixins 的使用方式，但是神奇的事情发生在编译 SCSS 的时候。这被编译成

```
 .foo, .bar {
      height: 30px; }

   .bar {
      width: 10px; } 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到`.bar`继承了`.foo`的所有，并拥有自己的属性。这减少了 CSS 代码的数量，从而减小了文件大小。让我们用一个更实际的例子。

这里，我们想要创建不同的按钮，它们具有相同的基本样式，只是颜色不同。我们将创建一个基类，包含每个按钮包含的公共样式，然后每个按钮继承这些样式，而不是一遍又一遍地编写属性。这在下面的例子中可以看到。

```
 .button {
        height: 30px;
        font-size: 16px;
        padding: 0 2rem; 
        border-radius: 4px;
    }

    .button-default {
        @extend .button;
        color: #404040;
        border: 1px solid #404040;
    } 

    .button-success {
        @extend .button;
        background: green;
        color: white; 
    }

    .button-danger {
        @extend .button;
        background: red;
        color: white;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这被编译成

```
 .button, .button-default, .button-success, .button-danger {
        height: 30px;
        font-size: 16px;
        padding: 0 2rem;
        border-radius: 4px; }

    .button-default {
        color: #404040;
        border: 1px solid #404040; }

    .button-success {
        background: green;
        color: white; }

    .button-danger {
        background: red;
        color: white; } 
```

Enter fullscreen mode Exit fullscreen mode

我想我们现在清楚地知道如何使用`@extend`指令进行继承了。请注意，您可以扩展其他选择器，如 id、标签，甚至伪类。也可以继承一个选择器的属性，该选择器继承了另一个选择器的属性。如果这听起来令人困惑，看看下面的例子。

```
 .button {
       height: 30px;
       font-size: 16px;
       padding: 0 2rem; 
       border-radius: 4px;
    }

    .button-default {
        @extend .button;
        color: #404040;
        border: 1px solid #404040;
    } 

    .button-default-blue {
        @extend .button-default;
        background: blue;
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里，`button-default`继承了`button`的属性，而`button-default-blue`继承了`button-default`的属性。这意味着`button-default-blue`继承了`button`和`button-default`的属性。这叫做连锁。

上面的 SCSS 编译成

```
 .button, .button-default, .button-default-blue {
        height: 30px;
        font-size: 16px;
        padding: 0 2rem;
        border-radius: 4px; }

    .button-default, .button-default-blue {
        color: #404040;
        border: 1px solid #404040; }

    .button-default-blue {
        background: blue; } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们考虑一下使用继承可以给我们的样式表带来的好处。

### 继承的优势

1.  **允许可重用的 CSS 样式**
    CSS 中的重复样式大量出现。这并不好，因为它用不必要的样式填充了我们的样式表。继承减少了 CSS 的重复，因为相似组件共享的样式在样式表的相同位置被继承和定义。这导致了一个更加干净和可维护的样式表。

2.  **减少了 HMTL 类的使用量**
    继承的另一个好处是我们不必在 HTML 中写那么多类。让我们用上面的按钮例子来解释这一点。想象一下，如果我们不使用`@extend`功能。我们的 HTML 看起来会像这样。

```
 <button class="button button-default">Example</button> 
```

Enter fullscreen mode Exit fullscreen mode

但是对于继承，我们将只使用一个类。

```
 <button class="button-default">Example</button> 
```

Enter fullscreen mode Exit fullscreen mode

现在，这可能看起来很小，因为我们只使用了两个类，但是如果你必须使用许多其他类，这就变得混乱了。像下面这样。

```
 <button class="button button-default button-default-blue button-default-disabled ">Example</button> 
```

Enter fullscreen mode Exit fullscreen mode

如果继承被恰当地使用，它明显地减少了我们在 HTML 代码中使用的类的数量。这使得 HTML 代码更加简洁，易于调试和维护。这些优势为开发者节省了时间和精力。接下来，让我们考虑一些使用继承的一般技巧。

### 使用继承的提示

1.  **对相似的组件使用`@extend`**
    这会让你的样式表保持理智。就像在我们的 button 例子中，我们看到所有的类都是相似的，都是 button 类的变体。想象一下，如果一个`li`或表格单元格或任何其他不相关的组件继承了这些按钮属性。这可能会使调试和维护 CSS 变得困难。

2.  **不要过度使用`@extend`**
    虽然`@extend`是一个非常有用的功能，但应该小心使用。过度使用它可能会使我们的代码难以维护。例如，如果我们有一个基类，并且有大约 50 个类继承它的属性，那么一个属性的任何变化都会影响许多其他类。所以不应该过度使用。

3.  **何时用 mixin 代替**
    有时候我们应该用 mixin 代替扩展。如果你不明白什么是 mixin，你可以阅读[这篇文章](https://dev.to/sarah_chima/sass-mixins-19a)。`@extend`指令不支持媒体查询，也不接受参数。因此，如果有必要使用这些功能，请使用 mixin。不过，一般来说，当这些特性不是必需的时候，继承是一种方法。

因此，如果使用得当，继承是 Sass 的一个令人愉快的特性。

有什么问题或补充吗？请留言评论。

感谢您的阅读。