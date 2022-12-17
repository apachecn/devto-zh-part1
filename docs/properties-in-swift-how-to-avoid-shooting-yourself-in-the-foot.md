# Swift 中的属性:如何避免搬起石头砸自己的脚

> 原文：<https://dev.to/preslavrachev/properties-in-swift-how-to-avoid-shooting-yourself-in-the-foot>

*注:这篇文章最初发表在[我的博客](http://www.preslav.me/2017/07/23/properties-in-swift-how-to-avoid-shooting-yourself-in-the-foot/)上。*

Swift 提供了几个构造，使得编写代码更加流畅，样板文件更少。有时候，这种简洁的语法是有潜在代价的。一个这样的方面是属性，更具体地说，是属性初始化。语法上的微小差异可能会导致不必要的内存消耗、意外的状态不一致等。当项目仍然很小的时候，这些问题可能会被忽略，而在稍后的阶段，当项目大到很难调试的时候，这些问题会再次出现。

尽管一个有经验的 Swift 程序员会立即注意到以下两个表达之间的区别，但相对于该语言的新手可能不会。考虑下面的表达式:

```
var urlSession: URLSession = {
  let urlSessionConfiguration: URLSessionConfiguration = URLSessionConfiguration.default.copy() as! URLSessionConfiguration
  urlSessionConfiguration.requestCachePolicy = NSURLRequest.CachePolicy.returnCacheDataElseLoad

  // some further configuration

  return URLSession(configuration: urlSessionConfiguration)
}() 
```

Enter fullscreen mode Exit fullscreen mode

然后和这个比较:

```
var urlSession: URLSession {
  let urlSessionConfiguration: URLSessionConfiguration = URLSessionConfiguration.default.copy() as! URLSessionConfiguration
  urlSessionConfiguration.requestCachePolicy = NSURLRequest.CachePolicy.returnCacheDataElseLoad

  // some further configuration

  return URLSession(configuration: urlSessionConfiguration)
} 
```

Enter fullscreen mode Exit fullscreen mode

注意到不同了吗？如果您还没有，下面这段代码可能会有所帮助:

```
for _ in 1...10 {
  print(urlSession)
} 
```

Enter fullscreen mode Exit fullscreen mode

前一个表达式将总是打印出相同的实例 ID，而后者将输出 **10 个不同的实例**。Swift 区分了带有闭包初始化器的[存储属性](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html#//apple_ref/doc/uid/TP40014097-CH14-ID255)和[计算属性](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html#//apple_ref/doc/uid/TP40014097-CH14-ID259) (ex 2)。存储的属性被初始化一次，虽然它们的值可能会在应用程序的生命周期中改变，但是初始化器只被调用一次。这一点很清楚，因为这实际上是一个赋值操作(由`=`操作符表示)，闭包在赋值之前执行(闭包后的`()`括号)。另一方面，只读计算属性只不过是完整的[计算属性](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html#//apple_ref/doc/uid/TP40014097-CH14-ID259)声明的部分应用:

```
var myProp: MyClass {
  get {
    // optionally do some necessary pre-work
    return MyClass(/* set some intial values */)
  }
  set(newProp) {
    a = newProp.a
    b = newProp.b
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

只读计算属性没有设置器，在这种情况下，`get`和`set`关键字可以省略。这就引出了上面的第二个例子。在这种情况下，每当访问`urlSession`属性时，服务于 getter 方法的闭包就会被调用。这反过来每次都会创建一个新的`URLSession`实例，潜在地导致应用程序变慢、状态不一致或内存泄漏。

注意:因为计算属性在每次访问时都会被重新分配，所以它只能被声明为`var`而不是`let`。然而，第一个例子是一个只被赋值一次的存储属性，所以最好将它声明为一个`let`常量，以防我们不需要再次对它进行赋值(总是推荐)

## 进一步阅读

*   [属性| Swift 编程语言(Swift 4)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html)
*   [Swift +带闭包的初始化](https://medium.com/the-traveled-ios-developers-guide/swift-initialization-with-closures-5ea177f65a5)
*   [Swift:小心计算属性|机器人娜塔莎](https://www.natashatherobot.com/swift-computed-properties/)