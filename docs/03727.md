# Swift 中受保护的属性和方法-替代方案

> 原文：<https://dev.to/d4ttatraya/protected-properties-and-methods-in-swift---an-alternative-a6i>

*最初发布于[我的博客网站](https://d4ttatraya.me/Protected-Access-Swift/)。*

swift 开发人员一直强烈要求在 [Swift 的访问控制](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html)中增加`protected`访问。在栈溢出问题上，很多次被问到如何在 swift 中编写受保护的方法。但是 swift 仍然没有提供任何直接的方法来做到这一点。我们真的能在 Swift 中实现受保护的访问吗？是的，我们可以！

苹果已经在 2014 年发表了一篇博客[访问控制和保护](https://developer.apple.com/swift/blog/?id=11)关于这种需求以及它是多么不必要。但是我仍然相信在项目中有很多情况，我们需要对外部类隐藏一些属性和方法，但是只有孩子可以访问。所以这是我想到的:

```
//Animal.swift file
class Animal {
    fileprivate var protectedVar: Int = 0
    fileprivate func protectedFunc() {//do stuff}
}

class Dog: Animal {
    func doSomething() {
        protectedVar = 1
        protectedFunc()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们试图从 *Animal.swift* 文件之外访问`protectedVar`或`protectedFunc()`，那么编译器将抛出一个错误。
要实现这种受保护的访问，我们唯一需要记住的是**孩子必须和**中声明的父母在同一个文件中声明。此外，这种方法不能在开发库、包或框架时使用。

您是否曾在 Swift 中搜索受保护的访问权限时遇到困难？然后在下面的评论里告诉我你是怎么熬过来的。此外，如果你有任何其他解决方案，请在这里或在 [Twitter @d4ttatraya](https://twitter.com/D4ttatraya) 上发布。

*参考资料:[苹果开发者指南](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html)T3】*