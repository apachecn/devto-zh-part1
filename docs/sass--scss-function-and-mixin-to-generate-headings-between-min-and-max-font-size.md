# 萨斯/ SCSS 函数生成最小和最大字体大小之间的标题

> 原文：<https://dev.to/nickreynke/sass--scss-function-and-mixin-to-generate-headings-between-min-and-max-font-size>

在学校里，我想:“为什么我必须从几何学的角度来学习线条？!"我不知道为什么以及在哪里使用它们。几天前我记得我们在学校学过如何使用它们，但是我现在不记得如何使用它们了，所以我必须查一下。

我想要一个函数来返回特定标题的字体大小，所以我想出了这个函数`f(x) = -8px * x + 68px`，其中`x`是从 1 到 6 的标题。

此函数中的标题将以 h1 的 60px 字体大小开始，以 h6 的 20 px 字体大小结束。

我是这样想出这个函数的:

```
f(x) = m * x + c

maxFontSize = 60px
minFontSize = 20px
m = (minFontSize - maxFontSize) / (6 - 1)
c = maxFontSize - m

f(x) = -8px * x + 68px 
```

Enter fullscreen mode Exit fullscreen mode

终于到了 SCSS:

```
@function getHeadingFontSize($minFontSize, $maxFontSize, $heading) {
  $m: ($minFontSize - $maxFontSize) / (6 - 1);
  $c: $maxFontSize - $m;
  @return $m * $heading + $c;
}

// Font size of headings
@for $i from 1 through 6 {
  h#{$i} {
    font-size: getHeadingFontSize(20px, 60px, $i);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就生成了下面的 CSS:

```
h1 {
  font-size: 60px
}

h2 {
  font-size: 52px
}

h3 {
  font-size: 44px
}

h4 {
  font-size: 36px
}

h5 {
  font-size: 28px
}

h6 {
  font-size: 20px
} 
```

Enter fullscreen mode Exit fullscreen mode

我不知道这在某种程度上是否有用，因为你团队中的设计师只会给你尺寸，但是...耶。