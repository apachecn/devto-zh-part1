# 右键单击& Elm

> 原文：<https://dev.to/zaptic/right-clicks--elm-2ll>

在 [Zaptic](https://www.zaptic.com) ，我们允许客户创建一个流程图，描述他们想要完成的业务流程。我们使用平移&缩放界面在 SVG 中绘制流程图，以便于查看。我们跟踪鼠标按下、鼠标拖动&鼠标抬起事件来管理这个 UI。

不幸的是，在开发中，这种体验有点烦人，因为每次我们在元素上加载浏览器的检查器时，右键单击的`mousedown`都会被 UI 跟踪，但是`mouseup`会在上下文菜单上，所以不会注册。结果呢？用户界面处于“平移”模式，并试图跟随你的鼠标到任何地方，即使你不再按下任何鼠标按钮。

为了克服这一点，我们需要跟踪哪个鼠标按钮被使用，这样我们就可以忽略右键单击。我们使用的是 [elm-lang/mouse](http://package.elm-lang.org/packages/elm-lang/mouse/latest) 包，其中包含一个可以用于`mousedown`事件的`position`解码器。解码器只提供了鼠标位置`pageX`和`pageY`，它不提供任何关于哪个按钮被按下的信息，所以我们需要更深入一点。

如果我们看一下模块的[源代码](https://github.com/elm-lang/mouse/blob/1.0.1/src/Mouse.elm)，我们可以看到位置解码器正在做以下事情:

```
position : Json.Decoder Position
position =
  Json.map2 Position
    (Json.field "pageX" Json.int)
    (Json.field "pageY" Json.int) 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所猜测的，它正在寻找`pageX`和`pageY`属性，并且它期望它们是整数。如果我们看看鼠标按下事件的可爱的 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/Events/mousedown),我们可以看到它有许多其他属性，包括一个整数`button`属性，它包含了哪个按钮被使用的一些指示。我们可以将自己的&型解码器设置为:

```
type alias MouseClick =
    { pageX : Int
    , pageY : Int
    , button : Int
    }

mouseClickDecoder : Json.Decode.Decoder PFM.MouseClick
mouseClickDecoder =
    Json.Decode.map3 PFM.MouseClick
        (Json.Decode.field "pageX" Json.Decode.int)
        (Json.Decode.field "pageY" Json.Decode.int)
        (Json.Decode.field "button" Json.Decode.int) 
```

Enter fullscreen mode Exit fullscreen mode

并修改我们的事件处理程序来使用它:

```
type Msg
    = DragStart MouseClick
    | ...

onMouseDown : Attribute Msg
onMouseDown =
    on "mousedown" (Json.Decode.map DragStart mouseClickDecoder) 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们将要处理`DragStart`消息的`update`函数中，我们可以检查`button`的值并忽略右击。在不同的按钮如何被表示为整数的问题上，浏览器之间有一些不一致，你可以在 [quirksmode](https://www.quirksmode.org/js/events_properties.html#button) 上读到。幸运的是，它们都与表示为`2`的“右键单击”对齐。所以我们可以忽略:

```
DragStart click ->
    -- Ignore right-clicks which are given value '2' by browsers
    if click.button /= 2 then
        let
            pos =
                { x = click.pageX, y = click.pageY }
        in
        ({ model | = drag = Just { start = pos, current = pos } }, Cmd.none)
    else
        (model, Cmd.none) 
```

Enter fullscreen mode Exit fullscreen mode

我们做到了！我们不再需要右键点击，可以随时愉快地进入浏览器开发工具。

如果你有任何想法或建议，请告诉我！总是乐于学习。

*更新*:查看下面伊利亚的评论。它真的清理了更新中的消息处理:

```
-        DragStart click ->
-            -- Ignore right-clicks which are given value '2' by browsers
-            if click.button /= 2 then
-                let
-                    pos =
-                        { x = click.pageX, y = click.pageY }
-                in
-                { model | drag = Just { start = pos, current = pos } } ! []
-            else
-                model ! [] +        DragStart pos ->
+            { model | drag = Just { start = pos, current = pos } } ! [] 
```

Enter fullscreen mode Exit fullscreen mode