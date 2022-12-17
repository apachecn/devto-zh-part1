# 怪异@延伸副作用

> 原文：<https://dev.to/peiche/weird-extend-side-effects-4lp>

我有一个使用 Bootstrap 的项目。我不是导入 CSS 并用我自己的扩展样式，而是导入 Sass 样式并编译成一个 CSS 文件。就在这样做的时候，我注意到了一些奇怪的事情。

Bootstrap 定义了一个按钮类，如下所示(为了简洁起见，代码被删减了，因为我的重点是选择器):

```
.btn {
  // styles
} 
```

Enter fullscreen mode Exit fullscreen mode

我自己的一些风格使用 Sass 指令`@extend`作为表单输入按钮:

```
.search-form .search-submit {
  @extend .btn;
  @extend .btn-secondary;
} 
```

Enter fullscreen mode Exit fullscreen mode

在其他地方，我有一些特定于链接的样式，我想确保*不会被*应用到`.btn`类:

```
a:not(.btn) {
  // styles
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，当我编译成 CSS 时，我没有看到那些链接特有的样式被应用。相反，这是输出到我的样式表的内容:

```
a:not(.btn):not(.search-form .search-submit) {
  /* styles */
} 
```

Enter fullscreen mode Exit fullscreen mode

这是因为我在提交按钮上使用了`@extend`。所以我将 Bootstrap 的按钮类定义复制到我自己的样式表中的 mixin 中，并将输入按钮的样式改为:

```
.search-form .search-submit {
  @include button;
  @include button-variant($secondary, $secondary);
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦我做了更改，样式表的输出就如我所料:

```
a:not(.btn) {
  /* styles */
} 
```

Enter fullscreen mode Exit fullscreen mode

注:`button` mixin 是我的。`button-variant` mixin 已经存在于 Bootstrap 的代码中。

* * *

*这篇文章最初发表于[eichefam.net](https://eichefam.net/2017/12/28/weird-extend-side-effects/)。*