# 在 iOS 中从原生视图触发 Webview 功能

> 原文：<https://dev.to/ajayv1992/trigger-webview-function-from-native-view-in-ios>

我有一个打开 webView 的类。在前一个类 a 之上打开了另一个类 b。现在，在 ClassB 中，我想调用 ClassA 中 webView 的 JavaScript 函数。

我试过[self . command delegate eval js:@ " jsFunction(' Hello ')；"];当我从 ClassA 调用时，它是有效的，但是当我从 ClassB 调用时，它就不起作用了。

那么，如何从 iOS 第二视图调用 webview 功能呢？如果能有所帮助的话，我会用科尔多瓦。？