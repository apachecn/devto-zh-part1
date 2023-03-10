# 我如何提高药剂的性能

> 原文：<https://dev.to/tokoyax/how-do-i-improve-performance-in-elixir-5g3>

[https://dev . to/tokoyax/a-little-story-about-the-no-command-1am](https://dev.to/tokoyax/a-little-story-about-the-no-command-1am)

继续上述条款

## 执行速度问题

命令`no`比这样的`yes`命令慢。

```
> yes | pv -r > /dev/null
[31.0 MiB/s] 
```

Enter fullscreen mode Exit fullscreen mode

```
>./no | pv -r > /dev/null
[85.6 KiB/s] 
```

Enter fullscreen mode Exit fullscreen mode

我得到了下面的评论。

> 你最好用缓冲器来写。实际上，yes 命令并不为每一行调用 write (2)。

好像是大量 I/O 造成的，用缓冲区比较好。

努力提高。

### 使用 IO 列表

[https://www . bignerdranch . com/blog/elixir-and-io-lists-part-1-building-output-efficient](https://www.bignerdranch.com/blog/elixir-and-io-lists-part-1-building-output-efficiently)

根据上面的文章，在向`IO.puts`传递字符串时，不是只传递一个组合字符串，而是将字符串的部分存储到列表中，性能更好。

如果相同的字符串存储在列表中，这是有效的，因为相同的字符串引用相同的地址。

我修正了“no.exs”使用缓冲区的问题。

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
    {_, _, _, debug} = args
    message = buffer(args)
    case debug do
      true -> IO.inspect({:debug, message, self()})
      _    -> IO.puts(message)
    end
    say(args)
  end

  defp buffer(args) do
    {argv, process_num, _, debug} = args

    message = case debug do
      true -> "#{expletive(argv)} from process no.#{process_num}"
      _    -> expletive(argv)
    end

    buffer(args, message, [])
  end

  defp buffer(args, message, list) do
    # faster than append last
    # https://hexdocs.pm/elixir/List.html
    list = [message | list]
    cond do
      IO.iodata_length(list) >= 4096 -> list
      true -> buffer(args, message, list)
    end
  end

  defp expletive(_ = ''), do: "n\n"
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
      _   -> String.to_integer(opts[:processes])
    end

    Enum.each((1..num), fn(n) ->
      spawn_link(No, :say, [{argv, n, self(), opts[:debug]}]) end)
  end

  defp wait(args) do
    wait(args)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 测量

在
之前

```
> ./no | pv -r > /dev/null
[85.6 KiB/s] 
```

Enter fullscreen mode Exit fullscreen mode

在
之后

```
> ./no | pv -r > /dev/null
[802KiB /s] 
```

Enter fullscreen mode Exit fullscreen mode

成功:D

尝试并行执行

```
> ./ no - p 4 | pv - r > /dev/null
[1.33 MiB / s] 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我们多平行跑。

```
> ./ no - p 10000 | pv - r > /dev/null
[5.00 MiB /s]
[3.16 MiB /s]
[1.84 MiB /s]
[458 KiB /s]
[466 KiB /s]
[569 KiB /s]
[724 KiB /s]
[805 KiB /s]
[1.27 MiB /s]
[6.17 MiB /s]
[15.3 KiB /s]
[1.13 MiB /s] 
```

Enter fullscreen mode Exit fullscreen mode

虽然得到了加速的结果，但输出结果并不恒定。

我不知道为什么。

### 总结

*   我得到了 IO 列表等速度提升的知识。
*   制造得更快只是一种乐趣。
*   `yes`命令仍然更快。

github:[[https://github.com/tokoyax/no:title](https://github.com/tokoyax/no:title)

(参考)

*   [https://www . bignerdranch . com/blog/elixir-and-io-lists-part-1-building-output-efficient](https://www.bignerdranch.com/blog/elixir-and-io-lists-part-1-building-output-efficiently)
*   [https://hexdocs.pm/elixir/List.html](https://hexdocs.pm/elixir/List.html)