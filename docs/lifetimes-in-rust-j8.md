# 生锈寿命

> 原文：<https://dev.to/zeerorg/lifetimes-in-rust-j8>

# 简介

Rust 是一种系统编程语言，它通过采用全新的内存管理方法来保证内存安全，并防止与之相关的问题，如线程间的数据竞争、内存泄漏和悬空指针。在这篇文章中，我想讨论一种他们称之为生命周期的方法。

## 借款及所有权简介

借用、所有权和生存期，这三个概念在 Rust 中相互补充，并在运行时提供内存保证。为了保持我们对生命周期的关注，我们将简要地看看其他的是什么。

#### 借款

示例:`let x = &y`
这里的`x`引用`y`，并且在功能上“几乎”类似于 C/C++引用。

#### 所有权

Rust 没有垃圾收集器，所以它遵循一个简单的规则来清除分配的内存。

```
fn foo() {
    let v = [1, 2, 3];
} 
```

Enter fullscreen mode Exit fullscreen mode

当`v`超出范围时，它的内存将被清除，即使它是动态分配的。

# 那么什么是一生呢？

用外行的话来说，一个东西的寿命是“这个东西可用的时间长度”，在 Rust 中意思差不多，这里的“东西”是“变量”。

看看这段代码，以及编译器在注释中生成的错误:

```
fn main() {
    let r;              
    {
        let i = 1;      
        r = &i;         // borrow occurs here
    }                   // `i` dropped here while still borrowed

    println!("{}", r);
}    // borrowed value needs to live until here 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的是:

1.  我们声明一个变量`r`
2.  我们进入范围 2.1。我们初始化一个变量`i` 2.2。给`r`分配一个`i`的参考
3.  超出范围
4.  使用`r`

在步骤 3，所发生的是用于`i`的内存被释放并且`r`指向一个垃圾内存位置。

在步骤 4 之后，编译器给了我们一个甜蜜的错误信息，把我们从 C/C++会给我们带来的运行时灾难中拯救了出来。

# 好吧，但何必在意呢？

再来看另一个例子:

```
struct Car {
    model: &str
} 
```

Enter fullscreen mode Exit fullscreen mode

您创建了一个结构，并愉快地单击了编译按钮，但是...

```
error[E0106]: missing lifetime specifier

model: &str
        ^ expected lifetime parameter 
```

Enter fullscreen mode Exit fullscreen mode

你收到这条信息。你在挠头，想着“我没做错什么”。

嗯，根据编译器的逻辑，结构范围一结束，引用`model`就会被销毁。那么怎么修复呢:

```
struct Car<'a> {
    model: &'a str
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意`'a`，我们已经明确地为变量的生命周期指定了一个名称，然后在语句`model: &'a str`中，我们告诉编译器只要我们拥有类型为`Car`的变量，就保留引用。

需要注意的一点是，生存期总是存在的，但大多数时候它们是由编译器自动隐含的，所以你不必在每次借用引用时都显式地分配它们。

# 向上调平

我们来看另一个代码:

```
struct Person<'a> {
    name: &'a str,
    do_task: &'a Fn(&str)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里`do_task`是对一个函数的引用，它借用了一个字符串引用，“但是...我们没有定义`&'a str`”。`&str`意味着它依赖于函数的作用域，因此当函数完成时，字符串引用将被清除。

而`do_task`和`name`被绑定到`Person`类型的变量的范围。

生存期是一个新的有趣的概念，但第一次可能很难理解。您可以参考的其他资源有:

*   [生锈示例:寿命](https://rustbyexample.com/scope/lifetime.html)
*   [生锈文档:寿命](https://doc.rust-lang.org/1.9.0/book/lifetimes.html)

编码快乐！！