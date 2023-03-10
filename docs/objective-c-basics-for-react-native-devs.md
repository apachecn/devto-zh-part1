# objective-C React-Native dev 的基础

> 原文：<https://dev.to/kayis/objective-c-basics-for-react-native-devs>

你发现 React 不仅仅是一个框架，你现在可以使用 React 技能来做一些事情，如 [VR](https://facebook.github.io/react-vr/) 和[移动](https://facebook.github.io/react-native/)开发。你的第一个移动项目也是如此，甚至你用 React-Native 做的前几个项目也是如此，而且你已经很好地掌握了 JavaScript 知识。大多数时候你通过 npm 安装 JS 模块，有时也安装本地模块，这些模块很容易与`react-native link`链接。

但是现在有人想让你集成，例如，脸书 SDK，突然你发现自己在阅读安装指南，这迫使你查看这个`ios`目录，里面充满了外国的神秘事物。

你得在`Info.plist`和`AppDelegate.m`里改点代码，该死。

其中一个文件看起来不太吓人。`Info.plist`似乎是企业开发人员经常遇到的 XML 灾难，你可以面对它。

但是`AppDelegate.m`是另一个故事。你已经见过一些 C 了，所以`#includes`和注释有点道理，但是其他的呢？我甚至不！

但是不要害怕，因为我要告诉你关于这种古老的语言！

## 什么

目标——C 是 C 和 SmallTalk 的私生子。像 C++一样，它试图用面向对象的编程特性来增强 C。因此，正如你可能已经想到的，目标并不意味着它有更少的未定义行为，而是在它的袖子里有 OOP 技巧。

它更像 SmallTalk 而不是 C++，它包含了消息的概念而不是方法调用。它们有趣的特性之一似乎是，它们是动态类型的，这应该给开发者更多的灵活性。

它还有奇怪的语法，基本上是 C 和 OOP 插件的混合。

所以经历`AppDelegate.m`事情开始变得简单。

```
/**
 * Copyright (c) 2015-present, Facebook, Inc.
 * All rights reserved.
 *
 * This source code is licensed under the BSD-style license found in the
 * LICENSE file in the root directory of this source tree. An additional grant
 * of patent rights can be found in the PATENTS file in the same directory.
 */ 
```

嗯，那是评论，izi pizi。

```
#import "AppDelegate.h"
#import <React/RCTBundleURLProvider.h>
#import <React/RCTRootView.h> 
```

这里有一些包括，有点难。

这些被称为指令，它们被传递给 C 预处理器。据我所知，它们并不是 C 语言的一部分，而是前处理器语言的一部分。你需要知道的是，它们被用来在编译之前将本地代码包含到你的项目中。因此，当你创建一个本地组件时，它有时会调用本地代码，而这些代码必须存在于你的应用程序需要知道的某个文件中。

```
@implementation AppDelegate
...
@end 
```

什么？现在我们进入有趣的部分！

这是 Objective-C 给 C 带来的面向对象的附加功能之一。

在一些面向对象的语言中，定义和实现是在文件和 Objective 之间分开的——C 就是这样一种语言。

你可能之前已经注意到了`#import "AppDelegate.h"`，它缝合了这次分裂。

在`.h`文件中，您定义了自己的接口，在`.m`文件中，您通过导入`.h`文件并使用带有接口名称的`@implementation`来实现这些接口，在我们的例子中是`AppDelegate`，并使用`@end`来实现和结束实现。

我没有什么 C/C++技能，但据我所知，这种拆分只是一种约定，所以你可以将所有内容都写在一个文件中，但我不会在这里更深入地讨论`.h`，因为在 React-Native adventures 中我不必研究它。

```
- (BOOL)application:(UIApplication *)application 
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
  ...
} 
```

那么现在这里发生了什么？

正如你可能猜到的，这是一种方法。

如我所说，Objective-C 与 C++非常不同，尤其是在语法方面。

开始的`-`告诉我们这个方法是一个实例方法，一个`+`会把它变成一个静态(或类)方法。

也许一个 JS 的例子在这里有所帮助。

```
class MyClass {
  instanceMethod() {};
}
MyClass.staticMethod = function() {};

const instance = new MyClass();

instance.instanceMethod();
MyClass.staticMethod(); 
```

然后就是整个`(BOOL)` `(UIApplication *)`的东西。我最初认为这是 Objective-C 设置类型的方式，但我错了，这些只是老派的 C 类型强制转换。

Objective-C 的 OOP 是动态类型的，但由于它的核心仍然是 C，编译器需要知道如何处理所有的数据，所以你必须在运行时告诉它发生了什么。

如果没有这些类型转换，该方法将如下所示:

```
- application:application didFinishLaunchingWithOptions:launchOptions
{
  ...
} 
```

这是什么意思？

```
- methodName:param1 labelForParam2:param2
{
} 
```

方法名是 param1 的隐式标签，其他参数应该有自己的标签。虽然这些标签是可选的，但是我们推荐使用它们，因为它们是在调用点使用的。SmallTalk 就是这么做的，为什么要改变呢？

是什么让我们在 Objective-C 中调用方法发送消息呢？

```
[object methodName:valueForParam1 labelForParam2:valueForParam2]; 
```

在 JS 中，它看起来像这样:

```
object.methodName(valueForParam1, valueForParam2); 
```

使用标签的原因似乎是为了在调用点上有更好的可读性。

```
[array addObject:myObj at:myIndex]; 
```

代替

```
array.addObject(myObject, myIndex); 
```

反正下一行是变量声明，前面是 type 的 basic C，后面那一行，我们现在也可以读(差不多):

```
NSURL *jsCodeLocation;

jsCodeLocation = [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index.ios" fallbackResource:nil]; 
```

`[RCTBundleURLProvider sharedSettings]`是一个没有参数的消息，它的结果将被用作后续消息的接收对象。

在 Js 中，它看起来像这样:

```
let jsCodeLocation;

jsCodeLocation = RCTBundleURLProvider.sharedSettings().jsBundleURLForBundleRoot("index.ios", null) 
```

是的，有`nil`而不是`null`。似乎代表*不在*之列？同样，你的字符串必须以`@`为前缀。

```
RCTRootView *rootView = [[RCTRootView alloc] initWithBundleURL:jsCodeLocation
                                             moduleName:@"myapp"
                                             initialProperties:nil
                                             launchOptions:launchOptions];
rootView.backgroundColor = [[UIColor alloc] initWithRed:1.0f green:1.0f blue:1.0f alpha:1]; 
```

此外，这里没有太多新的事情发生。用一些配置创建了`rootView`对象，然后设置了它的背景颜色属性。

这里有趣的是，即使消息的参数被标记，你也不能改变它们的位置，正如你在`initialProperties:nil`中看到的，参数停留在它的位置，并被显式地设置为无值。

接下来的台词现在大部分都是自我解释，但是说到`self`，这些台词大概需要一些解释，哈哈。

```
self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
...
[self.window makeKeyAndVisible]; 
```

`self`是 Objective-C 的`this`，所以在这种情况下是`AppDelegate`对象。

哦，我差点忘了，那个`new`！

```
UIViewController *rootViewController = [UIViewController new]; 
```

代替

```
let rootViewController = new UIViewController(); 
```

所以这就像在`UIViewController`上调用一个静态方法来获得一个新的实例。*字面上的*(但有点错误)翻译成 JS 应该是

```
let rootViewController = UIViewController.new(); 
```

总结一下，整个`AppDelegate.m`在 JavaScript 中可以是这样的:

```
import "React/RCTBundleURLProvider.h";
import "React/RCTRootView.h";

class AppDelegate extends UIResponder {
  application(application, launchOptions) {
     let jsCodeLocation;

     jsCodeLocation = RCTBundleURLProvider.sharedSettings().jsBundleURLForBundleRoot("index.ios", null);

     let rootView = RCTRootView.alloc().(jsCodeLocation, "myblossomapp", null, launchOptions);
     rootView.backgroundColor = UIColor.alloc().initWith(1.0, 1.0, 1.0, 1];

     this.window = RCTRootView.alloc().initWithFrame(UIScreen.mainScreen().bounds);
     let rootViewController = new UIViewController();
     rootViewController.view = rootView;
     self.window.rootViewController = rootViewController;
     self.window.makeKeyAndVisible();
     return true;
  }
} 
```

这是一个字面上的翻译，在一些地方语义完全错误，但我认为它可以帮助一些人理解它。

## 结论

嗯，看起来很客观——C 语言是一种野兽般的语言，但没有什么是你不能驯服的！

在这里，我真的只是触及了整个事情的基础。大多数时候你运行`react-native link`并完成它，有时你不得不复制粘贴代码到`AppDelegate.m`。对于这一点，你不需要深入了解 Objective-C，但它有助于了解一些语法，有时你会错过一个字符，并得到疯狂的错误，不知道发生了什么。

我还认为，如果你经常查看的文件不是语法上的谜，它会让你对你的软件更有信心。