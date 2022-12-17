# SASS 控制指令

> 原文：<https://dev.to/sarah_chima/sass-control-directives-6hk>

如果您一直在浏览 Sass 代码或阅读关于 Sass 的文章，您可能会遇到 Sass 控制指令。这些控制指令是`@if`、`@for`、`@each`和`@while`。它们在 Sass 中是如何发挥作用的？它们是如何使用的？什么时候使用它们比较合适？本文通过讨论这些指令来回答这些问题以及更多问题。不过，首先让我们对 Sass 控制指令做一个简要的概述。

在 Sass 中，控制指令和表达式用于仅在某些情况下包含样式，或者多次包含不同的相同样式。控制指令是一个高级特性，主要用于 mixins。除非您正在处理一个大型项目，否则您可能不需要使用它们。然而，了解它们仍然很重要，因为它们可能会在某个时候派上用场。让我们来讨论这些指令。

### **@if**

 **`@if`指令接受一个 Sass 表达式，如果表达式的计算没有返回`false`或`null`，则执行指令下的样式块。我们用一个简单的例子来说明这一点。

```
 h3 {
        @if(2+2 == 4) { color: black;}
        @if(true) {font-size: 24px;}
        @if(1 > 4) {color: blue;}
        @if(false) {font-size: 30px;}   
    } 
```

Enter fullscreen mode Exit fullscreen mode

将编译到

```
 h3 {
        color: black;
        font-size: 24px; } 
```

Enter fullscreen mode Exit fullscreen mode

前两个表达式被评估为真，因此添加样式，而另外两个被评估为假，因此不添加它们的样式。

如果表达式的计算结果不是`true`，也可以提供其他选项。`@else if`和`@else`语句就是用来做这个的。因此，如果`@if`语句失败，则按照顺序尝试`@else if`语句，直到一个语句成功或到达`@else`。这里有一个例子。

```
 @mixin heading($size) {
        @if($size == large) {
            font-size: 40px;
        } @else if ($size == medium) {
            font-size: 24px;
        } @else if ($size == small) {
            font-size: 18px;
        } @else {
            font-size: 16px;
        }
    }

    h2 {
        @include heading(large);
    }
    h4 {
        @include heading(medium);
    }
    p {
        @include heading(hi);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 h2 {
        font-size: 40px; }

    h4 {
        font-size: 24px; }

    p {
        font-size: 16px; } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，只有当表达式返回`true`时，才会执行每个指令后的样式块。如果所有的表达式都失败了，就像在`p`的情况下，就使用`else`语句下的样式块。接下来，我们将讨论`@for`指令。

### **@表示指令**

`@for`指令用于循环输出样式。这个循环有一个开始和结束值。`@for`指令有两种形式:你可以循环`through`开始和结束值，或者从开始`to`结束值开始循环。两者有细微的区别。`through`循环并包含终点，`to`不包含终点值。例子将使这一点更加清楚。

```
 @for $i from 1 through 4 {
        .box-#{$i} { width: 10 * $i;}
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 .box-1 {
        width: 10; }

    .box-2 {
        width: 20; }

    .box-3 {
        width: 30; }

    .box-4 {
        width: 40; } 
```

Enter fullscreen mode Exit fullscreen mode

变量`$i`可以是您决定使用的任何名称。该变量用于跟踪范围内的循环。请注意，上面的 SCSS 循环是从 1 到 4，包括 4。下面是用`to`代替时的情况。

```
 @for $i from 1 to 4 {
        .box-#{$i} { width: 10 * $i;}
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 .box-1 {
        width: 10; }

    .box-2 {
        width: 20; }

    .box-3 {
        width: 30; } 
```

Enter fullscreen mode Exit fullscreen mode

这次不包括最后一个数字`4`。这是因为在变量`$i`到达`4`之后，代码不会像在*到*的情况下那样执行。让我们转到下一个指令。

### **@每个**

`@each`指令用于遍历列表或映射，而不是像`@for`那样开始和结束值。它的语法是`@each $var in <list>`，其中`$var`可以是任何变量的名称，如`$name`或`$animal`。该变量被设置为列表中的每一项，并使用该变量处理指令下的样式块。`<list>`是一个返回列表的 SassScript 表达式。让我们使用这个指令。

```
 @each $place in lagos, newyork, paris {
        .place-#{$place} {
            background-image: url("img/place/#{$place}" )
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 .place-lagos {
        background-image: url("img/place/lagos"); }

    .place-newyork {
        background-image: url("img/place/newyork"); }

    .place-paris {
        background-image: url("img/place/paris"); } 
```

Enter fullscreen mode Exit fullscreen mode

**@每个有多个变量**

指令还可以利用多个变量来处理一个由列表组成的列表。听起来很困惑？看看下面的例子。

```
 @each $place, $color, $position in (lagos, blue, fixed), 
                                    (newyork, black, relative),
                                    (paris, gray, absolute) {
        .place-#{$place} {
            background-image: url("img/place/#{$place}" );
            border: 2px solid $color;
            position: $position;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

注意使用了不同的变量。这些变量中的每一个都被设置为每个子列表中的相应项目，并且使用这些变量来处理样式块。所以这将编译成:

```
 .place-lagos {
        background-image: url("img/place/lagos");
        border: 2px solid blue;
        position: fixed; }

    .place-newyork {
        background-image: url("img/place/newyork");
        border: 2px solid black;
        position: relative; }

    .place-paris {
        background-image: url("img/place/paris");
        border: 2px solid gray;
        position: absolute; } 
```

Enter fullscreen mode Exit fullscreen mode

**@各配一张地图**

让我们使用带有地图的`@each`指令。映射被视为一个对列表，因此我们还必须使用两个变量，在处理指令时，这些对将被设置为这两个变量。这里有一个例子:

```
 $animals: ( animal1:fish, animal2:rat, animal3:monkey);

        @each $key, $animal in $animals  {
            .#{$animal}-avatar {
                background-image: url('/img/#{$animal}.png');
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 .fish-avatar {
        background-image: url("/img/fish.png"); }

    .rat-avatar {
        background-image: url("/img/rat.png"); }

    .monkey-avatar {
           background-image: url("/img/monkey.png"); } 
```

Enter fullscreen mode Exit fullscreen mode

让我们最后考虑最后一个指令:`@while`指令。

### **@而**

就像其他控制指令一样，`@while`指令接受一个表达式并执行嵌套的样式块，只要表达式的计算结果不是`false`。它类似于`@for`指令，但是它可以用来执行比`@for`指令更复杂的循环。

当使用`@while`指令时，使用一个带有设定值的变量，而不是一个数值范围。让我们重复我们的`@for`指令示例，但是这次使用`@while`。

```
 $i: 1;
    @while $i < 4 {
        .box-#{$i} { width: 10 * $i;}
        $i: $i + 1;
    } 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码可以理解为当变量`$i`不大于 4 时，执行嵌套的样式块。这个值在嵌套的样式块中增加，直到它不再小于 4，这是它返回 false 的条件，然后它停止。这将编译成

```
 .box-1 {
        width: 10; }

    .box-2 {
        width: 20; }

    .box-3 {
        width: 30; } 
```

Enter fullscreen mode Exit fullscreen mode

使用`@while`指令时，如果不提供失败的条件，循环将永远运行。在我们的例子中，我们反复增加了`$i`的值，所以条件被设置为在某个时刻返回`false`。

这就是我们的控制指令。

有什么问题或补充吗？请留言评论。

感谢您的阅读。:)**