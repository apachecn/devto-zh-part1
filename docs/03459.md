# Pony 编程语言简介

> 原文：<https://dev.to/roperzh/a-brief-dive-into-the-pony-programming-language-39e>

*本帖最初发表于[monades.roperzh.com](https://monades.roperzh.com/pony-programming-language/?cm=devto)T3】*

Pony 是一种新兴的编程语言，其作者将其描述为“一种开源、面向对象、角色模型、功能安全的高性能编程语言。”

抛开措辞不谈，本文主要关注语言本身最有趣的方面以及代码的外观。

## 编制完成

Pony 处于编译语言的统治之下，当执行`ponyc`命令(这是 Pony 安装的一部分)时，编译器会创建一个与当前文件夹同名的可执行文件。

```
$ cd myprogram
$ ponyc
$ ./myprogram 
```

## 静态键入

Pony 提供的许多保证(运行时无异常、安全功能等等)都由它的类型系统支持。如果您遗漏了一个类型或者将一个值赋给了一个不同类型的变量，编译器会报错并停止编译。

表示类型的语法是`variable: Type`。

```
class Hello
  // name is a variable of type String
  let name: String
  // age is a variable of type U64
  let age: U64

  // the argument name' is a variable of type string
  new create(name': String) =>
    name = name' 
```

但是，在某些情况下，类型是由编译器推断出来的。例如，这里可以安全地省略`hello`变量的类型。

```
let hello = Hello("Seneca") 
```

## 面向对象

Pony 是面向对象的，用类来公开。一个类是用关键字`class`声明的，可以有:

*   通过名称区分的一个或多个*构造函数*
*   *字段*
*   *功能*

与许多其他 OOP 语言不同，Pony 没有继承的概念，而是包含了[特征和接口](https://tutorial.ponylang.org/types/traits-and-interfaces.html)。

```
class Book
  // Fields of a given class are defined
  // like this.
  // - `var` fields can be asigned multiple times
  // - `let` fields can only be assigned once in the constructor.
  // - fields starting with underscore are private
  let author: String
  var _editions: U32

  new create(author': String, editions': U32) =>
    author = author'
    _editions = editions'

  // A class can have functions
  fun get_author(): String => author

  fun get_editions(): U32 => _editions

  // To set values, is necessary to mark
  // the function as `ref`
  fun ref set_editions(to: U32) => _editions = to 
```

## 演员模特

小马拥抱演员模式，支持开箱即用。如果你没听说过，有兴趣的话，可以去看看[了解一下演员模特](https://monades.roperzh.com/get-to-know-the-actor-model/)。

在 Pony 世界中，参与者类似于类，唯一的区别是它们是异步实体。演员有行为而不是功能；当被调用时，行为体不会被同步执行，相反，它会在未来某个不确定的时间被执行。

```
use "promises"

actor Writer
  // As with classes, you can define fields
  let name: String

  // And constructors too!
  new create(name': String) =>
    name = name'

  // Instead of functions, an Actor has behaviors
  be get_name(promise: Promise[String]) => promise(name) 
```

### 与演员交流

因此 actors 的异步特性，Pony 也提供了原语来支持通过`Promise` s 的异步通信。`Promise` s 要么被满足，要么被拒绝。如果你对小马承诺的细节感兴趣，凯文·霍夫曼有一篇关于这个主题的极好的[博客文章](https://medium.com/@KevinHoffman/the-promise-of-ponys-future-44040a0b64ff)。

```
// Create a new Actor let writer = Writer("Italo Calvino")

// Create a new promise let promise = Promise[String]

// Invoke the Actor behavior, providing a
// promise to be fulfilled writer.get_name(promise)

// Print the value of the promise when fullfilled
// using a partial application of `env.out.print` promise.next[None](env.out~print()) 
```

## PonyTest

Pony 附带了`PonyTest`包，它提供了一个简单的单元测试框架:每个单元测试都是一个类，只有一个测试函数，默认情况下，所有的测试都是并发运行的。

```
use "ponytest"

actor Main is TestList
  new create(env: Env) =>
    PonyTest(env, this)

  fun tag tests(test: PonyTest) =>
    test(_TestAdd)

class iso _TestAdd is UnitTest
  fun name():String => "addition"

  fun apply(h: TestHelper) =>
    h.assert_eq[U32](4, 2 + 2) 
```

## 有趣的事实

*   您通过外部函数接口(FFI)将 Pony 与 C 集成在一起
*   允许除以零，结果为零
*   `a = b`是返回旧值`a`的表达式

## 统计数据

*   生命的 **~5 年**，2012 年 11 月 9 日首次承诺
*   **4549 次**提交
*   **46** 发布
*   **104** 贡献者
*   **2672**GitHub 明星
*   **76**stack overflow 中带有`pony`标记的问题

*注:这些数字的日期是 2017 年 10 月 31 日，随着时间的推移，它们变得越来越无效；此外，它们并不代表任何重要的东西，只是为了好玩才包含在内的。*

## 资源

*   [网站](https://www.ponylang.org/)
*   [教程](https://tutorial.ponylang.org/)
*   [常见模式食谱](https://patterns.ponylang.org/)
*   [例题](https://github.com/ponylang/ponyc/tree/master/examples)