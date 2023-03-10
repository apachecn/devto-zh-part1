# 揭开先进科特林概念的神秘面纱第一部分

> 原文：<https://dev.to/praveenkajla/demystifying-advance-kotlin-concepts-a97>

自从谷歌将其正式应用于安卓系统，我就一直有学习 kotlin 的冲动。由于我来自 java 背景，这些基础知识很容易掌握，但是我不熟悉函数式编程范例。所以学完基础就不学科特林了。然后我学习了 javascript 中的函数式编程，这就像一阵风一样，我突然发现概念是一样的，比如 HOC、arrow 函数、纯函数，只是语法不同。这篇文章假设你知道一些函数式编程的原理，但是如果你不知道也不用担心。你可以[观看这个令人敬畏的介绍](https://www.youtube.com/watch?v=e-5obm1G_FY)。够了，让我们登上这艘船去科特林。

# 功能

## 01。本地功能

```
fun OuterFunction(param:Int){
  val outerVar = 11
  fun localFunction(){
    println(params)
    println(outerVar)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

局部函数是函数内部的函数。局部函数可以访问外部函数的参数及其局部变量(即闭包)。

**有什么用**

当我们想做代码重用时，这是很有用的，例如，我们不想创建一个顶级函数，或者我们不想
在类之外创建一个成员函数。这样分组比较好。

**注意**
对于 outerFunction 之外的其他函数，我们无法访问 outerFunction 的 localFunctions。

## 02。中缀函数

对于有单个参数的扩展函数或成员函数，我们可以用中缀符号
来调用它们

```
infix fun String.extensionFunction(x: Int): Int {
...
}
//can be called as 

val str = "hello"
str extensionFunction 2 
```

Enter fullscreen mode Exit fullscreen mode

在`UNIT TESTING`中有用

## 03。匿名函数

我们已经看到了`higher-order functions`，其中 lambda 表达式作为代码块传递。

所以`Anonymous Functions`略有不同。

```
fun op(x:Int,op:(Int) -> Int):Int{
  return op(x)
} 
```

Enter fullscreen mode Exit fullscreen mode

**lambdas**T2】

```
//called like

op(3,{it*it})
//here {it*it} is lambda expression 
```

Enter fullscreen mode Exit fullscreen mode

**匿名**

```
//can have multiple returns

op(3,fun(x):Int{
         if(x>10) return 0
         else return x*x
        }) 
```

Enter fullscreen mode Exit fullscreen mode

有正常功能的完整身体但没有名字。

## 04。内嵌函数

kotlin 中的 lambda 表达式让位于 java 中的匿名类。这增加了它的开销，如果 lambda 表达式有一个闭包，它将导致一个实例也被创建，从而增加了更多的内存开销。

此外，所有这些 lambda 表达式都影响着调用堆栈。
**它影响性能**。

使用`Inline Functions`我们可以最小化这些影响。

**非直线**

```
 fun op(op:()->Unit){
    println("This is before lambda")
    op()
    println("this is after lambda")
}

fun main(args: Array<String>) {
    op({println("this is the actual function")})
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到反编译的 kotlin 字节码
[非线性](https://gist.github.com/praveenKajla/8d9675b9a4b0533bed046dc8af2c97bc)

**内联**

```
 inline fun op(op:()->Unit){
    println("This is before lambda")
    op()
    println("this is after lambda")
}

fun main(args: Array<String>) {
    op({println("this is the actual function")})
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到反编译的 kotlin 字节码
[内联](https://gist.github.com/praveenKajla/6167a975afd6526012687265cd56eb1d)

如果你比较两个反编译版本，你可以看到**内联**修饰符做的是

**将整个内联函数代码复制到我们调用该函数的地方，它也内联(复制)被传递的 lambda 的代码**

内联接受一个更高阶的函数，并将其内联(复制并粘贴该函数的内容)传递到被调用的地方。

所以，本质上是把它展平，它在说，不是我调用这个函数，而是我把代码粘贴到那里。

这为我们提供了优化，因为我们消除了所有不必要的匿名类或调用堆栈等。

当然，由于我们是复制和粘贴代码，它也有自己的副作用。所以当我传入 lambdas 时，inline 修饰符显然是有用的，否则它就没有意义。

注意
如果我们想内联 lambda 函数，我们实际上不能存储对它引用。

```
 inline fun op(op:()->Unit){
    val reference = op //this would say illegal usage
    op()
    println("this is after lambda")
}

fun main(args: Array<String>) {
    op({println("this is the actual function")})
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你将多个 lambda 作为参数传递给高阶函数，你可以通过在前面使用`noinline`修饰符明确地告诉编译器不要内联特定的 lambda 函数。

如果函数体很大，你可能不想内联它。

## 05。退货和本地退货

让我们举一个例子

```
fun ContainingFunction(){
  val nums=1..100
  numbers.forEach{
    if(it%5==0){
      return
      }
    }
  println("Hello!")
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行这个，你会得到一个空的输出，即没有 ***Hello！*** 印出来。
因为 forEach 函数的输入参数是一个 lambda 表达式，而
return 语句让我们从包含函数本身而不是 lambda 表达式返回。

**那么如何从 lambda 返回而不是包含函数**

似乎 kotln 有一个方法，即改变返回语句

**标签**

```
...
  if(it%5==0){
    return@forEach //i.e. name of the higher order function
  }
...
//or you could define your own labels
numbers.forEach myLabel@{
          if(it%5==0){
            return@myLabel  
         }
   .... 
```

Enter fullscreen mode Exit fullscreen mode

**注意**
但是如果我们使用匿名函数作为参数，它将从匿名函数本身返回，没有任何标签

此外，非局部返回仅在从内联函数调用时才被允许。我的意思是，如果我们查看 kotlin 的 [forEach](https://gist.github.com/praveenKajla/e26a86535f25d8da772fb20e41d61b7f) 代码，你会发现它有内联修饰符

因此，如果我们从 forEach 中移除内联修饰符，它将显示错误。

## 06。Lambda 扩展

或λ接收器

```
//lambdas
f:() -> Unit
//lambda extension
f:SomeFunctionOrClass.() -> Unit 
```

Enter fullscreen mode Exit fullscreen mode

花点时间看看这个例子 [resthandler](https://gist.github.com/praveenKajla/636cce1bfabe16dc6f4ae0928f06b77c)

我们可以看到 routeHandler 将 lambda 扩展作为参数

```
fun routeHandler(path:String,f:RouteHandler.() -> Unit):RouteHandler.() -> Unit = f 
```

Enter fullscreen mode Exit fullscreen mode

因此，所有扩展函数都可以访问它们正在扩展的类的成员，即 routeHandler 拥有 RouteHandler 类的请求和响应成员

**这有助于我们创建一个非常流畅、富有表现力的 DSL**
[进一步的例子](https://kotlinlang.org/docs/reference/type-safe-builders.html)

## 06。在 Kotlin 中调用实例

或λ接收器

```
... main{
...
  val manager = Manager()
  ...
 }
 class Manager{

 } 
```

Enter fullscreen mode Exit fullscreen mode

现在想象一个场景，我想通过使用像
一样的实例
来调用 Manager 类的一些功能

```
manager("do Something") 
```

Enter fullscreen mode Exit fullscreen mode

我们可以和科特林一起做

我们可以简单地实现一个名为 **invoke** 的函数，它被定义为一个操作符，可以接受任何参数

```
class Manager{
  operator fun invoke(value:String){
    prinln(value)
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

[下一部分:课程](https://dev.to/praveenkajla/demystifying-advanced-kotlin-concepts-pt2-1-5bl)