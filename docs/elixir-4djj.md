# 在 Elixir 的代码审查中出现的家伙

> 原文：<https://dev.to/seizans/elixir-4djj>

【2016 年灵药降临日历 の12 日目です。

关于在 Elixir 上开发时在代码审查的指摘点中出现的内容，
列举了几个我认为很常见的内容。

## 假设返回值时，用 _ 不匹配命名

提前命名的人读的时候就知道了。

## 如果能用函数定义匹配的话，请在那边

```
def spam(x) do
  case x do
    :aaa ->
      :ok
    :bbb ->
      :error
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

像这样做的话，就像下面那样做。

```
def spam(:aaa) do
  :ok
end
def spam(:bbb) do
  :error
end 
```

Enter fullscreen mode Exit fullscreen mode

## 通过 if 使用 case

分场合使用 if 的情况不多。
使用 case 比较好的情况很多。

```
user = Repo.get(query, id)
if user != nil do
  ...
else
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

要写的话就如下。

```
case Repo.get(query, id) do
  nil ->
    ...
  user ->
    ...
end 
```

Enter fullscreen mode Exit fullscreen mode

## 错误返回值形式不同的不通过 with 汇总，而是通过 case 嵌套

```
with {:ok, foo} <- spam(),
     {:ok, bar} <- ham() do
  ...
else
  :error ->
    ...
  {:error, reason} ->
    ...
end 
```

Enter fullscreen mode Exit fullscreen mode

像这样 spam ( )返回: error，ham ( )返回{:error，reason}的情况下，用
with 总结的话就不知道哪个返回哪个错误形式了，很痛苦。
所以一般用 case 嵌套书写。

```
case spam() do
  {:ok, foo} ->
    case ham() do
      {:ok, bar} ->
        ...
      {:error, reason} ->
        ...
    end
  :error ->
    ...
end 
```

Enter fullscreen mode Exit fullscreen mode

## 函数调用必须不带括号()

因为有统一的好处。

## 不重新代入

如果要重新代入的话，基本上要取别的名字。
Phoenix 的 conn 这样的小部分例外除外。
现在还没有感觉到重新代入的必要性。

## %URI{}使用吧

```
url_base = "https://#{host}"
url = "#{url_base}/user/#{user_id}" 
```

Enter fullscreen mode Exit fullscreen mode

想做什么的话就用 URI。 (可以用 HTTPoison 等 URI 型传递)

```
url = %URI{scheme: "https",
           host: host,
           path: Path.join("/user", user_id)} 
```

Enter fullscreen mode Exit fullscreen mode

如果想做成 String 的话，使用 URI.to_string ( )就可以了。

## 如果满足条件则追加到列表中

制作 List 和 Map 时，如果满足条件的话会追加。
比如说这样的。

```
@spec build_spams() :: list()
def build_spams() do
  spams = base_spams()
  spams = if condition1() do
    [additional1() | spams]
  else
    spams
  end
  spams = if condition2() do
    [additional2() | spams]
  else
    spams
  end
  spams
end 
```

Enter fullscreen mode Exit fullscreen mode

将追加要素的部分提取为函数的话，有时会更有前景。
如下所示。

```
@spec build_spams() :: list()
def build_spams() do
  base_spams()
  |> maybe_add_spams1()
  |> maybe_add_spams2()
end

defp maybe_add_spams1() do
  if condition1() do
    [additional1() | spams]
  else
    spams
  end
end

defp maybe_add_spams2() do
  if condition2() do
    [additional2() | spams]
  else
    spams
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 再送: credo

在 CI 上，mix credo 一定会回答。
. credo.exs 是按照以下感觉做的。

```
%{
  configs: [
    %{
      name: "default",
      files: %{
        included: ["lib/"],
        excluded: []
      },
      checks: [
        {Credo.Check.Readability.MaxLineLength, max_length: 120},
        {Credo.Check.Readability.ModuleDoc, false},
        {Credo.Check.Refactor.Nesting, false},
        {Credo.Check.Refactor.PipeChainStart, false},
      ]
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode