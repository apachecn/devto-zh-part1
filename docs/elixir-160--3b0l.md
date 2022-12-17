# 介绍 Elixir 1.6.0 的新功能

> 原文：<https://dev.to/kdxu/elixir-160--3b0l>

# 关于 Elixir v1.6.0

虽然还没有正式发布，但是在[https://github.com/elixir-lang/elixir](https://github.com/elixir-lang/elixir)中面向 v1.6.0 的功能现在正在进行中开发。 现行的最新版本是 v1.5.2。

master 分支的文档在[这里](https://hexdocs.pm/elixir/master/)持续更新。
此次将进入 v1.6.0 的新功能中的两个

*   混合格式
*   动态主管

我来介绍一下。

## 混合格式(代码格式化程序)

[文档](https://hexdocs.pm/mix/master/Mix.Tasks.Format.html)

[相应的源代码](https://github.com/elixir-lang/elixir/blob/master/lib/mix/lib/mix/tasks/format.ex)

写 Elixir 最困扰的事情之一是没有官方的代码格式。

指出缩进偏移和无参数函数的`()`忘记，本来不是人做的事。 机器能做的事情最好由机器来做。

第三方的 code formatter 有[ex_format](https://github.com/uohzxela/ex_format) 和[exfmt](https://hex.pm/packages/exfmt) 等，但存在着对样式指南不严格或注释消失等问题

Elixir-1.6.0 dev 中为[Code.format_string！ / 2 通过](https://hexdocs.pm/elixir/master/Code.html#format_string!/2)函数可以对整个项目进行格式设置。
作为处理，将 string 作为 input 通过`Code.Formatter.algebra!`分解为 AST，根据现有的格式信息通过`Inspect.Algebra.format/2`重新组装为 string。[(参照源代码)【](https://github.com/elixir-lang/elixir/blob/master/lib/elixir/lib/code/formatter.ex#L196)

### 使用方法

```
$ mix format /path/to/file.ex 
```

Enter fullscreen mode Exit fullscreen mode

中设置指定文件的格式。

另外，可以在`.formatter.exs`中写入设定文件，定制项目整体的格式、规章。

```
[
  inputs: [
    "lib/*/{lib,unicode,test}/**/*.{ex,exs}",
    "lib/*/mix.exs"
  ],

  locals_without_parens: [
    # Formatter tests
    assert_format: 2,
    assert_format: 3,
    assert_same: 1,
    assert_same: 2,

    # Errors tests
    assert_eval_raise: 3,

    # Mix tests
    in_fixture: 2,
    in_tmp: 2
  ]
] 
```

Enter fullscreen mode Exit fullscreen mode

接下来是

```
$ mix format 
```

Enter fullscreen mode Exit fullscreen mode

进行
后，由`inputs:`指定的代码将被格式化。

使用现有的 aliases 功能

```
defp aliases() do
    {xxxx: ["format", "release"]}
end 
```

Enter fullscreen mode Exit fullscreen mode

等等，构建时等任意任务一起咬 format 也可以吧。

另外，打开`--check-formatted`选项的话，可以检查不正确格式的文件的存在。 (如果有非法文件，就会吐出 exit 0)这在 CI 上的运用似乎很有用。

## 动态主管

[文档](https://hexdocs.pm/elixir/master/DynamicSupervisor.html)

[源代码](https://github.com/elixirlang/elixir/blob/master/lib/elixir/lib/dynamic_supervisor.ex)

[符合条件的提取请求](https://github.com/elixir-lang/elixir/pull/7006)

Dynamic Supervisor 是 supervisor 的一种新行为，允许动态添加子进程。
是代替`:simple_one_for_one`的东西。 将现有的`:simple_one_for_one`具有重启战略的项目升级到 Elixir 1.6.0 时，需要更换为动态支持器。
可以在不指定 children 的情况下启动，然后在任意时间进行`start_child/2`。

动态 supervisor 在没有 children 链接的情况下启动 link。 现在支持的战略好像只有`:one_for_one`。

我用用看。 启动动态服务程序。

```
iex> {:ok, sup} = DynamicSupervisor.start_link(strategy: :one_for_one)
{:ok, #PID<0.85.0>} 
```

Enter fullscreen mode Exit fullscreen mode

动态添加子项。

```
iex> {:ok, agent1} = DynamicSupervisor.start_child(sup, {Agent, fn -> %{} end})
{:ok, #PID<0.87.0>} 
```

Enter fullscreen mode Exit fullscreen mode

再加一个。

```
iex> {:ok, agent2} = DynamicSupervisor.start_child(sup, {Agent, fn -> %{} end})
{:ok, #PID<0.89.0>} 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> DynamicSupervisor.count_children(sup)
%{active: 2, specs: 2, supervisors: 0, workers: 2} 
```

Enter fullscreen mode Exit fullscreen mode

和使用`:simple_one_for_one`时触感没有变化。 但是，我也很在意如果追加了`:one_for_one`以外的 strategy 会有什么样的举动。
只是在模块库中明确表示了`Dynamic`，所以感觉各个 supervisor 的 strategy 变得容易掌握，容易处理。

## 总结

Elixir 是一种目前开发非常活跃的语言。 (其基础的 Erlang/OTP 也是)

一定要追踪 GitHub，看看 Elixir Conf 的资料，追踪最新的动向，充分利用其出色的功能吧。