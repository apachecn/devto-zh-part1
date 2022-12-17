# 过度设计 Sass 地图

> 原文：<https://dev.to/huijing/over-engineering-sass-maps>

我终于为我不时构建的小随机演示创建了一个登陆页面，因为你知道，CSS 是我的爱好。SingaporeCSS 是我们的 meetup 群组，负责运营 Talk。当我去年建立这个网站时，我认为这是一个尝试 CSS 变量(以及彩色字体和其他“前沿”东西)的借口。

我特别喜欢的 Sass 的一个特性是循环能力。对我来说，这纯粹是为了方便开发人员，因为编译后的结果完全可以手写，但我是个懒虫，我宁愿花半天时间来弄清楚如何编写循环。*#过度工程*

这是一个用例。我想把我的颜色保存在一个 Sass 地图中，它有颜色的名称和十六进制代码。我想在以后的大量其他声明中重用这个图。所以在使用 CSS 变量来表示颜色的情况下，我希望在我的`var()`函数中也有后备值。

*思考:我实际上已经仔细考虑过这个问题，并且意识到如果我在这个问题上使用 Sass 映射，那么 fallback 值可能是多余的。跟我说说这个。*

CSS 变量应该有它们自己的帖子，我技术上已经写好了，除了它在 [Codrops CSS reference](https://tympanus.net/codrops/css_reference/custom-properties/) 上。但是如果你想了解更多关于 CSS 变量的知识，请直接到[参考资料部分](#further-resources)。

我还想使用相同的地图来编写第 n 个子选择器，这样相关的元素就会有基于我的 Sass 地图中的颜色的重复颜色。这一切听起来相当直截了当，不是吗？但是，事实证明 Sass 映射[没有索引交互变量](https://github.com/sass/sass/issues/996)。

在我天真的想法中，可能是独角兽拉屎彩虹的地方，我想写类似这样的代码:

```
@function colour($colour-name) {
  @return var(--#{$colour-name}, map-get($colours, $colour-name));
}

@each $name, $colour in $colours {
    section:nth-child($index) {
        background-color: colour($name);
        mix-blend-mode: color-dodge;
    }
} 
```

我想我一直在想，当我们使用`map()`函数时，我们可以使用的`index`参数，你知道吗？但这并不是说 Sass 本身不太棒。让我们谈谈我的项目中最常见的 Sass 功能。

如果你不熟悉 [Sass](http://sass-lang.com/) ，它是一个预处理器，使用与 CSS 相同的语法，但也通过 SassScript 扩展了它，允许属性使用混合、变量、嵌套、函数等等。

SassScript 支持 7 种数据类型:

*   数字
*   用线串
*   旗帜
*   布尔运算
*   无效的
*   列表
*   地图

我在这里只讨论 Sass 地图。

## 萨斯地图

Sass 映射是在 Sass 3.3 中引入的，它允许我们在 Sass 变量中存储一个逗号分隔的键值对列表。有了这个漂亮的新数据类型，我们也有了一些地图功能。这就是萨斯地图的样子:

```
$map: (
  key1: value1,
  key2: value2
); 
```

Sass 地图最常见的用例(至少对我来说)是管理颜色。我曾经把我的颜色存储在 Sass 变量中，比如:

```
$green: #7ED321;
$red: #ff595e;
$yellow: #F8E81C;
$blue: #4990E2;
$purple: #DB61F4; 
```

现在，我把它们放在一个 Sass 地图中，就像这样:

```
$colours: ( 
  green: #7ED321,
  red: #ff595e,
  yellow: #F8E81C,
  blue: #4990E2,
  purple: #DB61F4,
); 
```

这种看似微小的差异的原因是使用 Sass 指令编写循环的能力。但是在我们开始之前，我想简单介绍一下 Sass maps 中所有可用的函数。

| 功能 | 结果 |
| --- | --- |
| *map-get($map，$key)* | 返回与给定键关联的映射中的值。 |
| *地图合并($map1，$map2)* | 将两个地图合并成一个新地图。 |
| *地图-移除($map，$keys…)* | 返回移除了键的新地图。 |
| *地图键(＄map)* | 返回映射中所有键的列表。 |
| *地图值(＄map)* | 返回地图中所有值的列表。 |
| *map-has-key($map，$key)* | 返回映射是否有与给定键关联的值。 |
| *关键词(＄args)* | 返回传递给采用可变参数的函数的关键字。 |

此外，您还可以将所有列表函数用于 Sass 映射。这里有更多你可以用萨斯地图做的事情。

| 功能 | 结果 |
| --- | --- |
| *长度(＄list)* | 返回列表的长度。 |
| *第 n 个($list，$n)* | 返回列表中的特定项目。 |
| *set-n(＄list，＄n，＄value)* | 替换列表中的第 n 项。 |
| *join(＄list 1，list2，[＄separator])* | 将两个列表合并成一个列表。 |
| *追加($list1，$val，[$separator])* | 将单个值追加到列表的末尾。 |
| *zip(＄lists…)* | 将几个列表合并成一个多维列表。 |
| *索引(list，＄value)* | 返回一个值在列表中的位置。 |
| *列表-分隔符($list)* | 返回列表的分隔符。 |

## Sass 控制指令

我遇到的每一门编程入门课程或教程都涉及到了指令。给你的程序注入一些逻辑是很基本的。也帮助我们编写更少的代码。CSS 本身不支持控制指令，因为它是一种样式表语言。

### @如果

`@if`指令评估 SassScript 表达式，如果它返回除了`false`或`null`之外的任何内容，则使用声明中嵌套的样式。在`@if`语句之后可以跟随`@else if`语句或者只是一个`@else`语句，这样就可以在你的风格中加入某种逻辑。

例如，这是一个根据用作函数参数的颜色值返回深色文本颜色或浅色文本颜色的函数。

```
@function text-colour($colour) {
  @if (lightness($colour) > 49) {
    @return #000; // Lighter background, return dark color
  } @else {
    @return #fff; // Darker background, return light color
  }
}

// Usage
.element {
    background-color: #000;
    color: text-colour(#000);
} 
```

### @为

我们可以使用`@for`指令编写循环。循环运行的次数由计数器变量决定。有两种方法来编写这个指令，注意使用的关键字的不同。

```
// includes the values of <start> and <end>
@for $var from <start> through <end>

// runs up to but not including the value of <end>
@for $var from <start> to <end> 
```

counter 变量也可以在 CSS 声明中使用，对于数字递增的选择器或第 n 个子选择器很有用。

```
@for $i from 1 through 3 {
  .item-#{$i} { 
    width: 2em * $i; 
  }
} 
```

```
/* compiles into */
.item-1 {
  width: 2em; 
}

.item-2 {
  width: 4em;
}

.item-3 {
  width: 6em;
} 
```

### @虽然

如果不想写 for 循环，SassScript 允许写 while 循环。在表达式计算为假之前，`@while`指令中的嵌套样式将被输出。比如:

```
$i: 9;
@while $i > 0 {
  .item-#{$i} { width: 1em * $i; }
  $i: $i - 3;
} 
```

```
/* compiles into */
.item-9 {
  width: 9em;
}

.item-6 {
  width: 6em;
}

.item-3 {
  width: 3em;
} 
```

老实说，我以前从未使用过`@while`指令，因为我还没有遇到它的用例。

### @每

这是我经常使用的，也是这篇文章的重点。`@each`指令非常灵活，它可以为列表或映射中的每一项设置变量`$var`的值，然后使用该变量输出声明中的样式。一般的语法是:

```
@each $var in <list or map> 
```

问题是，它可以接受多个变量，非常适合在 Sass 映射中使用。例如:

```
$colours: ( 
  green: #7ED321,
  red: #ff595e,
  yellow: #F8E81C,
  blue: #4990E2,
  purple: #DB61F4,
);

:root {
  @each $name, $colour in $colours {
    --#{$name}: $colour;
  }
} 
```

```
/* compiles into */
:root {
  --green:#7ed321;
  --red:#ff595e;
  --yellow:#f8e81c;
  --blue:#4990e2;
  --purple:#db61f4
} 
```

## 过度工程多？

尽管如此，我最初的问题是重用相同的颜色 Sass map 来为我的元素集生成背景颜色，同时使用基于地图中颜色数量以编程方式生成的第 n 个子选择器。这意味着如果我在地图上添加更多的颜色，我不需要改变其他任何东西。

这是在 2007 年实现的，我想出这个来实现我最近部署的[演示登陆页面](//%7B%7B%20site.url%20%7D%7D/demos/) :

```
@each $name, $colour in $colours {
  section:nth-child(#{length($colours)}n + #{index(($colours), ($name $colour))}) {
    background-color: $colour;
    background-color: colour($name);
    mix-blend-mode: color-dodge;

    a:hover {
      background-color: #444;
      color: $colour;
      color: colour($name);
    }
  }
} 
```

让我试着解释一下。`#{length($colours)}`给出了地图中颜色的总数，而`#{index(($colours), ($name $colour))}`返回了地图中指定颜色的索引。它们一起构成了第 n 个子选择器`ð“n + ð“`，其中-是地图中颜色的数量。

## 包装完毕

这是正确的做事方式吗？我不知道。有用吗？是的。所以我对此有点纠结。虽然学习更多关于 Sass 函数的知识是一个有趣的练习。如果你有任何反馈，我很想听听。

我相当有信心有一个更好的方法来完成这件事，正如我最新的马来西亚国旗的[代码笔所证明的那样，独一无二的](https://codepen.io/huijing/full/vZeyVP/)[安娜·都铎](https://thebabydino.github.io/)叉开了我的笔，[让它变得好了 10 倍](https://codepen.io/thebabydino/full/VWMzqM/)！她还制作了过程的[截屏。 *#internetisawesome*](https://www.youtube.com/watch?v=VlLmUNbwdMQ)

## 进一步资源

*   [Sass 官方文档](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)
*   [Lea Verou: CSS 变量:var(–subtitle)](https://www.youtube.com/watch?v=kZOJCVvyF-4)
*   [正确使用 CSS 变量](https://madebymike.com.au/writing/using-css-variables/)
*   本地范围的 CSS 变量:什么，如何，为什么
*   [Codrops CSS 引用:自定义属性](https://tympanus.net/codrops/css_reference/custom-properties/)

*最初发表于 2017 年 6 月 30 日[www.chenhuijing.com](https://www.chenhuijing.com/blog/overengineering-sass-maps/)。*