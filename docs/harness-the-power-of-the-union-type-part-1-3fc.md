# 利用联合类型的力量(第 1 部分)

> 原文：<https://dev.to/marshallformula/harness-the-power-of-the-union-type-part-1-3fc>

最初发布于[marshal formula . codes](https://marshallformula.codes/harness-the-power-of-the-union/)

* * *

到目前为止，我已经在[工作](https://volumeintegration.com)中使用 elm 大约一年半了。Elm 是我第一次使用纯函数式语言，现在我仍然被深深地吸引住了。我仍然只是开始触及函数式编程所能提供的皮毛。我不断发现这些新的洞察力宝石，并建立新的认知联系。最近的一个发现是[联合型](https://guide.elm-lang.org/types/union_types.html)的内在动力。

## 澄清一点

从技术上讲 Elm 使用的结构是一个[标记的联合类型](https://en.wikipedia.org/wiki/Tagged_union):

> 一种用于保存值的数据结构，该值可以采用几种不同但固定的类型。任何时候都只能使用其中一种类型，一个标记字段会明确指出使用的是哪一种类型

另外——其他[语言](https://wiki.haskell.org/Algebraic_data_type)称之为[代数数据类型](https://en.wikipedia.org/wiki/Algebraic_data_type)(或 ADT)。

## 如何使用

在我们开始一些具体的例子之前——我想澄清一些误解，这些误解是我第一次学习 elm 中的联合类型时产生的。以下是联合类型的外观:

```
type Shape
    = Cirle
    | Rectangle
    | Triangle 
```

基于上面的定义和这个例子——你可能会(像我一样)认为这基本上只是一个枚举。这是一个不错的起点。您肯定可以像使用枚举一样使用联合类型，但是您会错过它提供的真正功能。

在讨论联合类型时，首先讨论一些术语可能会有所帮助。在上面的例子中，类型是一个`Shape`，每个指定的值`Shape`是**类型构造函数**。

```
type Shape     -- Type definition
    = Circle   -- Circle Shape constructor
    | Rectangle   -- Rectangle Shape constructor
    | Triangle -- Triangle Shape constructor 
```

来自面向对象的背景——这种术语的描述是真正让我开始喜欢的东西。构造函数用于创建一个`Shape`值——就像一个类(可以有多个构造函数)使用构造函数来实例化一个类对象。这只是相似性的终结，所以不要把这个概念带得太远，但也许这种联系会引发一些进一步的理解，就像我一样。

所以让我们开始使用新的`Shape`类型:

```
makeCircle : Shape
makeCircle =
    Circle

makeRectangle : Shape
makeRectangle =
    Rectangle

-- you get it 
```

好的。诚然，这是相当乏味的。但是它确实演示了如何使用`Shape`的构造函数来创建新的`Shape`类型。让我们把它变得更有趣一点。

### 标记有效载荷

到目前为止，我们的`Shape`只不过是一个枚举。如果你真的继续阅读上面提到的[维基百科条目](https://en.wikipedia.org/wiki/Tagged_union)，你可能会看到这个片段:

> 枚举类型可以被看作是一种退化的情况:单元类型的标记联合。它对应于一组空构造函数，可以作为一个简单的标记变量来实现，因为除了标记的值之外，它不包含任何其他数据。

最后一句话是否暗示了与枚举类型不同——联合类型的标记可以保存数据？为什么是的。接得好。

这就是工会类型的真正力量发挥作用的地方。联合类型的构造函数可以指定构造该版本联合类型所需的附加数据(比如 OO 中构造函数的参数)。现在我们可以让我们的`Shape`更有用一点。

```
type Shape
    = Circle Int
    | Rectangle Int Int
    | Triangle Int 
```

整洁！现在，`Circle`可以存储它的半径，`Rectangle`可以存储它的高度和宽度，`Triangle`可以存储它的边长(我们现在保持简单，只允许等边三角形)。

现在——你可能会说，`Rectangle`的两个构造函数参数是宽度和高度并不完全明显——因为我们只看到它有两个`Int`值。这里有一个提示...`alias`是你的朋友:

```
type alias Radius = Int
type alias Height = Int
type alias Width = Int
type alias Side = Int

type Shape
    = Circle Radius
    | Rectangle Height Width
    | Triangle Side 
```

对于我们这个简单的例子来说，这可能有点过头了——但是它说明了构造函数的定义可以非常明确。每当我的构造函数参数含糊不清或令人困惑时，我都会使用这种技术。我发现它非常有价值。

我们最好修正我们的 creator 函数，以反映我们的新类型构造函数:

```
makeCircle : Radius -> Shape
makeCircle radius =
    Circle radius

makeRectangle : Height -> Width -> Shape
makeRectangle height width =
    Rectangle height width

makeTriangle : Side -> Shape
makeTriangle side =
    Triangle side 
```

* * *

好吧-说到真正的好东西。现在我们有了不同的类型构造函数——我们需要一种方法来检索存储在类型中的数据。为此，我们需要使用[模式匹配](https://gist.github.com/yang-wei/4f563fbf81ff843e8b1e)。

因此，让我们创建一个函数来寻找给定形状的周长:

```
pi = 3 -- rounding for simplicity

findPerimeter : Shape -> Int
findPerimeter shape =
    case shape of 
        Circle radius -> 
            2 * pi * radius

        Rectangle height width ->
            (height * 2) + (width * 2)

        Triangle side ->
            (side * 3) -- again equilateral triangle for simplicity

-- let's try it out

findPerimeter (makeCircle 5)        -- 30

findPerimiter (makeRectangle 2 10)  -- 24

findPerimeter (makeTriangle 6)      -- 18 
```

很可爱吧？但是，当我们得到一个支持新形状的新需求时，会发生什么呢...说个`Hexagon`。那么，让我们添加一个新的构造函数:

```
type Shape
    -- other types already defined here
    | Hexagon Side -- equal sides for simplicity 
```

很简单。现在，也许你认为我们已经展示了工会类型所能提供的所有优点？好消息...

[![ron](img/d94db28ec1a0d44855d9094344c79052.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oElUlf91--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://marshallformula.codes/conteimg/2017/11/ron.gif)

没错。更棒的朝你的方向去了。你看，我们刚刚添加了一个新的类型构造函数，但是我们忘了在`findPerimeter`函数中考虑它。你看——不像在其他语言中，或者通过使用枚举——我们友好的 elm 编译器不会让这种情况发生。

```
-- MISSING PATTERNS ---------------------------------------------

This `case` does not have branches for all possibilities.

|>    case shape of
|>        Circle radius ->
|>            2 * pi * radius
|>
|>        Rectangle height width ->
|>            (height * 2) + (width * 2)
|>
|>        Triangle side ->
|>            (side * 3)

You need to account for the following values:

    Hexagon _

Add a branch to cover this pattern! 
```

多么令人难以置信的有用和信息丰富的编译器！它把我们从愚蠢中拯救了出来。无论何时使用联合类型，Elm 编译器都会检查以确保您已经涵盖了导致更少 [footguns](https://en.wiktionary.org/wiki/footgun) 的所有可能性，从而减少 bug。

* * *

在第 2 部分中，我将从 union 类型中提取更多的魔力。当与递归、封装和多态等概念结合使用时，我将展示使用联合类型的一些独特优势。

这里讨论的所有代码都可以在[这个 ellie](https://ellie-app.com/sF8YfTW2ca1/0) 中获得。