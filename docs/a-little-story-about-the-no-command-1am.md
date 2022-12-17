# 一个关于“不”命令的小故事

> 原文：<https://dev.to/tokoyax/a-little-story-about-the-no-command-1am>

受这篇文章的启发，我做了类似于灵药练习的命令。

[http://postd.cc/a-little-story-about-the-yes-unix-command/](http://postd.cc/a-little-story-about-the-yes-unix-command/)

## 这是否定世界一切的‘不’命令！！！

规格几乎和`yes`一样。继续输出`n`到标准输出直到永远。

它使得像这样使用它成为可能。

```
> no
n
n
n
n
n
n
n
... 
```

Enter fullscreen mode Exit fullscreen mode

或者，

```
> no | sh work_a_lot.sh 
```

Enter fullscreen mode Exit fullscreen mode

你讨厌的老板说:“再加把劲！！更加努力！！!"

我的天啊...这是通往‘过劳死’的道路。

这个命令在这种时候应该是有用的:D

# 我们开始吧

### 环境

```
> elixir -v
[Async - threads: 10] [hipe] [kernel - poll: kernel - poll: kernel - poll: false] [dtrace]

Elixir 1.5.2 
```

Enter fullscreen mode Exit fullscreen mode

### 项目创建

```
> mix new no
> cd no 
```

Enter fullscreen mode Exit fullscreen mode

### 编辑`mix.exs`

要用 Elixir 创建可执行文件，可以使用`escript`。

[http://erlang.org/doc/man/escript.html](http://erlang.org/doc/man/escript.html)

*   `line: 9`指定要使用的模块`escript`
*   最好写一个返回模块名等的函数。

`mix.exs`

```
defmodule No.Mixfile do
  use Mix.Project

  def project do
    [
      app: :no,
      version: "0.1.0",
      elixir: "~> 1.5",
      escript: escript_config(),
      start_permanent: Mix.env == :prod,
      deps: deps()
    ]
  end

  # Run "mix help compile.app" to learn about applications.
  def application do
    [
      extra_applications: [:logger]
    ]
  end

  defp escript_config do
    [main_module: No]
  end

  # Run "mix help deps" to learn about dependencies.
  defp deps do
    [
      # {:dep_from_hexpm, "~> 0.3.0"},
      # {:dep_from_git, git: "https://github.com/elixir-lang/my_dep.git", tag: "0.1.0"},
    ]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这里指定的模块的`No.main/1`成为入口点。

## 实现

它只是正常循环并不奇怪。我添加了一个选项`-p`，它会生成许多进程，并为每个进程持续输出`n`。此外，我还可以看到带有`-d`的 PID，这样我就可以观察并行执行。

`OptionParser`顺手有用！

`no.exs`

```
defmodule No do
  @moduledoc """
  Documentation for No.
  """

  def main(args) do
    args
    |> parse_args()
    |> run()
    |> wait()
  end

  def say(args) do
    {argv, process_num, sender, debug} = args
    case debug do
      true -> send sender, {:debug, "#{expletive(argv)} from process no.#{process_num}", self()}
      _    -> send sender, {:ok, expletive(argv)}
    end
    say(args)
  end

  defp expletive(_ = ''), do: 'n'
  defp expletive(value), do: value

  defp parse_args(args) do
    {opts, argv, _} = OptionParser.parse(
      args,
      swithces: options_list(),
      aliases: aliases_list()
    )
    {argv, opts}
  end

  defp options_list() do
    [
      processes: :integer,
      debug: :boolean,
    ]
  end

  defp aliases_list() do
    [
      p: :processes,
      d: :debug,
    ]
  end

  defp run(args) do
    {argv, opts} = args
    num = case opts[:processes] do
      nil -> 1
      _  -> String.to_integer(opts[:processes])
    end
    (1..num)
    |> Enum.each(
      fn(n) ->
        spawn_link(No, :say, [{argv, n, self(), opts[:debug]}])
      end
    )
  end

  defp wait(args) do
    receive do
      {:ok, message} ->
        IO.puts message
      {:debug, message, pid} ->
        IO.inspect {message, pid}
    end
    wait(args)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 打造

当您完成构建时。
运行在有`mix.exs`的目录下。

```
> mix escript.build 
```

Enter fullscreen mode Exit fullscreen mode

可执行文件应该在当前目录下生成。

## 执行

在当前目录中。

```
> ./no -p 100
n
n
n
n
n
... 
```

Enter fullscreen mode Exit fullscreen mode

调试选项可用。

```
>.. / no - p 100 - d
{"n from process no. 47", # PID <0.122.0>}
{"n from process no. 47", # PID <0.122.0>}
{"n from process no. 47", # PID <0.122.0>}
{"n from process no. 40", # PID <0.115.0>}
{"n from process no. 47", # PID <0.122.0>}
... 
```

Enter fullscreen mode Exit fullscreen mode

抓住你了。！！！

# 有多快？

```
> ./no | pv -r > /dev/null
[93.7 KiB / s] 
```

Enter fullscreen mode Exit fullscreen mode

至于`yes`命令，

```
> yes | pv -r > /dev/null
[31.6 MiB / s] 
```

Enter fullscreen mode Exit fullscreen mode

命令太慢了！！！！xD
冷静点，相信会在并行处理中反转！！

```
>./no - p 100 | pv -r > /dev/null
[1.12 KiB / s] 
```

Enter fullscreen mode Exit fullscreen mode

...为什么？

## 总结

*   将重新实现 Linux 命令作为一门语言实践课程是很好的
*   许多函数的大小都是合理的
*   用* `|>`进行处理是令人愉快的
*   不知道为什么性能慢。
*   我稍后会调查。
*   十六进制注册
*   注册->[http://blog.tokoyax.com/entry/elixir/register-to-hex](http://blog.tokoyax.com/entry/elixir/register-to-hex)

Github 回购:[https://github.com/tokoyax/no](https://github.com/tokoyax/no)

这篇文章是从日文翻译过来的。

[http://blog.tokoyax.com/entry/elixir/no-command](http://blog.tokoyax.com/entry/elixir/no-command)

(参考)

*   [https://github.com/rizafahmi/elixirdose-cli](https://github.com/rizafahmi/elixirdose-cli)
*   [https://dev.to/roperzh/overview-of-elixir-executables-4bk](https://dev.to/roperzh/overview-of-elixir-executables-4bk)
*   [https://qiita.com/niku/items/729ece76d78057b58271](https://qiita.com/niku/items/729ece76d78057b58271)
*   [https://qiita.com/niku/items/5b7967d88cae58e034e2](https://qiita.com/niku/items/5b7967d88cae58e034e2)