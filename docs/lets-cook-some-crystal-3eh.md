# 让我们煮一些水晶吧！

> 原文：<https://dev.to/juliendemangeon/lets-cook-some-crystal-3eh>

注意:这个帖子最初发布在[marmelab.com](https://marmelab.com/blog/2017/12/14/lets-cook-some-crystal.html)。

水晶？你们中的一些人可能在有史以来最好的电视剧《绝命毒师》中听说过它！谢天谢地，我们不会在这篇文章中谈论药物，而是谈论我最近听说的一种非常棒的编程语言。

[![Breaking Bad Crystal Cooking](img/b9fd16f57a9bf4effe9d098aa86301e1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yWwJ-sH5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marmelab.cimg/blog/crystal_breakingbad.jpg)

在我不断探索发现新语言和范式的过程中，我遇到了水晶。Crystal 是一种编译型、[静态类型化、](https://en.wikipedia.org/wiki/Type_system#Static_type_checking)[面向对象的](https://en.wikipedia.org/wiki/Object-oriented_programming)语言。它的语法受到了 Ruby 的强烈启发，它的目标大部分与 [Golang](https://golang.org/) 相同。

像往常一样，当我学习一门新语言时，我开始了一个全新的项目，可以在 GitHub 上发布。我的用例:[一个 CLI app](https://github.com/marmelab/laurel) 旨在方便[凯撒密码](https://en.wikipedia.org/wiki/Caesar_cipher)操作。

在这篇文章中，我将尝试给出语言能力的一个大的概述，以及代码片段。

## 红玉，你在吗？

正如我在介绍中所说的，许多 Crystal 语法概念来自于 Ruby。这种句法选择的主要原因来自语言创造者的背景。

Ruby 是一种面向对象的、反射的和动态类型化的编程语言。它是 20 年前开发的，为开发者提供生产力和乐趣。在 *GitHub* 、 *Diaspora* 或 *Redmine* (我第一次快乐的 Ruby 体验)等许多中大型项目中使用过，并由 RoR ( *Ruby on Rails* )使之流行起来。

虽然 Ruby 是一种解释型的多平台语言(运行在 T2 的虚拟机上)，但 Crystal 不是。这意味着 Crystal 需要在执行它的平台上编译。

正如你稍后将要看到的，水晶不是红宝石，它是模仿红宝石的。如果你是一个 ruby 主义者，你会对静态类型和推理有点失望。即便如此，许多基本概念还是取自 Ruby。其中，我们找到了*【积木】**【符号】**【一切都是物体】的原理*。

### 晶体块

[块](https://crystal-lang.org/docs/syntax_and_semantics/blocks_and_procs.html)在水晶中大量使用(和在红宝石中一样多)。它们允许用自己的上下文捕获代码块，并在以后执行它。这和其他语言中的*闭包*或者*匿名函数*几乎是一回事。

这是我的玩具项目的一部分代码。

```
(1...Encoder::ALPHABET.size).each do |shift|
    decoded = Encoder.decode(encoded, shift)
    candidates << { key: decoded, count: ((decoded.split(' ') - [""]) & dictionary).size }
end 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，定义在`do |shift|`和`end`之间的块被调用用于`1`和`Encoder::ALPHABET.size`之间的每个字母(感谢[晶振范围](https://crystal-lang.org/docs/syntax_and_semantics/literals/range.html))。

由于`...`，最后一个字母(由`Encoder::ALPHABET.size`定义)被排除在范围之外。为了包含最后一个字母，有必要使用`..`，这是包容性的。

在`.each`调用中，该块被**与 [range.cr](https://github.com/crystal-lang/crystal/blob/v0.24.1/src/range.cr#L102) 中的相应函数“产出”。事实上，一个块是用 Crystal 中的关键字`yield`调用的，如下面的 doc 示例中所述。** 

```
def twice
  yield
  yield
end

twice do
  puts "Hello!"
end

# => prints "Hello!" twice 
```

Enter fullscreen mode Exit fullscreen mode

### 符号

符号是 Ruby 中另一个有趣的概念。一个水晶[符号](https://crystal-lang.org/api/0.23.1/Symbol.html)是一个常数，通过它的名字来识别。在内部，Crystal 拥有一个符号注册表，表示为 Int32。

```
 :hello === :hello # true
:hello === "hello" # false 
```

Enter fullscreen mode Exit fullscreen mode

符号与[仙丹原子](http://elixir-lang.github.io/getting-started/basic-types.html#atoms)非常相似。

### 一切都是物体

在 Crystal 中，就像在 Ruby 中一样，一切都是对象。实际上，自从读了这篇文章，你已经体验了不少于 3 个物体(块、符号和范围)。就像在 Ruby 中一样，所有对象都扩展了[对象](https://crystal-lang.org/api/0.20.1/Object.html)。当你写 Crystal 代码的时候，大多数时候，你写的是类。

```
module Curses
  class Window
  end
end

Curses::Window.new 
```

Enter fullscreen mode Exit fullscreen mode

然而，Crystal 也可以以功能性的方式使用。由于一个模块需要一个类，解决方案就是让模块[【自扩展】](https://crystal-lang.org/docs/syntax_and_semantics/modules.html#extend-self)。例如，我可以通过使用`extend self` :
来编写一个`Encoder`模块，使得*不包含*类

```
module Encoder
  extend self

  ALPHABET = "abcdefghijklmnopqrstuvwxyz "

  def encode(uncoded : String, shift : (Int32 | Int64))
      dictionary = create_shift_dictionary(shift)
      normalized_uncoded = normalize(uncoded)

      normalized_uncoded.tr(dictionary.keys.join, dictionary.values.join)
  end

  [...]

end 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以直接调用`encode()`方法，不需要支持类:

```
include Encoder
encode('o') 
```

Enter fullscreen mode Exit fullscreen mode

## 晶体功能

尽管 Crystal 借鉴了 Ruby 的一些概念，但它也带来了许多意想不到的功能，比如*类型推断*、*并发*和*编译时空引用检查*。

### 式推理的核心

*类型推断*是最有价值的晶体函数之一。它给你权力将类型属性过程委托给编译器。此外，结合[联合类型](https://crystal-lang.org/docs/syntax_and_semantics/union_types.html)，它允许给同一个变量赋予不同的类型。

```
if 1 + 2 == 3
  a = 1
else
  a = "hello"
end

a # : Int32 | String 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`a`的类型是在编译时从 [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) (抽象语法树)中自动推断出来的。也可以手动分配类型，如下所示。

```
a : (Int32 | String) = 1 
```

Enter fullscreen mode Exit fullscreen mode

类型推理也适用于[数组](https://crystal-lang.org/docs/syntax_and_semantics/literals/array.html)、[元组](https://crystal-lang.org/docs/syntax_and_semantics/literals/tuple.html)、[命名元组](https://crystal-lang.org/docs/syntax_and_semantics/literals/named_tuple.html)、[哈希](https://crystal-lang.org/docs/syntax_and_semantics/literals/hash.html)...

```
[1, "hello", 'x'] # Array(Int32 | String | Char)
{1, "hello", 'x'} # Tuple(Int32, String, Char)
{name: "Crystal", year: 2017} # NamedTuple(name: String, year: Int32)
{1 => 2, 'a' => 3} # Hash(Int32 | Char, Int32) 
```

Enter fullscreen mode Exit fullscreen mode

存在声明空类型结构的语法糖和快捷方式:

```
[] of Int32 # same as Array(Int32).new
Tuple(Int32, String, Char) # Tuple(Int32, String, Char)
NamedTuple(x: Int32, y: String) # NamedTuple(x: Int32, y: String)
{} of Int32 => Int32 # same as Hash(Int32, Int32).new 
```

Enter fullscreen mode Exit fullscreen mode

### 与通道和光纤并发

除了有趣的语法和静态类型系统，Crystal 还通过[纤程](https://crystal-lang.org/docs/guides/concurrency.html#fibers)带来了一些并发功能(灵感来自 [CSP](https://en.wikipedia.org/wiki/Communicating_sequential_processes) )。与其他编译语言相比，这是 Crystal 最有价值的功能之一

[并发](https://en.wikipedia.org/wiki/Concurrency_(computer_science))通过操作切换，在同一时间间隔内运行多个任务。这个概念经常与[并行](https://en.wikipedia.org/wiki/Parallel_computing)相混淆，后者由同时运行多个操作组成(数学上是并行的)。

水晶纤维可以比作 Golang 的 [Goroutines](https://gobyexample.com/goroutines) 。主要区别在于 Crystal 将操作分配给 CPU 的方式。当 Golang 使用所有可用的 CPU 时，Crystal 目前只能使用其中一个。

光纤使用与 Golang 相同的`message passing`系统来实现光纤之间的同步和数据传递。它使用一种叫做[通道](https://crystal-lang.org/docs/guides/concurrency.html#channels)的轻型管道。

下面是一个用 Goroutines 和纤程编写的异步操作的小例子。它会给你一个更具体的纤维用法的例子。

```
// GOLANG

messages := make(chan string)
defer close(messages)

go func() {
    time.Sleep(time.Second * 5)
    messages <- "ping"
}()

msg := <-messages // Program will wait for 5s
fmt.Println(msg) // => ping 
```

Enter fullscreen mode Exit fullscreen mode

```
# CRYSTAL

messages = Channel(String).new

spawn do
  sleep 5.seconds
  messages.send("ping")
end

msg = messages.receive # Program will wait for 5s
puts msg # => ping 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我使用了`channel`系统来提供主线程和例程(异步线程)之间的交换管道。

为了声明一个异步线程，在晶体端使用了带有关联的[代码块](https://crystal-lang.org/docs/syntax_and_semantics/blocks_and_procs.html)的`spawn`关键字。在 Golang 中，匿名函数通过前面的关键字`go`在 goroutine 中启动。

在分配时间(在本例中)，主线程停止，直到相应的`channel`发回数据。

### 空引用检查

在你的职业生涯中，你肯定听说过“NullPointerException”或[十亿美元的错误](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare)。这个众所周知的概念来自于这样一个事实:调用一个引用`Null`非常简单。

在 Crystal 中，这种异常是不可能发生的。由于静态类型，在编译时会发出一个检查来防止它。

```
if rand(42) > 0
  hello = "hello world"
end

puts hello.upcase 
```

Enter fullscreen mode Exit fullscreen mode

前面的代码将失败，并出现编译时异常。

```
$ crystal hello_world.cr
Error in hello_world.cr:5: undefined method 'e' for Nil (compile-time type is (String | Nil))

puts hello.upcase
          ^~~~~~ 
```

Enter fullscreen mode Exit fullscreen mode

发生这个异常是因为`hello`可以是`String`也可以是`Nil`，而`Nil`上没有`upcase`方法。

## 水晶工装

支持 Crystal 的另一个理由是它强大的标准库，它涵盖了大多数通常的需求。[很多工具](https://crystal-lang.org/api/0.23.1/)都是和 Crystal 本身捆绑在一起的(HTTP，JSON，Markdown Parser 等等)，让它可以即时使用。

比如这里有一个用 Crystal 写的小型 HTTP 服务器。

```
require "http/server"

server = HTTP::Server.new(8080) do |context|
  context.response.content_type = "text/plain"
  context.response.print "Hello world, got #{context.request.path}!"
end

puts "Listening on http://127.0.0.1:8080"
server.listen 
```

Enter fullscreen mode Exit fullscreen mode

## 水晶生态系统

尽管 Crystal 还很年轻，但由于其类似 Ruby 的语法，它很快就获得了广泛的欢迎。事实上，在日渐衰落的 rubyist 的世界里，它与另一种语言 [Elixir](https://elixir-lang.org/) 共享炒作。

目前大多数基于水晶的项目都是非常机密的。然而，其中一些很快受到欢迎:

*   KEMAL -快速、有效、简单的 web 框架(包括 websockets 等)
*   数据库包装器(灵感来自于 Elixir 的 ECTO)
*   [SIDEKIQ](https://github.com/mperham/sidekiq.cr) -简单高效的作业处理(包括网络监控用户界面)

Crystal bundles 具有易于使用的依赖管理系统。它由一个简单的 YAML 文件(名为 shard.yml)组成，列出了依赖项和相关的版本。看起来是这样的:

```
name: MyAmbitiousProject
version: 0.1.0

dependencies:
  openssl:
    github: datanoise/openssl.cr
    branch: master

development_dependencies:
  minitest:
    git: https://github.com/ysbaddaden/minitest.cr.git
    version: ~> 0.3.1

license: MIT 
```

Enter fullscreen mode Exit fullscreen mode

这样，一个简单的`crystal deps`命令就足以将所需的依赖项检索到项目树中。

如果你对发现其他有趣的水晶项目感到好奇，一个[专用门户](https://crystalshards.xyz/)已经为此目的而特别创建。

## 结论

Crystal 已于 2014 年首次发布。就目前而言，这是一种相对年轻的语言，但它有着光明的未来。

它的高级类型推理系统结合了静态类型和联合类型，给 Crystal 一种高级脚本语言的感觉，性能接近金属。对于一般的性能需求，它是 Golang 的一个很好的替代品。

此外，如果你已经熟悉 Ruby，Crystal 是首选语言。它提供了良好的性能，而不必忍受 Golang 的沉重语法。

最后，如果你喜欢这种语言，但需要[分布式操作](https://en.wikipedia.org/wiki/Distributed_computing)，你可以看看[的仙丹语言](https://elixir-lang.org/getting-started/introduction.html)，这也是 ruby 爱好者非常欣赏的。

如果你对与这篇文章相关的项目感兴趣，你应该看看[资源库](https://github.com/marmelab/laurel)。同时，这也会给你一个好的开始来整理水晶应用程序。**