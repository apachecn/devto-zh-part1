# Rust 入门:函数和结构

> 原文：<https://dev.to/mnivoliez/getting-started-with-rust-function-and-struct-397>

*最初发布在我的[博客](https://mathieu-nivoliez.com/posts/2017-12-05-getting-started-with-rust-function-and-struct)*

大家好！今天我们继续 rust 系列，我们将讨论函数和结构。

## “好像很多？我们真的要报道所有这些吗？”

是啊是啊。

为了做到这一切，我们需要一个变量，让我们定义它:

```
// We want to buy a fallout boy, so lets define a number of caps.
let caps = 5; 
```

Enter fullscreen mode Exit fullscreen mode

我们只是定义了一个值为 5 的变量上限。

## “等一下！之前的帖子不是说 rust 是强类型语言吗？”

是的，这是真的。我来给你解释一下类型推理的美好世界吧！
在编译时，rust 编译器会试图从`caps`的使用(赋值或操作)中猜测出它的类型。
我说*试试*，因为有时候，rust 编译器不会成功。例如，如果使用大写字母不允许定义其类型。
这里的类型推断是为了避免编写容易被猜到的类型。
你仍然可以用下面的语法定义类型:

```
let caps: usize /* type define here */ = 5; 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，变量可以被隐藏。想象一下，你得到了一个字符串形式的大写字母的数目，仍然称它为大写字母是有意义的。这就是跟踪的意义所在。让我给你看看:

```
let caps = "five";
let caps = get_number_from_string(caps); // caps is now 5 
```

Enter fullscreen mode Exit fullscreen mode

最后但同样重要的是，之前创建的变量在默认情况下是不可变的。这意味着在声明它们之后不能更改它们。为此，你必须明确地告诉编译器:

```
let mut caps;
caps = 5; 
```

Enter fullscreen mode Exit fullscreen mode

在隐藏的情况下，variabke 本身是不一样的。我们摧毁它，重新创造一个新的。算是吧。

可能现在还不直观，但是通过练习，会的。

## “好吧，但是这个 *get_number_from_string* 的事情呢？”

很好的过渡，就好像我在写你的台词。
*get _ number _ from _ string*是一个函数。让我们看看如何定义它:

```
fn get_number_from_string( num: &'static str ) -> usize {...} 
```

Enter fullscreen mode Exit fullscreen mode

## “坚持住！！！！这是什么？？？？?"

好了，我们停一下。`fn`是表示我们正在声明一个函数的关键字。`get_number_from_string`是函数的名称，`num: &'static str`是我们传递给函数的参数，在本例中，是对一个[字符串片](https://doc.rust-lang.org/std/str/)的引用。我们回到正题。然后我们得到了`-> usize`，它表明我们返回了`usize`类型的东西。最后是函数体。
简单点说，养成大声朗读代码的习惯:*函数`get_number_from_string`接受一个字符串形式的数字，并返回一个无符号的 int 值。我强烈认为代码应该像一个好故事一样易读。
函数用来把你的代码分成你**能**理解的单元。*

我可以永远谈论函数，但你必须练习才能理解它。

现在我们来谈谈结构。

## “Struct，是 strucutre 的简称吗？”

你是对的。猜猜我们用结构做什么？

## “我们用它来...拿着东西，给东西一个我们可以依靠的形状？”

没错。这正是我们对`struct`所做的。

你还记得前面的`caps`变量吗？就是现在这个，觉得老了没？

```
struct CapsPurse {
  caps: usize // unsigned int is all we need. We can't have a negative value of caps.
}

//lets implements some functionalities to it.

impl CapsPurse {
  pub fn new() -> CapsPurse {
    CapsPurse { caps: 0 }
  }

  // we want to add and remove caps to it.
  pub fn add_caps(&mut self, amount: usize) {
    self.caps += amount; 
  }

  pub fn remove_caps(&mut self, amount: usize) {
    if amount <= self.caps {
      self.caps -= amount;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们回顾一下这里发生了什么。首先，我们定义一个用来装帽子的钱包。我们定义一个方法来创建钱包，我们定义方法来添加或删除它的上限。像以前一样，大声读出方法签名，它应该清楚地指出它是做什么的。
最终，你会遇到两个未知元素:`pub`和`self`。`pub`告诉编译器这个方法可以从结构体的外部看到。为了正确解释这个概念，让我们用身体来解释。我们的手臂是公开的，我们的脸也是。每个人都可以看到它们，并在一定范围内触摸它们。另一方面，你的生殖器是私人的。你把它们藏在裤子里不让外界看到。只有你能触摸和看见它们。当然，在你的生活和你的属性中有例外，但在代码中没有。
所以`pub`的意思是，它就像一只手臂，当它不在这里时，它就像生殖器:私人的。
另一个符号`self`，指的是你调用方法的结构。
目前我不知道如何以不同的方式解释`self`的概念。

## “好的，...能给我来杯咖啡吗？”

同意。这不是一件简单的事情。但是坚持住，困难的部分就在前面。我们稍后会继续，因为这篇文章已经够长了。

我试图让这篇文章尽可能的准确和易懂。如果有任何错误或不准确的地方，请留下评论。我很乐意纠正它。如果你对我应该如何解释事情有什么建议，我也一样。

下一集再见。

马修