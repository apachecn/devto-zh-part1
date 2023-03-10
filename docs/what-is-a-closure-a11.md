# 什么是终结？

> 原文：<https://dev.to/mortoray/what-is-a-closure-a11>

你可能听说过这个术语，甚至用过，但是到底什么是结束呢？它是数据和代码的结合，已经成为现代编程的主要内容。它们提供了自然的功能特性。即使你不能完全理解它们，也非常有用。让我们仔细看看，揭开这个奇怪构造的神秘面纱。

## 一个简单的例子

我们可以从一个简单的局部形式开始:

```
defn pine = -> {
    var x = 1

    defn incr = -> {
        print(x)
        x = x + 1
    }

    incr() // prints 1
    incr() // prints 2
} 
```

Enter fullscreen mode Exit fullscreen mode

这里定义了一个函数`pine`。在这个函数中，我们定义了另一个名为`incr`的函数。在`incr`中使用的`x`变量没有在这里定义:它是`pine`函数的局部变量的一部分。函数`incr`是其代码和周围范围内变量的闭包。

通过调用`incr`两次，我们看到`x`在两次调用之间持续存在。此外，`x`在作用域:
之间共享

```
 incr() // 1
    print(x) // 2
    x = 4
    incr() // 4 
```

Enter fullscreen mode Exit fullscreen mode

`incr`和`pine`作用域指的是同一个`x`。这取决于语言，在 Leaf 中，这里显示的，这种共享是默认的。在 C++和其他语言中，克隆是默认的；在这种情况下，该值不是共享的，而是在创建函数时复制一份。例如:

```
defn pine = -> {
    var x = 5

    //a copy of `x` is made now
    defn incr = -> clone {
        print(x)
        x = x + 1
    }

    incr() // 5
    print(x) // still 5 (would be 6 if shared)
    x = 1
    incr() // 6 (would be 1 if shared)
    print(x) // 1
} 
```

Enter fullscreen mode Exit fullscreen mode

对`incr`的后续调用使用同一个`x`，但是`pine`中的调用独立于它。这就产生了两个不同的`x`变量。这两种类型的闭包都结合了代码和数据范围。结果大相径庭，所以知道你面对的是什么类型很重要。

## 高阶函数

当我们把闭包和高阶函数结合起来时，我们得到了有趣的新的可能性。例如，闭包的数据范围可以在创建它的函数之外持续存在。

```
defn addr = (x) -> {
    var f = (y) -> {
        return x + y
    }

    return f
}

var a5 = addr(5)
print( a5(1) ) // 6
pinrt( a5(3) ) // 8

var a7 = addr(7)
print( a7(1) ) // 8
pinrt( a7(3) ) // 10 
```

Enter fullscreen mode Exit fullscreen mode

`addr(5)`正在创建一个将`5`加到一个数字上的函数。不过，我们没有从`addr`内部调用那个函数，而是将它赋给变量`a5`，然后调用它。当`addr(7)`被调用时，它创建一个新的环境，它的`x`值与来自`a5`的值不同。

闭包可以传递给一个对周围范围一无所知的函数。

```
defn key_sort( data : listï½¢anyï½£, key : string ) {
    sort( data, (a,b) -> {
        return a#key < b#key
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

`sort`函数需要两个对象的比较器。它不知道这种比较是如何进行的，也不知道它可能正在从封闭范围访问数据。这段代码演示了我们传递给`sort`的闭包真正封装了代码和数据，而不仅仅是一些语法欺骗。

> 在许多使用闭包的情况下，编译器很可能选择使用欺骗手段。最通用的方法，适用于`sort`和`return`的方法有一点开销。前面用`incr`展示的更简单的方法，通常可以在没有任何运行时闭包支持的情况下编译。

## 高度感兴趣的技术单位

以上基本上解释了什么是闭包，以及如何使用它。如果你对语言理论感兴趣，你可能对一些细节不满意。如果您对语言理论不感兴趣，现在可以停止阅读，尽情享受闭包带来的乐趣。

让我们回到这段代码:

```
defn pine = -> {
    var x = 1

    defn incr = -> {
        print(x)
        x = x + 1
    }

    incr() // 1
    incr() // 2
} 
```

Enter fullscreen mode Exit fullscreen mode

我称之为`incr`闭包，这在技术上可能不正确——这取决于语言。在 Leaf 中，它还不是一个闭包，而是一个接受“上下文”的函数。和 C 中的这个函数差不了多少:

```
struct pine_context {
    int x;
}

void incr( pine_context * pctx ) {
    print( pctx->x );
    pctx->x++;
} 
```

Enter fullscreen mode Exit fullscreen mode

当函数被调用时，`incr()`，闭包就产生了。编译器发现该函数需要一个`pine_context`环境；它在直接封闭的范围内找到一个，即`pine`，这似乎是合乎逻辑的。它将上下文绑定到函数，然后进行调用。

> 这是一个编译器可以使用诡计的地方。它避免创建任何闭包对象，而是用`pine_context`调用`incr`函数。这种优化允许使用本地闭包，而没有开销成本。

如果`incr`会超出其声明的范围，也需要进行这种绑定。

```
defn pine = -> {
    var x = 1

    defn incr = -> {
        print(x)
        x = x + 1
    }

    return incr
}

var q = pine()
q() // 1
q() // 2 
```

Enter fullscreen mode Exit fullscreen mode

当编译器遇到`return`语句时，它意识到需要在返回之前将`incr`函数绑定到`pine_context`。return 语句之后，就没有进一步查找上下文的机会了。

> 闭包不就是一个伪装的类和实例吗？好观察力。是的，通常有语法上的差异，和一些行为上的差异，但是它们都模拟了数据和代码之间相同的关系。在 Leaf 编译器中，它们大多共享相同的处理代码。

## 只是表面现象

使用闭包，语言的行为会有很大的不同。重要的是要明白，从根本上来说，它只是一个函数和一些数据的组合。知道范围是共享的还是克隆的是一个重要的细节。或者在可能的情况下，如在 C++中，共享部分上下文，克隆部分上下文。

我已经稍微深入到了叶子的特定行为，只是为了给出一个复杂的概念。如果您想更深入地了解闭包是如何实现的，请告诉我。