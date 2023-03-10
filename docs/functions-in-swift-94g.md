# Swift 中的功能

> 原文：<https://dev.to/abelagoi/functions-in-swift-94g>

[![](img/1b3f904f94f2aa9c66ee0a511ef43025.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---KTT3hSp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AoNwmg5Snx8KmQ0eyBvxlrg.png)

### 简介

函数是执行特定任务的自包含代码块。

### 定义和调用函数

下面是一个简单的函数，它目前实际上什么也不做。

```
func emptyFunction() { //your code should go inside here} 
```

Enter fullscreen mode Exit fullscreen mode

`func`关键字用于定义函数，而`emptyFunction`是函数的名称。你必须在任何时候创建一个函数时使用`func`,函数名可以根据函数的用途而变化。

上述函数可以在我的代码中调用，如下图

```
emptyFunction() 
```

Enter fullscreen mode Exit fullscreen mode

### 功能参数

有时候我们需要将参数
传递给一个函数，我们可以像下面这样将参数传递给一个函数

```
func functionWithParameter(parameterOne: Int) { 
    print("This is my parameter: \(parameter)") 
} 
```

Enter fullscreen mode Exit fullscreen mode

`paramterOne`是传递给函数的参数，而`Int`表示参数的数据类型。`data type (Int)`作为一种契约，当我们调用函数含义时必须遵守，参数必须是一种类型的`Int.`我们需要在调用函数时包含我们传递的参数，如下图

```
functionWithParameter(parameterOne: 32) 
```

Enter fullscreen mode Exit fullscreen mode

通过用逗号
分隔参数，我们可以在一个函数中使用多个参数

```
//declaring the function
functionWithParameters(parameterOne: Int, parameterTwo: String) {
}

//calling the function
functionWithParameters(parameterOne: 32, parameterTwo: "Hello") 
```

Enter fullscreen mode Exit fullscreen mode

### 函数返回类型

`return type`用来表示我们的函数应该返回的值的数据类型。就像参数类型一样，Xcode 会强制我们确保函数返回它指定的类型。

```
func functionWithReturnType() -> String { 
    return "I must return a string"
} 
```

Enter fullscreen mode Exit fullscreen mode

swift 中有许多数据类型，包括整型、布尔型、字符串型、浮点型和双精度型。`-> String`表示函数必须返回字符串类型。任何不返回值的函数都有一个名为`Void`的特殊返回类型。当我们没有指定函数返回类型时，我们只是告诉 swift 返回类型是一个 void，因此我们的函数不能返回任何东西。

```
//Different ways to declare Void

func noReturnType() { 
    //swift will automatically take this function as void 
    //hence we must not return anything
}

function returnWithReturnType() -> Void { 
    //same as above, but we declare the void ourselves
}

function returnWithReturnType() -> () { 
    //this is another way to declare void
} 
```

Enter fullscreen mode Exit fullscreen mode

### 具有多个返回值的函数&类型

有时我们需要函数返回多个值，我们可以在 swift 中借助下面的 tuple 来完成这个任务

```
//declaring the function
myFunction () -> (value1: Int, value2: String) {
    return (value1, value2)
}

//calling the function
let result = myFunction(value1: 12, value2: "Hello from me")

//we can access the return value like below
result.value1
result.value2 
```

Enter fullscreen mode Exit fullscreen mode

### 带有默认值的功能参数

我们可以为任何函数参数定义一个默认值，以防我们不想一直传递该值

```
func someFunction(parameterWithDefault: Int = 32) {} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们调用`someFunction(parameterWithDefault: 50)`并传入一个参数 50，它将覆盖赋给函数的默认值 32，我们也可以调用不带参数的函数。

### 功能参数外部和本地名称

有时我们需要给我们的函数一个外部和本地的名字，主要是为了可读性。外部名称用于将参数传递给函数，而内部名称用于访问函数内部的参数。

```
func myFunction(havingValue value: Int) { 
    //we can access the havingValue parameter with value 
    print(value)
}

//calling the function
myFunction(havingValue: 50) 
```

Enter fullscreen mode Exit fullscreen mode

注意，当我们调用函数时，我们使用`havingValue`来传递参数，但是要访问函数内部的参数，我们使用`value`。

### 省略自变量标签

每次我们调用一个有参数的函数时，我们都像下面这样定义参数名

```
myFunction(label: Int) {}

//calling the function
myFunction(label: 32) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过添加一个`_`作为函数外部名称来在调用函数时省略`label`，如下图

```
myFunction(_ label: Int) {}

//calling the function without the label definition
myFunction(32) 
```

Enter fullscreen mode Exit fullscreen mode

swift 的功能远不止我在这里介绍的内容，这里只是对它的一个介绍。你可以在苹果文档上的这里阅读更多关于 swift 功能

很高兴与你分享这篇文章。如果你喜欢这篇文章，请鼓掌表示支持👏*。感谢您的宝贵时间，请务必关注我或在*下方留下您的评论👇