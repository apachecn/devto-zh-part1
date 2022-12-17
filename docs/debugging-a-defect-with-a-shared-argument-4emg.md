# 用共享参数调试缺陷

> 原文：<https://dev.to/mortoray/debugging-a-defect-with-a-shared-argument-4emg>

在编写一些错误处理助手时，在我的算法流中，我遇到了一个缺陷。在[叶](http://leaflang.org/)中处理函数的共享参数传递是一个棘手的问题。

经过大量的搜索，我设法将它简化为最小的单元测试。

```
/* EXPECT(3) */
var a : integer shared := 2

defn pine = (b : integer shared) -> {
    var c : integer shared := 3
    b = c
    trace(b)
}

pine(a) 
```

Enter fullscreen mode Exit fullscreen mode

这个函数很傻，但足以证明一个错误。它表现为严重的探测`abort: duplicate-release-shared`错误。

出于好奇，这个例子是从下面的错误报告函数简化而来的。

```
@export defn print = ( ofi : ⁑fail_info optional shared ) -> {
    println(["Error:"])
     while has(ofi) {
        var q = getopt(ofi)
         println(["\t",q.tag])
         ofi = q.next
     }
} 
```

Enter fullscreen mode Exit fullscreen mode

它遍历错误中的标记并将它们打印到控制台。它基本上是有效的，除了在`ofi = q.next`行的一个问题——它也导致了那个讨厌的`duplicate-release-shared`错误。

## 这是所有权的问题

错误在于所有权:两条代码路径释放了相同的值。考虑这个基本代码:

```
var b : shared integer := 5
var c : shared integer := 6

b = c 
```

Enter fullscreen mode Exit fullscreen mode

`b`和`c`都是共享值。这些大致相当于 C++中的`shared_ptr<integer>`，或者 Java 中的`Integer`类型。当我们给`b = c`赋值时，我们不是在修改值，而是在改变引用。关于这个概念的更多细节，你可以参考我的文章[让名字脱离它的价值](https://mortoray.com/2013/01/18/divorcing-a-value-from-its-name/)。

Leaf 使用引用计数；当我们给一个共享变量赋值时，大概会发生这样的情况:

```
// b = c
acquire(c)
release(b)
assign(b, c) 
```

Enter fullscreen mode Exit fullscreen mode

我们在`b`中释放旧对象，获取新对象`c`，然后分配引用(一个内存指针)。

再回头看看测试用例，`b = c`来自那个例子:

```
defn pine = (b : integer shared) -> {
    var c : integer shared := 3
    b = c
    trace(b)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题是，`b`是函数的一个参数，所以只有当我们拥有这个变量时，才允许我们`release`它。原来我们没有！

考虑呼叫方:

```
pine(a) 
```

Enter fullscreen mode Exit fullscreen mode

简化为伪代码，再次基于引用计数:

```
var a_tmp = acquire(a)
pine( a_tmp )
release( a_tmp ) 
```

Enter fullscreen mode Exit fullscreen mode

这段代码确保在函数调用期间对`a`的引用是有效的。由于`a`是全局可修改的，我们需要在这里谨慎行事。问题是上面的代码调用了`release(a_tmp)`。但是我们的`pine`函数也在做`release(b)`，恰好和`a_tmp`是同一个共享对象。因此，我们已经释放了对象两次！

## 逻辑上回避问题

这个问题可以通过在函数中局部隐藏所有参数来避免。

```
defn pine = ( _b : integer shared) -> {
    var b = _b
    var c : integer shared := 3
    b = c
    trace(b)
} 
```

Enter fullscreen mode Exit fullscreen mode

由于函数做的第一件事是`acquire(_b)`，最终的`release(b)`不会触及源参数，所以隐藏是有效的。

对所有可能被修改的参数都这样做有点矫枉过正的感觉。我想编译器可以扫描代码并找出答案。或者，我可以不允许修改函数参数。这就是我如何得出我的问题[函数参数应该是可重赋值的还是可变的？](https://mortoray.com/2017/12/31/should-function-arguments-be-reassignable-or-mutable/)。

为了保证编译器的完整性，我可能会添加一个补丁(局部隐藏)，并在默认情况下禁止赋值给参数。然后使用一些魔法来尝试优化掉所有不需要的情况——尽管流行的观点似乎倾向于禁止修改论点。

> 请务必访问 [Leaf 站点](http://leaflang.org)以了解更多关于我的编程语言的信息。你也可以[在 Twitter 上关注我](https://twitter.com/edaqa)或者[订阅我的直播](https://www.twitch.tv/mortoray)并随时提问。