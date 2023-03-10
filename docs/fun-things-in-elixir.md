# 仙丹中的趣事

> 原文：<https://dev.to/ryanflach/fun-things-in-elixir>

我最近一直在学习 [Elixir](http://elixir-lang.org/) ，一种利用 [Erlang](https://www.erlang.org/) VM 的函数式编程语言。我发现这种语言及其文档的许多方面既有趣又强大。这篇文章是对其中一些元素的简要探索。

* * *

## 1。函数 arity 的文档语法

如果你不熟悉 arity， [Wikipedia 提供了一个很好的定义](https://en.wikipedia.org/wiki/Arity)。简而言之，函数的 arity 是函数期望或接受的参数的数量。

功能通常以`name/arity`的形式列在 Elixir 文档中。例如，枚举模块中的[计数函数将被列为:](http://elixir-lang.org/docs/stable/elixir/Enum.html#count/1) 

```
Enum.count/1
Enum.count/2 
```

这表明有两个 count 函数:一个接受一个参数，另一个接受两个参数。

如果一个函数没有参数，例如映射模块中的[新函数，它将被列为:](http://elixir-lang.org/docs/stable/elixir/Map.html#new/0) 

```
Map.new/0 
```

## 2。管道操作员(| >)

Elixir 为函数链接提供了一个漂亮的简写方式。有点类似于 JavaScript 中的承诺，Elixir 中的管道操作符自动将表达式求值的值传递给下一个函数。如果右边的函数调用的 arity > 1，仍然可以提供这些参数，而第一个参数没有必要显式提供，因为它已经由管道操作符提供了。

下面是两个相同的操作——第一个没有管道操作符，第二个使用了管道操作符。

```
# Without |>
my_string = "hello there, how are you?"
split_string = String.split(my_string, ",")
=> ["hello there", " how are you?"]

# With |>
split_string = "hello there, how are you?" |> String.split(",")
=> ["hello there", " how are you?"] 
```

这个例子非常简单，只链接一次。即使在这个级别，它也为我们节省了一行代码，而没有牺牲可读性。当在几个层次上链接时，管道操作符真正开始发光了。请看这个来自[官方文件](http://elixir-lang.org/getting-started/enumerables-and-streams.html#the-pipe-operator) :
的例子

```
# Without |>
Enum.sum(Enum.filter(Enum.map(1..100_000, &(&1 * 3)), odd?))
=> 7500000000

# With |>
1..100_000
  |> Enum.map(&(&1 * 3)) 
  |> Enum.filter(odd?) 
  |> Enum.sum
=> 7500000000 
```

## 3。带子句的函数定义

Elixir 允许您用不同的子句创建相同的函数，利用其定义中的子句来确定调用哪个版本的函数。

```
defmodule SomeModule do

  def foo(arg \\ nil) when is_nil(arg) do
    IO.puts "No argument provided."
  end

  def foo(arg) do
    IO.puts "You provided the argument: #{arg}."
  end

end 
```

在上面的例子中，我已经定义了函数`foo/1`，并为`nil`的参数设置了默认值。如果调用该函数时没有参数，则打印一条消息，指出:

```
> SomeModule.foo
=> No argument provided. 
```

否则，arg 的值被插值并打印:

```
> SomeModule.foo(12)
=> You provided the argument: 12. 
```

## 4。代码内文档和测试

Elixir 允许开发人员在代码中创建公共函数的文档。

```
defmodule MainProgram.SomeModule do

  @doc """
  Takes an integer argument and returns the square of that value.
  """
  def squared(num) when is_integer(num) do
    num * num
  end

end 
```

当在[IEx](http://elixir-lang.org/docs/stable/iex/IEx.html)(Elixir 的交互式 shell)中运行这个模块时，我们可以像访问任何其他文档一样访问这个文档，可以使用`h`(帮助)命令:

```
iex> h MainProgram.SomeModule.squared

                        def squared(num)

Takes an integer argument and returns the square of that value. 
```

更好的是，单元测试可以在相同的文档中创建:

```
defmodule MainProgram.SomeModule do

  @doc """
  Takes an integer argument and returns the square of that value.

  ## Examples
      iex> MainProgram.SomeModule.squared(2)
      4
      iex> MainProgram.SomeModule.squared(-4)
      16
  """
  def squared(num) when is_integer(num) do
    num * num
  end

end 
```

当运行`mix test`时，在`## Examples`下列出的两个测试将与测试套件中的所有其他测试一起运行。为了确保这一点，文档中的测试需要以`iex>`开头，并缩进四个空格，在我们的`SomeModuleTest`文件中，我们需要列出`doctest` :

```
defmodule MainProgram.SomeModuleTest do
  use ExUnit.Case, async: true
  doctest MainProgram.SomeModule
end 
```

就是这样。我们现在已经有了将与`mix test`一起运行的测试，并在我们的文档中提供了有用的例子:

```
iex> h MainProgram.SomeModule.squared

                         def squared(num)

Takes an integer argument and returns the square of that value.

Examples
| iex> MainProgram.SomeModule.squared(2)
| 4
| iex> MainProgram.SomeModule.squared(-4)
| 16
iex> 
```

## 5。模式匹配

最后但同样重要的是模式匹配，这是灵丹妙药的一个极其强大的方面。这个主题可以是它自己的一篇博文(事实上，它已经作为一篇[优秀的博文](https://quickleft.com/blog/pattern-matching-elixir/)存在了)，所以这一部分将是一个非常简短的介绍。

在 Elixir 中，`=`操作符的正式名称是“匹配操作符”。这是因为它实际上是在进行左右两边的比较。变量赋值仍按预期工作:

```
iex> x = 1
=> 1 
```

但是翻转两边可以向我们展示比较是如何发生的

```
iex> x
=> 1
iex> 1 = x
=> 1
iex> 2 = x
=> ** (MatchError) no match of right hand side value: 1 
```

它实际上是比较左右两边来确认一个匹配。请注意，要进行变量赋值，变量必须位于匹配运算符的左侧。

模式匹配更常用于[元组](http://elixir-lang.org/getting-started/basic-types.html#tuples) :

```
iex> {a, b, c} = {1, :hi, "3"}
{1, :hi, "3"}
iex> a
1
iex> b
:hi
iex> c
"3" 
```

从表面上看，这与在 Ruby 中分配多个变量非常相似:

```
2.3.1 :001 > a, b, c = 1, :hi, "3"
 => [1, :hi, "3"]
2.3.1 :002 > a
 => 1
2.3.1 :003 > b
 => :hi
2.3.1 :004 > c
 => "3" 
```

但是请记住，匹配仍在进行，只允许我们在满足某些条件时分配变量:

```
iex> {:ok, result} = {:ok, 13}
{:ok, 13}
iex> result
13

iex> {:ok, result} = {:error, :oops}
** (MatchError) no match of right hand side value: {:error, :oops} 
```

如前所述，模式匹配值得深入研究。除了上面探讨的基本用例，它还可以用于数据结构和函数。如果你感兴趣，可以查看一下[官方网站](http://elixir-lang.org/getting-started/pattern-matching.html)(最终代码示例来自于此)或者前面提到的[博客](https://quickleft.com/blog/pattern-matching-elixir/)。