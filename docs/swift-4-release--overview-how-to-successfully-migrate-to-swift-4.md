# Swift 4 版本和概述:如何成功迁移到 Swift 4

> 原文：<https://dev.to/dianamaltseva8/swift-4-release--overview-how-to-successfully-migrate-to-swift-4>

Swift 是一种开源的静态类型编程语言，由苹果公司在 2014 年 WWDC 上推出。虽然 Swift 年前才出现，但它很快成为 iOS 开发者最热门的语言，也是他们最喜欢的语言。

根据 Stack Overflow 开发者调查，Swift 成为 2015 年最受欢迎的编程语言第一名，2016 年第二名。

值得注意的是，创始人致力于语言的不断改进和更新。最新的一个名为 [Swift 4](https://swift.org/blog/swift-4-0-release-process/) ，于 2017 年 6 月 5 日投入使用，主要关注标准库的 ABI 稳定性。

在 Swift 3 的基础上，Swift 4 语言提供了更高水平的稳定性，并提供了与 Swift 3 的源代码兼容性。

更重要的是，现在快速发展需要更少的时间。此外，新版本增加了智能密钥路径和序列化等功能，并减少了应用程序二进制文件的大小。

### Swift 4 中的新功能

*   对反射、字典和集合类型的创建、使用和操作的增强
*   为 Swift 类型的类型安全键值编码添加智能密钥路径
*   强制独占内存访问
*   使保留 Unicode 正确性的字符串的使用更加容易和快捷
*   增加了对存档和序列化的扩展支持
*   为 plist 和 JSON 等格式的序列化启用类型安全

### Swift 4 迁移变更概述

迁移器提供的绝大多数更改来自于通过比较以前和当前 SDK 生成的数据，这些数据可以驱动标识符和类型的重命名。

有几个特殊规定，迁移器可以安全地执行简单的机械更改。

**SDK 变化**

SDK 最普遍的两个变化是将全局常量移入静态类型属性，以及将字符串常量转换为 Swift 枚举案例。这些由迁移器自动处理。还有各种类型签名的变化。

**值得注意的特例**

string 在 Swift 4 中有新的 API，其中一些现在返回 Substring 或 String。为了简化这种转换，当 API 现在需要不同的类型时，迁移器将插入显式的初始化器转换。

SE-0110:区分单元组和多参数函数类型
f: (Void) - >()

当使用 f: (Void) ->()作为函数参数的类型时，通常意味着是 f: () ->()，因此迁移器会建议您使用这种类型。否则，根据 Swift 4 的 SE-0110 中的新规则，您将需要调用函数 f 作为 f(())。

**添加元组析构**

对于代码，例如:

swift func foo(_: ((Int，Int) -> ()) {} foo { (x，y) in print(x + y) }

迁移器必须添加显式元组析构才能在 Swift 4 中继续构建，例如:

swift func foo(_: ((Int，Int) -> ()) {} foo { let (x，y)= $ 0；打印(x + y) }

**默认参数值必须是公共的**

编译器现在对用作公共函数默认参数的引用的非文字值的可访问性更加严格；它们还必须是公开的。

*   公开引用的默认值。
*   提供返回合理默认值的公共函数。

### 项目迁移到 Swift 4

*   首先，确保您要迁移到 Swift 4 的项目在 Swift 3.2 中成功构建。您还应该记住 Swift 3.1 和 Swift 3.2 之间的显著差异，因此您的项目可能会有一些错误(例如，如果它是在 Swift 3.1 中构建的)，需要在迁移之前解决。

*   请记住，通过在源代码控制下管理 iOS 项目，您将能够更容易地查看通过迁移助手实现的更改，并在必要时放弃这些更改并重新启动迁移过程。

了解更多 **[成功迁移到 Swift 4 的有用提示。](https://smartym.pro/blog/swift-4-release-how-to-migrate-your-project-to-swift-4-programming-language/)**T7】

还有，多了解一下 [版本兼容性。T3T5】](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Compatibility.html)