# 点波:一个纯脚本短篇小说

> 原文：<https://dev.to/awfulaxolotl/dot-waves-a-purescript-short-story-5d8a>

如果你还没有检查过 PureScript，那么你完全应该检查一下。它是 Haskell 的精神之子，但目前专注于编译成 Javascript。可读的 Javascript 代码！

我不打算深入讨论这门语言的整体，因为已经有了令人惊叹的学习资源。如果你喜欢 React、Redux、ClojureScript 和 Elm 正在做的事情，那就看看吧！

这篇文章是 PureScript 项目开发过程中的一个例子。这是在用 PureScript 创建一个小项目时，对开发思路的真实观察。我刚开始接触这门语言，很惊讶地发现很少有类似的资源。希望它对你有用，或者你只是和波浪点有一种特殊的关系。嘿，这里没有评判。

让我们继续做一些点。请享用，但不要太多。

# Uno 圆点

[https://codepen.io/awfulaxolotl/embed/PZRLQV/?height=600&default-tab=result&embed-version=2](https://codepen.io/awfulaxolotl/embed/PZRLQV/?height=600&default-tab=result&embed-version=2)

最好的开始方式是从小处着手。让我们从画布上一个跳动的点开始。

首先是 HTML。

```
<!DOCTYPE html>
<html>
  <head>
    Dot Waves
  </head>
  <body>
    <canvas width="800" height="500" id="dot-waves"></canvas>
    <script src="/app.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

唷，到目前为止没什么可搞砸的。看上去不错，看上去不错。但是空白的画布是很无聊的画布，所以我们来变点魔法吧。

```
main :: forall eff. Eff ( timer :: Timer, dom :: DOM, canvas :: Canvas | eff) Unit
main = do
  Just canvas <- getCanvasElementById "dot-waves"
  ctx <- getContext2D canvas 
```

Enter fullscreen mode Exit fullscreen mode

第一步是获取 HTML 画布上下文对象。PureScript 是一种重视纯粹性的函数式语言，所以接触外部世界的函数被包装在`Eff`上下文中。这就是为什么主函数的类型(命令式语言中的返回类型)是`Unit` (nothing)但被包装在`Eff`中的原因。解开`Eff`中的一个价值，就是承诺接触外部世界，放弃完全的纯粹。

真扫兴。纯洁听起来不错，但是正如我爸爸曾经说过的，“你必须变得有点脏才能完成任何事情，孩子。”实际上他从来没有说过，但是不要让真相妨碍相关的轶事。

无论如何，我们现在有一个画布背景。让我们在 Dot Land 停一下，写一个函数来渲染一个点，给出它的坐标和半径。

```
renderDot :: Number -> Number -> Number -> Drawing
renderDot x y r = 
  filled (fillColor color) dot
  where
    dot :: Shape
    dot = circle x y r

    color :: Color
    color = rgb 200.0 220.0 210.0 
```

Enter fullscreen mode Exit fullscreen mode

所以我们有一个函数，它接受三个数字(x，y，半径)并返回一个点的图形。`filled`是来自 [purescript-drawing](https://github.com/paf31/purescript-drawing/blob/master/docs/Graphics/Drawing.md#filled) 库的一个函数，它采用填充样式和形状来创建一个图形。俏皮！

下一步是什么？我们有画布，圆点…哦！当然是*脉动的*。让我们把脉。最简单的方法是使脉动成为当前时间的函数。如果你想象这样一个函数的二维图形，它看起来就像一个正弦波。所以还是用`sin`函数吧！

```
pulse :: forall eff. Number -> Number -> Eff (dom :: DOM, timer :: Timer | eff) (Signal Number)
pulse min max = do
  nowMs <- animationFrame
  return $ nowMs ~> amp
    where secs ms = ms / 1000.0
          unitAmp x = ((sin x) + 1.0) / 2.0
          amp ms = (unitAmp $ secs ms) * (max - min) + min 
```

Enter fullscreen mode Exit fullscreen mode

我知道你在想什么，“这不是简单的 sin 函数，有那么多废话干什么？”抱歉。先不要给我发一封愤怒的电子邮件；请听我解释！

首先，检查底部定义的`amp`函数。从 [Unix 纪元](https://en.wikipedia.org/wiki/Unix_time)开始需要几毫秒，并返回该瞬间的脉动值。

太好了，那这些多余的废话是怎么回事？嗯，实际上那个“多余的垃圾”有个名字叫非常感谢。她的名字是“信号”，她使我们能够考虑现在和未来所有脉动的价值。

说 wut。

在这里忍耐一下。想象一下一个无限长的数据队列，您无法确定下一项何时可用。简而言之这是一个信号，但我猜真正的坚果壳不可能包含无限的东西。不管怎样，你抓到我了。

现在我们知道了什么是信号，让我们后退一步，回顾一下我们最初想要的是什么。我们想要一个随时间脉冲的点。如果我们有一个无限长的 Unix 历元时间列表的信号，我们可以将`amp`函数应用于每个元素，以获得每个时刻的无限长的脉冲值列表。我们可以简单地考虑在这个“脉冲信号”中提供给我们的最新项目，以获得当前的脉冲值。

这正是这里正在发生的事情:

```
nowMs <- animationFrame
return $ nowMs ~> amp 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`animationFrame`，一个 Unix 纪元时间的信号，其值以屏幕刷新率反馈给我们。`nowMs ~> amp`是映射到这个无限信号中每个元素的`amp`函数。然后我们在`Eff`的上下文中返回“脉冲信号”，因为我们必须接触浏览器环境来请求`animationFrame`。

一切都很顺利！我们已经有了画布上下文、点渲染器、随时间变化的脉冲值，还有一个关于 nutshells 的小的存在危机。现在是时候把一切联系在一起了。

```
rendering :: forall eff. Context2D -> Number -> Eff (canvas :: Canvas | eff) Unit
rendering ctx n = do
  clearRect ctx boundaries
  render ctx $ renderDot 400.0 250.0 n 
```

Enter fullscreen mode Exit fullscreen mode

`rendering`拿起画布，在画布上渲染一个点，并从`renderDot`中以客人的身份出现。

```
main :: forall eff. Eff ( timer :: Timer, dom :: DOM, canvas :: Canvas | eff) Unit
main = do
  Just canvas <- getCanvasElementById "dot-waves"
  ctx <- getContext2D canvas
  p <- pulse 30.0 100.0
  runSignal $ p ~> (rendering ctx) 
```

Enter fullscreen mode Exit fullscreen mode

这个更新的 main 函数接受一个介于 30.0 和 100.0 之间的脉冲信号，并在每个元素可用时运行渲染器。

这就是如何渲染一个跳动的点，伙计们。检查[完整源](https://github.com/awfulaxolotl/dot-waves/blob/c4eeb67378f97dc4c3fdd43cae39a6a6d7ded31c/src/Main.purs)。

# 请多点点

[https://codepen.io/awfulaxolotl/embed/wMmOxL/?height=600&default-tab=result&embed-version=2](https://codepen.io/awfulaxolotl/embed/wMmOxL/?height=600&default-tab=result&embed-version=2)

你对一个点不满意？你还想要吗？！

好吧，我们开始吧。具体来说，让我们在网格中制作一个由脉动点组成的矩形。

这是一个简单的变化，但是它强调了从命令式编程思维到函数式编程思维的巨大转变。我把它比作改变你的思维过程，从思考指令到思考由管道中的转换组成的数据流。

在命令式的世界里，你可以写一个 for 循环，迭代到每个点，并在你前进的时候渲染它们。

在功能世界中并非如此。我们希望从一粒种子数据开始，然后不断添加糖和香料，直到我们烤出一组合适的美味点。

```
places :: Array { x :: Int, y :: Int }
places = (\x y -> { x, y }) <$> 0 .. (xn - 1) <*> 0 .. (yn - 1) 
```

Enter fullscreen mode Exit fullscreen mode

矩形网格内的位置是一个不错的起点。这是一个使用 PureScript 记录的(x，y)坐标数组，是一种静态键值映射。

这就是事情变得非常有趣的地方。它可能看起来不像，但这里有一些有趣的机制。解释所发生的事情的最好方法是，走一遍这个表达式是如何被求值的。

它从`xn`和`yn`开始，分别是网格的宽度和高度。我们想要创建宽度和高度坐标的完整数组，所以我们使用中缀`..`操作符来创建它们。上面的代码片段评估为:

```
places = (\x y -> { x, y }) <$> [0, 1, ..., xn - 1] <*> [0, 1, ..., yn - 1] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们检查一下那个可爱的 lambda 函数和那个… `<$>`？事实证明，这只是典型 map 函数的中缀运算符版本——但有所改变。

函数式程序员喜欢概括事物，所以他们也概括了传统的 map 函数也就不足为奇了。它将对定义如何遍历自身的任何东西进行操作，而不仅仅是对列表进行操作。很好，但与此无关，所以让我们继续。

在另一个 Javascript 世界中，它应该是这样的:

```
// In Javascript
[0, 1, ..., xn - 1].map( x => y => ({ x, y }))

-- In PureScript
(\x y -> { x, y }) <$> [0, 1, ..., xn - 1] 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细观察，你可能会发现一些奇怪的东西。可怕的双λ！不，我在开玩笑。这并不可怕；其实是爱！函数返回一个函数，这意味着你可以部分地应用它的参数。这叫[阿谀](https://en.wikipedia.org/wiki/Currying)牛逼。默认情况下，PureScript 中的所有函数都是 curried 化的。

让我们看看当我们评估上面的 Javascript 语句和它的 PureScript 表达式对应物时会发生什么。

```
// In Javascript
[
  y => ({ x: 0, y }),
  y => ({ x: 1, y }),
  ..,
  y => ({ x: xn - 1, y })
]

-- In PureScript
[
  \y -> { x: 0, y },
  \y -> { x: 1, y },
  ...,
  \y -> { x: xn - 1, y }
] 
```

Enter fullscreen mode Exit fullscreen mode

哇，他们看起来几乎一模一样！原因很简单:Javascript 最近从函数式语言中获得了 lambda 函数的灵感。好的想法往往会渗透到所有的语言中。

好了，回到我们最初的函数:

```
places :: Array { x :: Int, y :: Int }
places = (\x y -> { x, y }) <$> 0 .. (xn - 1) <*> 0 .. (yn - 1) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在知道它相当于这个:

```
places = [\y -> { x: 0, y }, ...] <*> [0, 1, ..., yn - 1] 
```

Enter fullscreen mode Exit fullscreen mode

那么，那个`<*>`操作符到底是什么？还记得函数式程序员喜欢概括事物吗？原来这些家伙和女孩也推广了函数应用本身。*哦他们*。

具体来说，函数应用在上下文之间是通用的。在这种情况下，函数列表知道如何将自己应用于参数列表。让我们完成评估并检查最终值！

```
places = [{ x: 0, y: 0 }, { x: 0, y: 1 }, ..., { x: 1, y: 0 }, ..., { x: xn, y: yn }] 
```

Enter fullscreen mode Exit fullscreen mode

我不确定你，但我觉得我刚刚见证了一些美好的事情。通过抽象功能映射和应用程序，我们编写了——在我看来——真正清晰简洁的代码。当然，抽象背后的机制有点复杂。但是一旦你学会了，它们就成了你的第二天性。听着，妈妈，没有 for 循环了！

好吧，好吧，我们已经走了很远了。现在怎么办？嗯，还记得我提到的数据种子、管道和其他一些垃圾吗？地点列表是我们的数据种子。现在我们只需要添加一点额外的香料来得到我们的点。我们走吧！

```
renderDots :: Int -> Int -> Number -> Number -> Drawing
renderDots xn yn spacing diameter = fold dots
  where 
    places :: Array { x :: Int, y :: Int }
    places = (\x y -> { x, y }) <$> 0 .. (xn - 1) <*> 0 .. (yn - 1)

    coord { x, y } = { x: x' * spacing, y: y' * spacing }
      where x' = toNumber x
            y' = toNumber y

    coords :: Array { x :: Number, y :: Number }
    coords = coord <$> places

    dot { x, y } = renderDot x y (diameter / 2.0)
    dots = dot <$> coords 
```

Enter fullscreen mode Exit fullscreen mode

`fold dots`是函数的实际主体，`where`之后的所有内容都是中间值。

首先，我们创建初始位置，我们已经复习过了。很好，继续。

使用`coord`函数，我们通过将它们乘以一个常数来绘制地图。这将它们分开，创建我们的最终像素坐标。

之后，我们需要把这些像素坐标变成点。因此，让我们使用现有的`renderDot`函数来映射我们的坐标。*嘣巴达兵，*我们得到了一个渲染点数组。

然而，在这个代码片段中还有一个有趣的地方。你能发现它吗？结账`fold dots`。这是典型的 fold/reduce 函数，但是只有一个参数？如何折叠一个没有折叠功能的数组？

我给你一点时间猜…好了，时间到了。

泛化！在这种情况下，组合是广义的。类型(点是什么)定义了如何组合自身的成员。这叫做[幺半群](https://en.wikipedia.org/wiki/Monoid)。因此，fold 函数只是要求累积的点绘图将其自身与数组中的下一个点合并。

PureScript 和大多数函数式语言的一个伟大之处在于它试图攀登编程抽象之塔。通过组合几个共性，您可以生成极其简洁、正确和灵活的代码。

继续，我的程序员伙伴。抽象于。

这就是本章的内容，但是你可以随时查看这个特性的最终代码。

# 兴风作浪

[https://codepen.io/awfulaxolotl/embed/gPeEBE/?height=600&default-tab=result&embed-version=2](https://codepen.io/awfulaxolotl/embed/gPeEBE/?height=600&default-tab=result&embed-version=2)

到目前为止，这个短篇故事是一个谎言。标题承诺点波；不是跳动的点！毫无疑问，这是我们这个时代最大的悲剧。让我们修复它，掀起一些波澜！

方便的是，大部分产生波浪的逻辑已经形成。我们有一个时间流，脉动，和一个可组合的渲染函数。如果每个点根据它在网格中的位置来偏移它的计时器，那么应该会出现一个类似波浪的图案。我们去看看。

```
renderDot :: Int -> Int -> Number -> Drawing
renderDot x y t = 
  filled (fillColor dotConfig.color) dot
  where
    xp = (toNumber x) * dotConfig.separation
    yp = (toNumber y) * dotConfig.separation

    xf = (toNumber x) / (toNumber dotConfig.horizontal)
    yf = (toNumber y) / (toNumber dotConfig.vertical)

    t' = t + xf * yf
    unitAmp = ((sin t') + 1.0) / 2.0
    diameter = (pow unitAmp 3.0) * (dotConfig.maxSize - dotConfig.minSize) + dotConfig.minSize

    dot :: Shape
    dot = circle xp yp (diameter / 2.0) 
```

Enter fullscreen mode Exit fullscreen mode

新的`renderDot`函数从上一章定义的`renderDots`函数中去掉了一些逻辑。这给了我们更多的逐点控制，因为我们只需获取网格坐标和当前时间(从 Unix 纪元以来的毫秒数)来返回一个绘图。`renderDots`包装器简单地将这些组合在一起，在任何给定的时刻形成完整的画面。

```
t' = t + xf * yf 
```

Enter fullscreen mode Exit fullscreen mode

我们变化的核心恰好是最简单的。我们用实际时间加上一个偏移量来定义一个新的时间`t'`。根据设计，偏移量取决于 x 和 y 位置。这将从右下角到左上角创建一种波浪效果。

而且，嗯，也就差不多了！偏移时间和重构构成了变更的整体。此时检查出[源](https://github.com/awfulaxolotl/dot-waves/blob/66bd7f887b59fb7cdf6617a88cd911c2f7f32fe6/src/Main.purs)。

# 上蜡；打蜡

[https://codepen.io/awfulaxolotl/embed/jWzJJW/?height=600&default-tab=result&embed-version=2](https://codepen.io/awfulaxolotl/embed/jWzJJW/?height=600&default-tab=result&embed-version=2)

所以我们有我们的点，我们的波，现在我们只需要让它看起来很好。我在想一种 80 年代迈阿密夜生活的感觉，你明白吗？没有吗？你没得选择，被动读者女士。抱歉。

所以从你最喜欢的颜色选择开始，让我们想出一个让颜色跳动的方法。使用我们重构的`renderDot`函数，应该可以很容易地定制我们喜欢的点颜色。实现的关键部分是我们如何计算颜色分量( [RGB](https://en.wikipedia.org/wiki/RGB_color_model) )。

```
clrCmp n off = off + ((unitAmp (t' + n)) * (200.0 - off))
color = rgb (clrCmp 0.0 40.0) (clrCmp 0.66 25.0) (clrCmp 0.33 25.0) 
```

Enter fullscreen mode Exit fullscreen mode

我们希望在任何给定的点上使颜色与波同步，所以我们需要再次使用`t'`。`clrCmp`只是单个颜色分量的逻辑。我们有一个时间偏移和最终结果偏移来调整每个组件的行为和最大值。调整`off`变量可以改变颜色的饱和度，而`n`可以改变组件与其他组件的偏移量。

我们可能希望所有组件同时以相同的方式改变，但是如果所有组件都是均匀的，这将导致大部分是灰色/棕色。第二行的`color`绑定中使用的常量被手工调整为我们最喜欢的配色方案。

现在我们有了颜色，是时候调整剩下的体验了。白色上的浅色不能唤起迈阿密的感觉，所以让我们把背景设为黑色。然后为了更好的衡量，让我们把画布做得更大。越大越好！

```
<!DOCTYPE html>
<html>
  <head>
    Dot Waves
    <style>
      html {
        background-color: black;
        height: 100%;
      }
      body {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 100%;
      }
      canvas {
        max-height: 100%;
        max-width: 100%;
      }
    </style>
  </head>
  <body>
    <canvas width="1400" height="800" id="dot-waves"></canvas>
    <script src="/app.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

使用标准的`html`选择器作为背景完全可行，因为画布默认具有透明背景。理论上，我们可以在后台使用任何标准的 CSS 功能来美化体验。但是，有一些关于纯黑色的东西，你知道吗？

这是结束这次旅行的一个非常好的地方。我们有圆点，波浪，颜色，还有一点抛光。请随意访问源代码的[最终版本](https://github.com/awfulaxolotl/dot-waves/blob/add2744f685262cfd4d193434c9b5f647dbcb9f1/src/Main.purs)。

感谢您的阅读！