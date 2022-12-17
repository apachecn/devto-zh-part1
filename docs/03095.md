# 彩色编码与 SCSS 每个循环

> 原文：<https://dev.to/lynnewritescode/colour-coding-with-scss-each-loops-31k>

通常我的工作需要我创建用户可以从 CMS 添加和定制的模块。在许多情况下，他们喜欢控制模块的背景颜色，以适应他们的品牌调色板。

我想分享我在 SCSS 写作的方式，因为有些人可能会觉得这很有用。

下面是他们可以添加的模块的 html 示例。他们可以在 CMS 中选择背景颜色，该颜色会作为模块的类别名称显示出来，例如“绿色”。

```
<section class="module green">
  <p>Content goes here</p>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

在 SCSS 文件中，我们可以创建一个名为`$brand-colors`的颜色列表。在每个括号内，我们有一个字符串(来自 CMS 的颜色类名称)，十六进制值或颜色变量名，用逗号分隔。您可以在这里添加任意多的值，但是对于这个示例，我将坚持使用两个值。

```
$brand-colors: ("red",$c-red),("green", #008000),("blue",#0000ff);

.module {
  @each $color in $brand-colors {
    &.#{nth($color,1)} {
      background-color: nth($color,2);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以声明一个 each 循环，嵌套在模块的样式中，循环遍历列表`$brand-colors`中的每个`$color`。

第一部分- `&.#{nth($color,1)}` -引用了`$brand-colors`列表中括号内的第一个值。对于这个例子，如果我们用正常的方式写出来，它会是这样的:

```
.module {
  &.red {}
  &.green {}
  &.blue{}
} 
```

Enter fullscreen mode Exit fullscreen mode

下一部分- `background-color: nth($color,2);`引用每组括号内的匹配变量或十六进制代码。注意这个不需要用`#{}`包装。

使用这样的 each 循环，您可以节省大量时间和许多代码行。对于 2 或 3 种颜色来说，这可能看起来有些多余，但是如果他们决定将来要添加更多的颜色，这种方式也很容易扩大规模。我用的例子是颜色编码，但这可以用在各种不同的方式，不同的价值/选项。

希望这在某些方面有所帮助！请随意分享你如何在项目中使用它的想法。

更多阅读:[http://thesassway.com/intermediate/if-for-each-while](http://thesassway.com/intermediate/if-for-each-while)