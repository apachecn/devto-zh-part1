# Swift 中的协议及实例

> 原文：<https://dev.to/abelagoi/protocol-in-swift-with-practical-examples-3iif>

[![](img/3a69ff5cc0d005422806c5fcbd303d1e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dp40Pfnz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A80_c-wJSKSVcTIc4hi7KMQ.jpeg)

一个*协议*定义了适合特定任务或功能的方法、属性和其他需求的蓝图。然后，该协议可以被一个类、结构或枚举*采用*，以提供那些需求的实际实现。任何满足协议要求的类型被称为*符合该协议。*

> 所以，简单来说，一个协议说一个结构、类或枚举，如果你想成为那个，做这个，这个和这个。如果你想成为一名开发人员，你必须阅读和编写代码。
> 
> ### 协议的创建和采用

我们可以创建一个`Protocol`如下:

```
protocol SomeProtocol { //protocol definition goes here} 
```

Enter fullscreen mode Exit fullscreen mode

类、结构和枚举可以采用这些协议，方法是将协议名放在类型名之后，用冒号分隔，作为它们定义的一部分。可以列出多个协议，并用逗号分隔。

```
struct SomeStruct: FirstProtocol, SecondProtocol { //struct definition goes here} 
```

Enter fullscreen mode Exit fullscreen mode

如果一个类有一个超类，在它采用的任何协议前列出超类名称，后面加一个逗号。

```
class SomeClass: SomeSuperClass, FirstProtocol, AnotherProtocol { //class definition goes here} 
```

Enter fullscreen mode Exit fullscreen mode

### 举例一:属性要求