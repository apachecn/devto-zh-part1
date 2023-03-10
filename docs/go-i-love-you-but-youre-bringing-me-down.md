# 去吧，我爱你，但你让我失望。

> 原文：<https://dev.to/loderunner/go-i-love-you-but-youre-bringing-me-down>

我以前说过，现在再说一遍:我真的很喜欢围棋。这是我学习语言以来最有趣的一次。它是静态类型和编译的——作为一个面向系统的程序员，我从来没有发现任何可以真正让我远离 C 的东西——它有一个很棒的标准库，它很好地处理代码重用而无需继承，这些并发范例永远改变了我的生活。

也就是说，我很快就对这种语言的一些细节产生了一些问题。虽然我一直在尽我所能地学习它的模式和约定，但我非常确定其中一些可以在语言内部解决，而不是通过使用特定的模式或抽象。为了[简单](https://dave.cheney.net/2017/06/15/simplicity-debt)。

当我在阅读 Russ Cox 关于 Go 2 的 [Gophercon 2017 演讲](https://blog.golang.org/toward-go2)时，我决定接受他的观点:

> 我们需要你的帮助。
> 今天，我们最需要的是经验报告。请告诉我们 Go 是如何为你工作的，更重要的是不为你工作。写一篇博文，包括真实的例子、具体的细节和真实的经历。这就是我们如何开始讨论我们，围棋界，想要改变围棋的原因。

因此，作为一个新的围棋程序员，我想说的是，我还缺少什么。

## 运算符重载

引起你的注意了吗？很好。因为我讨厌运算符重载。我经历过太多的 C++代码库，开发人员认为聪明比写一个干净的接口要好。我曾经遇到过一些过分热衷于重新定义基本语义的程序员，比如点(`.`)、箭头(`->`)或调用(`()`)操作符来做一些意想不到的事情。文档和新开发人员入职的成本是巨大的，编码时转换范式的脑力劳动使整个事情非常容易出错。stdlib 可以很自由地做奇怪的事情，有人知道吗？)并没有真正的帮助。

但是操作符也是一些明确定义的语义的一个很好的速记。换句话说，如果语义*先于*操作符，那么滥用它们的机会就小得多。一个例子是用于基于整数的索引(如数组或切片)或键值操作(如映射)的`[]`操作符。或者使用`range`关键字迭代数组、切片、图和通道。

确保这些语义得到充分尊重的一种方法是将操作符作为定义良好的接口的语法糖。

键值运算符:

```
struct KeyValueMap interface {
    Value(interface{}) interface{}
    SetValue(interface{}, interface{})
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以使用`[]`操作符作为这些函数的简写。

```
foo := keyVal["bar"] 
```

Enter fullscreen mode Exit fullscreen mode

相当于

```
foo := keyVal.Value("bar") 
```

Enter fullscreen mode Exit fullscreen mode

用作键的对象需要是可散列的或者在某种程度上是可比较的。也许编译器可以执行与映射相同的规则。

范围迭代可以类似地进行:

```
type Iterator interface {
    Next() (interface{}, bool)
    Reset()
} 
```

Enter fullscreen mode Exit fullscreen mode

`Next`将返回迭代中的下一个值，当迭代器结束时，`bool`将返回`true`。`Reset`将用于在`for`循环的开始初始化迭代器。

```
for v := range it { 
```

Enter fullscreen mode Exit fullscreen mode

相当于

```
it.Reset()
for !done {
    i, done := it.Next()
    if done {
        break
    } 
```

Enter fullscreen mode Exit fullscreen mode

我认为这将允许 Go 开发者实现更多的 go-ish 结构，我可以看到许多使用这种简洁语法的内存高效迭代器的例子。

当然，这种打破了围棋中的强打字。也许像这样的特性会与泛型携手并进？

## 嵌入式接口的初始化

我们最近遇到了一个问题，花了我们一段时间来调试。我们使用类型嵌入在一个结构中组合不同的服务。起初，我们只有一个处理程序。

```
type FooHandler interface {
    // some `FooHandler` methods
}

type Handler struct {
   FooHandler
}

func main() {
    // Foo is a concrete type implementing FooHandler
    h := Handler{FooHandler: &Foo{}}
    RegisterHandler(h) 
```

Enter fullscreen mode Exit fullscreen mode

我们嵌入了第二个处理程序，但是忘记在`Handler`初始化中添加它。这个错误信息相当隐晦。

```
panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=0x1 addr=0x20 pc=0x10871d6]

goroutine 1 [running]:
main.RegisterHandler(0x1108140, 0x1126ac8, 0x0, 0x0) 
```

Enter fullscreen mode Exit fullscreen mode

通过添加初始化已修复此问题。

```
 h := Handler{FooHandler: &Foo{}, BarHandler: &Bar{}} 
```

Enter fullscreen mode Exit fullscreen mode

我希望错误信息能更清楚一些。这么简单的遗漏不应该需要 30 分钟和两个程序员。也许我们只是围棋新手，但我希望错误能更明显一些。或者编译器可以警告不要对嵌入类型使用零值。我不知道是否有这样的情况，你会想要嵌入一个`nil`值。对于一些代码库来说，这可能是一个突破性的改变，但是在内存安全方面的好处是值得的。

## 关闭通道

> 我在这里详述的问题仅仅是因为这种语言的一个我不知道的特性。评论和[推特](https://twitter.com/Sajma/status/889573024642584576)(这里还有)上都指出了这一点。我会告诉你如何避免我遇到的问题。

在下面的程序中，当从两个通道`ca`和`cb`接收零值时，最终循环将永远循环下去。

```
func main() {
    // Create a first channel
    ca := make(chan int)
    go func() {
        // Send one integer over the channel every millisecond up to 9
        for i := 0; i < 10; i++ {
            ca <- i
            time.Sleep(1 * time.Millisecond)
        }
        close(ca)
    }()

    // Create a second channel
    cb := make(chan int)
    go func() {
        // Send one integer over the channel every millisecond up to 99
        for i := 0; i < 100; i++ {
            cb <- i
            time.Sleep(1 * time.Millisecond)
        }
        close(cb)
    }()

    // Receive from the first available channel and loop
    for {
        select {
        case n := <-ca:
            fmt.Printf("a%d ", n)
        case n := <-cb:
            fmt.Printf("b%d ", n)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个问题可以通过从通道读取两个值来解决。通过使用`n, ok := <-ca`，如果通道关闭，`ok`将成为`false`。一旦通道被关闭，我们将它设置为`nil`作为从`nil`通道接收阻塞。

```
 // Read from first available channel and loop
    // until both channels are nil
    for ca != nil || cb != nil {
        select {
        case n, ok := <-ca:
            if ok {
                fmt.Printf("a%d ", n)
            } else {
                // If the channel has been closed, set it to nil
                // Receiving from a nil channel blocks, so we know
                // this select branch will be unreachable after this
                ca = nil
            }
        case n, ok := <-cb:
            if ok {
                fmt.Printf("b%d ", n)
            } else {
                cb = nil
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我仍然是一个新手，所以我相当肯定这些要么是我的误解，要么是因为它们不起作用而被尝试和放弃的东西。欢迎在评论中给我任何文档或建议。