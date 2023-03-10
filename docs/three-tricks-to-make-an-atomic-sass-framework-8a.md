# 构建原子 Sass 框架的三个技巧

> 原文：<https://dev.to/maxwell_dev/three-tricks-to-make-an-atomic-sass-framework-8a>

作为一名前端开发人员，我编写可管理 CSS 的首选方式是使用 BEM 命名架构。它有助于创建特定的、有作用域的类名，这些类名易于识别并避免大量的样式重叠。有一段时间，这似乎是我写 CSS 的唯一可持续的方式。

然后我读了约翰·波拉切克的“重新思考 CSS”幻灯片，了解了原子 CSS。我非常鼓励阅读整本书(不管你的反应如何)，但简而言之，它几乎完全是用基于视觉功能的辅助类来编写 CSS 的。我对此非常感兴趣，以至于我决定用 Sass 编写[我自己的原子 CSS 框架，并想分享我是如何克服我遇到的一些最大挑战的。](https://github.com/maxx1128/atomic-sass)

但是首先，让我写这篇文章并不是为了讨论原子 CSS 是不是一个好主意。我对被要求为另一个人的想法辩护不感兴趣，因为愤怒的开发人员没有被告知他们必须使用它...).我甚至还不知道我个人更喜欢哪一个。这篇文章只是看了一些我认为对其他开发者有用的 Sass 技巧。我不会回应为什么原子 CSS 很烂的叫嚣；请在别处做那件事。

完成这些免责声明后，让我们开始真正的文章吧！

### 1)创建全局类命名空间

第一步(也是最简单的一步)总是创建一个名称空间。这对任何框架来说都不是必需的，但是为了避免与其他框架冲突，这是值得的。

我最近才意识到，最好在一个地方定义一个全局名称空间。尤其是对于原子 CSS，它将被用在几乎所有的类上。对于 Sass，这意味着使用变量！

```
$g-nmsp: ".zz";

// The `$g-` prefix is a reminder that it's a global Sass variable. 
```

Enter fullscreen mode Exit fullscreen mode

定义了名称空间之后，一点点插值的魔力就让它派上了用场:

```
#{$g-nmsp}-relative { position: relative; }
#{$g-nmsp}-absolute { position: absolute; }
#{$g-nmsp}-static { position: static; }
#{$g-nmsp}-fixed { position: fixed; } 
```

Enter fullscreen mode Exit fullscreen mode

并且输出是这样的:

```
.zz-relative { position: relative; }
.zz-absolute { position: absolute; }
.zz-static { position: static; }
.zz-fixed { position: fixed; } 
```

Enter fullscreen mode Exit fullscreen mode

万岁！展望未来，现在很容易改变我的助手类 army 的名称空间。

### 2)创建基础样式和实用程序类

虽然我可以接受原子 CSS 使用许多类，但我担心它太多了。一个例子是排版——我可以为每个页眉的字体大小、边距、字体系列创建类。但是我每次都必须将这些类应用到每个头中！

我的折衷方案是将每个属性**应用于基本元素和一个助手类。通过这种方式，我可以在为相同的值创建辅助类的同时创建标准的标题样式。**

组织这一切的最好方法是什么？萨斯地图！

##### 2.1)定义排版值

第一步是识别基本的排版元素——标题标签、段落标签和其他任何元素。然后，我根据样式属性将它们放入嵌套地图中。下面是一个字体大小的例子:

```
$typography-map: (
  font-size: (
    h1: decimal-round(modular-scale(5), 2),
    h2: decimal-round(modular-scale(4), 2),
    h3: decimal-round(modular-scale(3), 2),
    h4: decimal-round(modular-scale(2), 2),
    h5: decimal-round(modular-scale(1), 2),
    h6: decimal-round(modular-scale(0), 2),
    large: decimal-round(modular-scale(0), 2),
    p: decimal-round(modular-scale(0), 2),
    small: decimal-round(modular-scale(-1), 2),
    tiny: decimal-round(modular-scale(-2), 2)
  ),

  // ... Line-heights, margins, and more below
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 2.2)从这些值生成两种样式

这是生成基本和辅助样式值的基本模式，同样以字体大小为例。

```
@each $label, $font-size in map-get($typography-map, font-size) {
  #{$label},
  #{$g-nmsp}-font-size-#{$label} { font-size: $font-size; }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出正是我想要的:

```
h1,
.zz-font-size-h1 { font-size: 3.05rem }

h2,
.zz-font-size-h2 { font-size: 2.44rem }

h3,
.zz-font-size-h3 { font-size: 1.95rem }

// And it continues... 
```

Enter fullscreen mode Exit fullscreen mode

对所有的排版样式都这样做，基本样式层在一起，我就有了好看的默认标题。我还为这些相同的属性提供了助手类，以便在需要时覆盖标题样式或在其他地方使用。

你可能想知道我为什么使用 Sass 地图。这是因为它们是将`property/value`对按类别组织在一起的有效方式。我会知道我所有的价值观在哪里，它们不会泄露到其他任何东西里。另外，很容易循环遍历它们并以一种干巴巴的方式生成 CSS。

### 3)生成基础和响应间隔类

最大的挑战之一是创建响应性的类。经典的例子是有或没有断点都可以应用的边距值，比如:

```
.zz-mt-base { margin-top:1rem }

@media (min-width: 30rem) {
  .zz-mt-base-sm { margin-top : 1rem }
}

@media (min-width: 50rem) {
  .zz-mt-base-md { margin-top : 1rem }
}

@media (min-width: 75rem) {
  .zz-mt-base-lg { margin-top : 1rem }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将一个`1rem`的`base`值添加到一个元素的上边距，简称为`mt`。我也可以只在某些断点之后这样做。这同样适用于其他边距值，如`double`或`half`，其他边距位置，如`bottom`，以及我的所有断点。

听起来很简单，但是挑战在于以一种干燥的方式来做这件事。

##### 3.1)定义断点

我处理更复杂问题的方法总是从小处着手。首先是定义断点。

我再一次求助于萨斯地图！这里我还有一个 mixin 用于从地图中获取值。

```
$breakpoint-map: (
  xs: 0px,
  sm: rem(480px),
  md: rem(800px),
  max: rem(1200px)
);

@mixin larger-than($point-name) {
  @if ($point-name != 'xs') {
    $width: map-get($breakpoint-map, $point-name);

    @media (min-width: $width) { @content; }
  } @else {
    @content;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，制作响应式既简单又有语义:

```
.some-selector {
    // Base styles go here!

    @include larger-than(md) {
        // Styles for screens larger than 800px go here!
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能想知道 0px 的断点。这甚至不是一个断点，mizing 忽略了它，所以它似乎没有用。坚持这个想法，因为我将在几个步骤中解释为什么。

##### 3.2)定义间距值

下一步是创建边距单位...是的，一张萨斯地图。

我通常的方法是定义一个基本间距值，然后做简单的数学和 Sass 函数来创建更大和更小的值。这使得间距值易于更改，更具凝聚力，但仍允许覆盖(如`none`值)。

```
$g-base-spacing: rem(16px);

$spacing-map: (
  none   : 0,
  quad   : decimal-round(($g-base-spacing * 4), 2),
  triple : decimal-round(($g-base-spacing * 3), 2),
  double : decimal-round(($g-base-spacing * 2), 2),
  oneHalf : decimal-round(($g-base-spacing * 1.5), 2),

  base   : decimal-round($g-base-spacing, 2),

  half   : decimal-round(($g-base-spacing / 2), 2),
  third  : decimal-round(($g-base-spacing / 3), 2),
  quart  : decimal-round(($g-base-spacing / 4), 2)
); 
```

Enter fullscreen mode Exit fullscreen mode

万岁，我所有的间距值都设置好了！不需要 mixin 或函数来拉它们，这也将在下一步解释。

##### 3.3)制作响应式效用类。

现在是有趣的部分。我创建这些类的理想解决方案做了几件事:

1.  将地图数据用于断点和间距值
2.  只需要编写一次不同的类名
3.  创建带断点和不带断点的实用程序类
4.  仅向响应类添加断点标签

还有一些样式需要断点数据，但不需要间距数据。例如，如果我希望在某些断点上的边距是`auto`。

最后的答案比较笨重，我就一步一步来。

首先我浏览了两张地图。从断点循环开始很重要——`margin: auto`类可以在那里定义，需要间隔的类在两个循环中都有。

```
@each $bp-label, $bp in $breakpoint-map {

 @include larger-than($bp-label) {
    @each $label, $length in $spacing-map {
      // Margin classes with spacing values go here
    }
  }

  // Auto margin classes go here
} 
```

Enter fullscreen mode Exit fullscreen mode

第二步是只为带有断点的间隔类创建标签。我的第一个版本定义了两次相同的间距类——一次有断点标签，一次没有。但是我找到了一个需要那个`xs`断点的干燥机解决方案。技巧是给每个类添加断点标签，但对于没有断点的类，使其为空字符串。

```
@each $bp-label, $bp in $breakpoint-map {

 $bp-label-final: '';

 @if ($bp-label != 'xs') { $bp-label-final: '-' + $bp-label; }

 @include larger-than($bp-label) {
    @each $label, $length in $spacing-map {
      // Margin classes that use the different spacing values go here
    }
  }

  // Auto margin classes that don't need the different spacing lengths go here
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我所需要的就是在类名中使用这个`$bp-label-final`变量的一个循环。它不向基类添加任何东西，而是向响应类添加所需的标签。我也可以在`margin: auto`类中使用这个技巧。我可以一次定义所有不同的保证金实用程序类，并得到我需要的一切！

```
@each $bp-label, $bp in $breakpoint-map {

  $bp-label-final: '';

  @if ($bp-label != 'xs') { $bp-label-final: '-' + $bp-label; }

  @include larger-than($bp-label) {
    @each $label, $length in $spacing-map {
      // Margin
      #{$g-nmsp}-m-#{$label}#{$bp-label-final} { margin: $length; }
      #{$g-nmsp}-mt-#{$label}#{$bp-label-final} { margin-top: $length; }
      #{$g-nmsp}-mr-#{$label}#{$bp-label-final} { margin-right: $length; }
      #{$g-nmsp}-mb-#{$label}#{$bp-label-final} { margin-bottom: $length; }
      #{$g-nmsp}-ml-#{$label}#{$bp-label-final} { margin-left: $length; }

      #{$g-nmsp}-mx-#{$label}#{$bp-label-final} {
        margin-right: $length;
        margin-left: $length;
      }

      #{$g-nmsp}-my-#{$label}#{$bp-label-final} {
        margin-top: $length;
        margin-bottom: $length;
      }
    }
  }

  // Auto margin classes that don't need the different spacing lengths

  #{$g-nmsp}-mx-auto#{$bp-label-final} {
    margin-right: auto;
    margin-left: auto;
  }

  #{$g-nmsp}-my-auto#{$bp-label-final} {
    margin-top: auto;
    margin-bottom: auto;
  }

  #{$g-nmsp}-mt-auto#{$bp-label-final} { margin-top: auto; }
  #{$g-nmsp}-mr-auto#{$bp-label-final} { margin-right: auto; }
  #{$g-nmsp}-mb-auto#{$bp-label-final} { margin-bottom: auto; }
  #{$g-nmsp}-ml-auto#{$bp-label-final} { margin-left: auto; }
} 
```

Enter fullscreen mode Exit fullscreen mode

找到解决方案了！现在，我有了一个简单的方法，可以从这个部分的开头获得输出。我可以很容易地改变断点、间距值或类语法，而不需要任何不必要的重复。更好的是，这个技巧也可以用于其他样式，比如填充实用程序类！

#### 结论

这三个技巧一起解决了我的原子 Sass 框架的大部分挑战。剩下的大部分是编写独立的类并决定包含什么。因此，对于其他打算写自己的书的人来说，记住它们是很有用的。

最后，我将谈谈我自己对原子 CSS 的感受。在读到它之后，我有一种类似的本能的排斥，许多其他开发者都有。对我来说，这是因为 BEM 帮了我这么多，不想“抛弃它。”此外，向页面添加大量的类会显得笨拙和混乱。这是一颗难以下咽的药丸。

但是看到 CSS 文件大小的好处，“单一责任原则”，以及更新和定制页面样式的便利性，我的思维更加开阔了。至少我认为对于某些项目来说，这是一个有效的架构，比如那些有很多开发人员的 CSS 技能比较粗糙的项目。

这种认识首先导致我做出这样的原子 Sass。如果我真的完全理解了原子 CSS，我会准备好的。