# 一个 Rubyist 看水晶(第二部分)

> 原文：<https://dev.to/citizen428/a-rubyist-looks-at-crystal-part-2>

正如我在[上一篇文章](https://dev.to/citizen/a-rubyist-looks-at-crystal-part-1)中承诺的，这篇文章将涵盖 Crystal 的一些更高级的特性，即宏、C 绑定和并发性。

#### 宏

从 Ruby 到 Crystal，最大的变化之一是缺少运行时自省，而运行时自省支持 Ruby 的许多元编程技术。然而，这可以通过使用[宏](https://crystal-lang.org/docs/syntax_and_semantics/macros.html)在一定程度上得到纠正，这些宏是在编译时接收 [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) 节点的方法，它们用这些节点来编写新代码。

这相当冗长，所以让我们看一个例子:在 Crystal 中，像`private`这样的访问修饰符需要成为方法定义的一部分。让我们通过引入宏`defp`(我借用了 [Elixir](http://elixirlang/) 的名字)来简化这一点，它为定义私有方法提供了更短的语法。

```
macro defp(name, &block)
  private def {{name.id}}
    {{block.body}}
  end  
end 
```

Enter fullscreen mode Exit fullscreen mode

这个宏接收一个名称和一个块，它使用这个名称和块的内容来定义一个方法，这个方法具有适当的名称和块的内容作为方法体。注意，插值中的 [id](https://crystal-lang.org/api/0.19.4/Crystal/Macros/ASTNode.html#id:MacroId%20instance-method) 调用确保我们可以将`name`作为一个符号、一个字符串或一个空字传入。让我们看看它的作用:

```
class Test
  def public
    priv
  end

  defp priv do
    "private method"
  end
end

t = Test.new
t.priv
#=> private method 'priv' called for Test
t.public #=> "private method" : String 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用我们的宏来定义一个名为`priv`的私有方法，然后我们试图调用它。不出所料，这将无法编译，我们会看到常见的错误消息。通过公共方法调用我们的私有方法当然会成功，所以通过宏定义的方法的行为与以常规方式定义的方法完全一样。这只是一个例子，说明通过宏来扩展 Crystal 的语法是多么容易。

不过，宏提供了更多，比如条件、迭代或 splat 参数。让我们在下面的例子中看看其中的一些:

```
macro define_abstract(klass, *names)
  abstract class {{klass.id}}
    {% for name, _index in names %}
      abstract def {{name.id}}
    {% end %}
  end
end

define_abstract Abstract, :one, :two

class Concrete < Abstract
  def one
    1
  end
end
# abstract `def Abstract#two()` must be implemented by Concrete 
```

Enter fullscreen mode Exit fullscreen mode

`define_abstract`是一个宏，它接收一个类名(常量、字符串或符号)和一个抽象方法名的可变列表，然后使用这些信息生成一个抽象类。出于测试的目的，我们从新定义的类继承，但是因为我们忘记实现第二个方法，我们将得到一个编译器错误，提醒我们仍然必须在我们的`Concrete`类中为`Abstract#two`提供实现。

#### 宏钩子

Crystal 提供了一些特殊的宏，它们在编译时充当钩子:`inherited`(定义了一个子类)、`included`(包含了一个模块)、`extended`(扩展了一个模块)和`method_missing`(调用了一个不存在的方法)。我们来看看后者，ruby 家应该很熟悉:

```
class Greeter
  def greet(name)
    "Hello #{name}!"
  end

  macro method_missing(call)
    greet({{call.name.id.stringify.capitalize}})
  end
end

g = Greeter.new
g.readers #=> "Hello Readers!" : String 
```

Enter fullscreen mode Exit fullscreen mode

这里，`method_missing`宏确保每当我们在`Greeter`的实例上调用未定义的方法时，我们将转换方法名，并将其作为参数传递给`greet`方法。多么友好的班级啊！ðŸ —

#### C 扩展

Crystal 的另一个优点是它让与 C 库的接口变得非常容易。我们先来看一个例子:

```
lib LibMath   
  fun nearbyint(x: Float64): Float64
  fun pow(x: Float64, y: Float64): Float64
end

LibMath.nearbyint(3.534) #=> 4.0: Float64
LibMath.pow(2, 10) #=> 1024.0: Float64 
```

Enter fullscreen mode Exit fullscreen mode

我们用`lib`声明一个 C 库的包装器，它允许我们声明我们感兴趣的函数和类型。这个例子包装了 macOS 的`math`库中的两个函数(详见`man 3 math`)，这两个函数是隐式链接的，所以我们不需要给编译器提供任何链接信息。

然后我们使用`fun`来指定我们感兴趣的函数，以及它们的参数和返回类型(根据晶体类型)。瞧，在我们定义了`nearbyint`和`pow`的绑定后，我们可以像调用`LibMath`上的类方法一样调用它们。这真是再简单不过了！

现在让我们看看如何使用一个没有自动链接的库。

```
@[Link("ncurses")]
lib LibNcurses
  fun initscr
  fun beep: Int32
end

LibNcurses.initscr
LibNcurses.beep 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用属性`Link`来指定我们想要链接到`ncurses`(参见`man 3 ncurses`)，它将把`-lncurses`传递给链接器。如果需要的话，我们还可以指定`ldflags`和`framework`(后者只在 macOS 上)，但是对于我们的简单例子，我们不需要这样做。然后我们继续为两个函数`initscr`和`beep`定义包装器，它们将使用终端的铃声来提醒用户。当然，如果在 Crystal 内部执行一个`puts "\a"`操作，事情会变得更简单，但是这是一个很好的例子，说明了这种语言是多么容易与任何 C 库接口。

### 并发

最后但同样重要的是，我们将看看 Crystal 中的并发性。虽然 Crystal 程序通常在单个操作系统线程中运行(除了垃圾收集器)，但它通过纤程实现并发，纤程是由主程序而不是操作系统管理的轻量级进程(这被称为[协作多任务](https://en.wikipedia.org/wiki/Cooperative_multitasking)，它允许低开销的上下文切换)。

纤程从一个只有 4k 的小堆栈开始，可以增长到 8MB，这是一个线程的典型大小。这意味着在现代的 64 位机器上，我们可以产生数百万条纤程而不会耗尽内存。如果你以前有过使用 Go 的 [goroutines](https://tour.golang.org/concurrency/1) 或者 Erlang 的[进程](http://erlang.org/doc/reference_manual/processes.html)的经验，这应该会感觉很熟悉。

理论够了，我们来看一个例子:

```
require "http/client"

chan = Channel(Hash(String, Int32)).new

sites = %w(
    https://crystal-lang.org
    https://twitter.com/crystallanguage
    https://salt.bountysource.com/teams/crystal-lang
)

sites.each do |site|
  spawn do
    response = HTTP::Client.head(site)
    chan.send({ site => response.status_code })
  end
end

(1..sites.size).map { chan.receive }
#=> [{"https://twitter.com/crystallanguage" => 200}, 
#    {"https://crystal-lang.org" => 200}, 
#    {"https://salt.bountysource.com/teams/crystal-lang" => 200}] 
```

Enter fullscreen mode Exit fullscreen mode

在请求了 [HTTP 客户端库](https://crystal-lang.org/api/0.19.4/HTTP/Client.html)之后，我们定义了一个[通道](https://crystal-lang.org/api/0.19.4/Channel.html)，它将用于我们的纤程和主执行线程之间的通信。该通道期望消息是带有字符串键和整数值的散列。

然后我们迭代我们的`sites`数组，它包含各种与 Crystal 相关的网站的 URL。对于每个 URL，我们生成一个新的执行线程(一个纤程)，它将向网站发出 HEAD 请求，然后在通道上放置一条适当的消息(站点的 URL 作为字符串键，请求的响应代码作为整数值)。

然后，我们接收所有放在通道上的消息。正如我们从所提供的输出中看到的(每次执行该程序时，输出可能会有所不同)，结果与`sites`数组的顺序不同，因为纤程是并行执行的，并且在不同的时间完成。

#### 结论

这第二篇文章结束了我对 ruby 爱好者的简短介绍。这一次我重点展示了 Crystal 的一些更强大的特性，比如它的宏系统、与 C 库集成的直接方法以及受 Go 启发的并发方法。敬请关注未来更多水晶相关的帖子！