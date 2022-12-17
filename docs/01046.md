# swift 中的“var”关键字

> 原文：<https://dev.to/abelagoi/the-var-keyword-inswift-227c>

在 swift 中，我们使用`var`关键字来声明变量。Swift 使用变量通过识别它们的名称来存储和引用值。变量必须在使用前声明。

### 声明变量

我们可以像下面这样在 swift 中声明一个变量:

```
var friendlyWelcome = "Hello" 
```

注意上面的关键字`var`，我们用它来表示`FriendlyWelcome`是一个变量，应该保存内容`"Hello"`。我们可以通过简单地改变内容
来改变变量的值

```
var friendlyWelcome = "Hello"

friendlyWelcome = "Ututu Oma"

// friendlyWelcome is now "Ututu Oma" 
```

当我们打算改变变量的值时，我们不应该再使用`var`关键字。

### 命名变量

变量名可以由字母、数字和下划线字符组成。它必须以字母或下划线开头。因为 Swift 4 是一种区分大小写的编程语言，所以大小写字母是截然不同的。

### 类型标注

我们可以在声明一个变量时提供一个`type annotation`,以明确变量可以存储的值的种类。下面是语法

```
var variableName: <data type> = <optional initial value> 
```

当我们声明一个不包含数据类型的变量时，swift 会自动使用初始值的数据类型为我们完成这项工作。数据类型可以是整数、字符串、布尔、双精度或浮点。当我们将一个变量声明为一个字符串时，Swift 强制我们只分配与指定的数据类型
相匹配的内容

```
var myName: String = "Agoi Abel"
myName = 4 //This will show an error, because the myName variable content should only be a string not an Integer (4) 
```

### 总结

记住以下几点很重要。可以用`var`关键字声明变量，不需要显式声明变量的类型。但是，请记住，在 Swift 中，每个变量都有一个类型。如果 Swift 不能推断出类型，那么它就抱怨。每个变量都有一种类型，而且这种类型不能改变。

很高兴与你分享这篇文章。如果你喜欢这篇文章，请鼓掌表示支持👏*。感谢您的宝贵时间，请务必关注我或在*下方留下您的评论👇