# React Native 中的 PanResponder 入门

> 原文：<https://dev.to/hyra/getting-started-with-the-panresponder-in-react-native-9mf>

在 React Native 中，你可以使用 PanResponder 来识别多点触摸手势以及滑动和其他触摸，使原生应用程序感觉简洁直观。但是让它运行起来会让人感到畏惧，有点像魔法。在这篇文章中，我将试着引导你完成这个过程，希望能揭开它的神秘面纱，让你走上神奇之路。

## 我们将制造什么

很明显，我们会想把重点放在 PanResponder 本身，所以 UI 方面，这将是非常准系统。我们可以在屏幕上拖动一个图像。当我们放开它时，它会弹回到原来的位置。另外，当我们按下图像时，它会放大。

<center>
![PanResponder Demo](img/00dbdb32f38942a6bd16ea2ff2392a59.png)
</center>

## 设置舞台

我假设你对建立一个新的 React 原生项目有些熟悉。如果没有，脸书的人已经很好地解释了这些步骤。

让我们从一个新项目开始。为了简单起见，我将它称为 panresponder-demo，因为它没有与独角兽押韵的名称。

```
$ react-native init panresponder_demo 
```

Enter fullscreen mode Exit fullscreen mode

首先，让我们将一个图像添加到项目中，作为您的拖放目标。

在 panresponder_demo 文件夹下创建一个目录`assets`,并将您想要使用的图像插入其中。如果没有，可以用[这个](img/panresponder-target.png)。

让我们在屏幕上显示我们的图像，这样我们就可以继续到酷的部分。

打开`index.ios.js`，在顶部添加`Image`组件:

```
import React, {
  AppRegistry,
  Component,
  StyleSheet,
  Text,
  View,
  Image // we want to use an image
} from 'react-native'; 
```

Enter fullscreen mode Exit fullscreen mode

现在用我们的图像替换默认的应用程序内容，因此改变`render()`方法

```
render() {
  return (
    <View style={styles.container}>
      <Image source={require('./assets/panresponder.png')} />
    </View>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

当你现在运行应用程序时，你应该会看到屏幕中央的图像，等待你去做一些更令人兴奋的事情。所以让我们开始吧。

<center>
![Panresponder 01](img/1646a1a2c16176b015151c31f9c277a6.png)
</center>

## 添加 PanResponder

让我们进入更有趣的部分。添加 PanResponder 系统。

在顶部，导入`PanResponder`以便我们可以使用它。同时，我们还将添加`Animated`，它允许我们使用动画值，这对我们的动画和计算很方便。

```
import React, {
  AppRegistry,
  Component,
  StyleSheet,
  Text,
  View,
  Image, // we want to use an image
  PanResponder, // we want to bring in the PanResponder system
  Animated // we wil be using animated value
} from 'react-native'; 
```

Enter fullscreen mode Exit fullscreen mode

PanResponder 基本上由几个可以实现的事件驱动方法组成。一旦你定义了你想要它的行为，就像你把它附加到一个视图上一样，然后它会把所有的事件(手势)传播到你连接的方法上。

为了简单地说明它，让我们实现`componentWillMount()`方法并设置一个基本的 PanResponder 实例:

```
componentWillMount() {
  this._panResponder = PanResponder.create({
    onMoveShouldSetResponderCapture: () => true,
    onMoveShouldSetPanResponderCapture: () => true,

    onPanResponderGrant: (e, gestureState) => {
    },

    onPanResponderMove: Animated.event([
    ]),

    onPanResponderRelease: (e, {vx, vy}) => {
    }
  });
}

render() {
  return (
    <View style={styles.container}>
      <Animated.View {...this._panResponder.panHandlers}>
        <Image source={require('./assets/panresponder.png')} />
      </Animated.View>
    </View>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

哇，这里发生了很多事情。我们来分解一下。

告诉操作系统我们希望允许移动我们将附加此 panresponder 的视图。`onMoveShouldSetPanResponderCapture`做同样的事情，但是对于拖动，我们希望能够做到。

接下来，我们得到了 3 个方法，当我们访问元素的移动时，它们将被调用。这是设置一些初始值的绝佳位置。

`onPanResponderMove`在我们移动元素时被调用，我们可以用它来计算对象的下一个值

`onPanResponderRelease`在我们释放视图时被调用。一分钟后，我们将使用它来使图像动画回到它的原始位置

最后，我们将 panresponder 添加到一个`Animated.View`中，我们用它来包装`Image`组件，这样它将服从我们的 panresponding 要求。

## 使其可拖动

让我们实现前两个方法，以便能够在屏幕上拖动图像。

为了跟踪图像在屏幕上的位置，我们需要记录它在某个地方的位置。这是组件`state`的完美工作，所以让我们添加这个:

```
constructor(props) {
  super(props);

  this.state = {
    pan: new Animated.ValueXY()
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们更新一下`panHandler`的实现:

```
componentWillMount() {
  this._panResponder = PanResponder.create({
    onMoveShouldSetResponderCapture: () => true,
    onMoveShouldSetPanResponderCapture: () => true,

    // Initially, set the value of x and y to 0 (the center of the screen)
    onPanResponderGrant: (e, gestureState) => {
      this.state.pan.setValue({x: 0, y: 0});
    },

    // When we drag/pan the object, set the delate to the states pan position
    onPanResponderMove: Animated.event([
      null, {dx: this.state.pan.x, dy: this.state.pan.y},
    ]),

    onPanResponderRelease: (e, {vx, vy}) => {
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，在拖动时，我们更新状态平移值，当我们移动时，我们将 dx/dy 设置为平移值。

现在我们有了我们的值，我们可以在我们的`render()`方法中使用它，当我们拖动的时候它会被不断地调用，所以我们可以计算我们的图像在这里的位置:

```
render() {
  // Destructure the value of pan from the state
  let { pan } = this.state;

  // Calculate the x and y transform from the pan value
  let [translateX, translateY] = [pan.x, pan.y];

  // Calculate the transform property and set it as a value for our style which we add below to the Animated.View component
  let imageStyle = {transform: [{translateX}, {translateY}]};

  return (
    <View style={styles.container}>
      <Animated.View style={imageStyle} {...this._panResponder.panHandlers}>
        <Image source={require('./assets/panresponder.png')} />
      </Animated.View>
    </View>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## 渐入佳境！

我们有进展了。当你现在运行应用程序时，你将能够在屏幕上拖动图像！然而，当你第二次这样做时，你会注意到它又从屏幕中间开始，而不是从你离开的地方开始。

让我们解决这个问题。

幸运的是，这很简单。我们需要改变`onPanResponderGrant`中的初始值，以考虑正确的偏移(我们将其拖离中心):

```
onPanResponderGrant: (e, gestureState) => {
  // Set the initial value to the current state
  this.state.pan.setOffset({x: this.state.pan.x._value, y: this.state.pan.y._value});
  this.state.pan.setValue({x: 0, y: 0});
}, 
```

Enter fullscreen mode Exit fullscreen mode

如果你再次运行这段代码，你会注意到第二次拖放操作非常完美，但是每次之后图像都会不规则地跳转。这与偏移的计算方式有关。一旦你放开图像，我们实际上需要弄平它。这可以在我们的第三个也是最后一个方法中完成:

```
onPanResponderRelease: (e, {vx, vy}) => {
  // Flatten the offset to avoid erratic behavior
  this.state.pan.flattenOffset();
} 
```

Enter fullscreen mode Exit fullscreen mode

## 上下缩放

最后但同样重要的是，让我们在拖动时改变图像的大小。首先，我们将向状态添加一个`scale`属性，这样我们就可以在我们的样式中使用它，并影响它在 PanResponder
中的值

```
this.state = {
  pan: new Animated.ValueXY(),
  scale: new Animated.Value(1)
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将在渲染方法
的样式中使用这个值

```
...
let rotate = '0deg';

// Calculate the transform property and set it as a value for our style which we add below to the Animated.View component
let imageStyle = {transform: [{translateX}, {translateY}, {rotate}, {scale}]};
... 
```

Enter fullscreen mode Exit fullscreen mode

这样，剩下要做的就是在 PanResponder 实现中影响`scale`的值。当我们开始拖动时，`onPanResponderGrant`方法被调用，因此我们可以动画显示值

```
onPanResponderGrant: (e, gestureState) => {
  // Set the initial value to the current state
  this.state.pan.setOffset({x: this.state.pan.x._value, y: this.state.pan.y._value});
  this.state.pan.setValue({x: 0, y: 0});
  Animated.spring(
    this.state.scale,
    { toValue: 1.1, friction: 3 }
  ).start();
}, 
```

Enter fullscreen mode Exit fullscreen mode

当我们释放它时，我们会将它动画化

```
onPanResponderRelease: (e, {vx, vy}) => {
  // Flatten the offset to avoid erratic behavior
  this.state.pan.flattenOffset();
  Animated.spring(
    this.state.scale,
    { toValue: 1, friction: 3 }
  ).start();
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

就是这样！我们有一个可以拖动的图像，它会给出一个视觉指示(除了跟随我们的手指)。

如果您不理解或者不想回顾，可以在 Github 上的[处找到结果代码。](https://github.com/Hyra/panresponder_demo)

一如既往，如果你有任何问题，你可以在推特上找到我。

编码快乐！