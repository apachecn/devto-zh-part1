# 仙丹模式匹配简而言之

> 原文：<https://dev.to/jackmarchant/elixir-pattern-matching-in-a-nutshell-3cmo>

[![](img/c091ded74e4d03953facc475c0f33b7f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xeea2FkJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/549/1%2AdFrM0yAq52YuJ4FzfHG_tA.jpeg)

在被介绍给基于 [Erlang](https://www.erlang.org/) 的函数式编程语言 [Elixir](http://elixir-lang.org) 之前，我不知道什么是模式匹配。希望在这篇文章结束时，你至少会对它有一个基本的了解。

在大多数编程语言中，你会用类似于
的东西给一个变量赋值

```
const myVariable = 'my value';
console.log(myVariable); // 'my value' 
```

现在，我的变量被绑定到你赋予它的值，你可以继续你的生活。

当你需要检查一个变量的值时，在大多数其他语言中，你会使用条件“if 语句”，一旦你添加了超过 2 或 3 个，它就会变得不可读。这是因为很难看到逻辑流程，尤其是当函数跨越许多行时。

从技术上讲，你可以在 Elixir 中做同样的事情，但是编译器对它的解释有很大的不同。=符号实际上被称为“匹配”运算符。它将使用左边的值，并将其与右边的值进行比较，以确定它们是否匹配。

在 Elixir 代码中经常使用元组，以便能够从一个函数返回多个值。通常，您会遇到{状态，值}元组，例如:

```
{:ok, return\_value} = do\_stuff() 
```

do_stuff()必须返回一个与该结构匹配的元组(否则 Elixir 将引发一个‘match error’)，return_value 现在被绑定到从该函数返回的元组中的第二项。

这就是模式匹配的基本工作原理，但真正的美妙之处在于您如何在各种环境中使用它，例如:

*   当一个函数可以返回多个值时，比如我们前面遇到的{status，value}元组:

```
case do\_stuff() do
 {:ok, value} -> value
 {:error, \_} -> raise "Oh no!"
end 
```

*   在函数头中，您可以对参数进行模式匹配，以便仅在满足特定要求时运行:

```
def my\_func({:ok, value}), do: value
def my\_func({:error, \_}), do: raise "Oops!"

IO.puts my\_func({:ok, "hello"}) # "hello" 
```

*   您甚至可以匹配列表:

```
[first, second, third] = [1, 2, 3] 
```

*   和分解数据结构

```
%{value: value} = map\_func() 
```

Elixir 中有很多模式匹配的例子，因为它非常有用和强大，与传统方法相比也非常高效。

一言以蔽之，那就是模式匹配！

* * *