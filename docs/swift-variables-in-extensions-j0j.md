# 扩展中的 Swift 变量

> 原文：<https://dev.to/luisramos1337/swift-variables-in-extensions-j0j>

我最近开始从事一个使用 Swift2.0 的项目！在过去的 3 年里，我一直在使用 Obj-C，当 Swift 出现时，我并不是早期采用者之一。语言和 SourceKit 的问题总是在 XCode 6 中崩溃，这不利于开发环境。现在，随着 2.0 版的发布，开源和 XCode 7 的承诺，Swift 已经准备好了！

我遇到的第一个问题是许多在项目中使用扩展/类别的 iOS 开发人员都会遇到的问题:如何向扩展添加变量(静态或非静态)？

因为我似乎总是在谷歌上搜索这个问题，所以为了方便起见，我把它记录在这里。

### 静态变量

对于一个静态变量，实际上有一个非常简洁的方法:

```
extension UIView {
  struct Static {
    static var defaultPadding : CGFloat = 16
  }
  func widthWithPadding() -> CGFloat {
    return self.bounds.width + Static.defaultPadding
  }
} 
```

因为可以在扩展中声明结构，所以可以使用这些值对象来保存静态变量，它们的作用方式与在类中声明静态变量的方式相同。如果您不喜欢额外的`Static.`声明，您可以使用计算变量:
创建包装器

```
extension UIView {
  var defaultPadding : CGFloat { return Static.defaultPadding }
} 
```

### 实例变量

对于实例变量，解决方案是使用 obj-c 运行时。您需要创建计算变量，使用`objc_getAssociatedObject()`和`objc_setAssociatedObject()`来获取和设置关联的对象。这是与 Obj-c
一起使用时采用的相同方法

```
import ObjectiveC

extension UIViewController {
  struct Static {
    static var MainTitle = "\_MainTitle"
  }
  var mainTitle: String? {
    get{
      return objc_getAssociatedObject(self,&Static.MainTitle) as? String
    }
    set(value){
      if newValue = newValue {
        objc_setAssociatedObject(self,&Static.GenericTitle,newValue as NSString?,.OBJC_ASSOCIATION_RETAIN_NONATOMIC)
      }
    }
  }
} 
```

这样你就可以扩展到你内心的渴望！如果你想了解结构和类之间的区别，请查看苹果文档。为了找到更多关于 obj-c 运行时的神奇之处，请查看 [NSHipster 关于它的帖子](https://dev.tonshipster-objc)。