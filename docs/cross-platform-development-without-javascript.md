# 无需 JavaScript 的跨平台开发

> 原文：<https://dev.to/lemuelogbunude/cross-platform-development-without-javascript>

我们都知道有跨平台开发大师使用 JavaScript 和 CSS 等语言来创建漂亮的移动应用程序。如果你像我一样在这些领域没有什么经验，并且不想开始一个完整的 JavaScript 课程，你会怎么做？

不要误解我——JavaScript 很棒，至少我在这方面没有什么经验。事实是，它真的会吓到一些刚刚起步的人，我们有这么多框架 React、Angular、jQuery、Vue、Mercury、Deku、Riot.js😕......obama.js，开玩笑的😊但是你明白我的意思。

对于想要与众不同的人，让我给你介绍一下 Flutter。Flutter 是一款新的移动应用 SDK，旨在帮助开发人员和设计人员为 iOS 和 Android 构建现代移动应用。如果你没有任何移动开发经验，Flutter 是一个简单快捷的方法来创建漂亮的移动应用程序。如果你是一个经验丰富的 iOS 或 Android 开发人员，你可以使用 Flutter 来表达你的观点，并利用你现有的 Java/ObjC/Swift 投资。

还要注意，通过 Flutter，你可以访问 iOS 和 Android 上的原生功能和 SDK。所以你可能会问 Flutter 使用什么编程语言，它使用 Dart。

Dart 是一种应用程序编程语言，易于学习，易于扩展，并且可以随处部署。相信我 Dart 真的很好，为了让你有一个很好的体验，我建议你使用的 IDE 是 Intelij IDEA，它和 Dart 一起工作很好。

下面是使用 Dart 的示例代码:

```
// Define a function.
printNumber(num aNumber) {
  print('The number is $aNumber.'); // Print to console.
}

// This is where the app starts executing.
main() {
  var number = 42; // Declare and initialize a variable.
  printNumber(number); // Call a function.
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class Point {
  num x;
  num y;

  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为代码是不言自明的，它非常类似于 Java 和其他语言。

你可以查看一下 [Flutter](https://flutter.io/) 网站，安装也很简单，明智的做法是查看一下你将使用的 [Dart](https://www.dartlang.org/) ，还有 [codelabs](https://codelabs.developers.google.com/codelabs/from-java-to-dart/#0) 供你试用，如果你有任何问题，你可以随时联系我。