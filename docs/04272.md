# 用药剂构建 REST 服务器

> 原文：<https://dev.to/plutov/building-rest-server-with-elixir-4pd>

我一直想尝试 Elixir，因为它是一个很好的 Erlang 生态系统，而且它在很多方面都与 Go 相似。我在想我可以从什么开始，我决定建造，是的...一个 REST 服务器。我花了大约 1 个小时来安装 Elixir，并使用 RESTful 框架构建了一个简单的 REST 服务器。

我使用 JSON 和[代理](http://elixir-lang.org/getting-started/mix-otp/agent.html)作为存储构建了一个小的`items` API。让我通过所有的行动来建立。

#### 先决条件

如果你使用 brew: `brew install elixir`，你应该只安装[药剂](https://elixir-lang.org/install.html)。

#### 创建新的混音项目

Mix 是和 Elixir 一起安装的，所以你只需要运行一个命令来创建一个项目，Mix 会为你创建所有的初始文件:config，deps，lib，tests，README。

```
mix new elixirrest 
```

Enter fullscreen mode Exit fullscreen mode

#### 定义依赖关系

由于我们决定使用`maru`包，我们必须将它添加到`mix.exs`文件中。

```
def application do
    [applications: [:logger, :maru], mod: {Elixirrest, []}]
end

defp deps do
    [{:maru, "~> 0.2.8"}]
end 
```

Enter fullscreen mode Exit fullscreen mode

要获取并编译依赖项，您必须在`elixirrest`文件夹中运行该命令:

```
mix do deps.get, compile 
```

Enter fullscreen mode Exit fullscreen mode

#### 配置

Mix 创建了一个文件`config/config.exs`，我们可以在其中放置我们需要的所有配置，对于不同的环境，您可以创建一个单独的文件，但是在我们的示例中，我们只有一个端口为`3030` :
的文件

```
use Mix.Config

config :maru, Elixirrest.Api,
    http: [port: 3030] 
```

Enter fullscreen mode Exit fullscreen mode

#### API

我创建了一个文件夹`lib/elixirrest`，里面有两个文件:`api.ex`、`agent.ex`。首先，我们将定义我们的 API，其次将用于作为存储工作者。

API 只包含 2 个端点:GET 列出所有项目，POST 创建新项目，它们使用代理作为存储。

```
defmodule Elixirrest.Api do
    use Maru.Router
    alias Elixirrest.Agent, as: Store

    namespace :items do
        desc "get all items"
        get do
            Store.get |> json
        end

        desc "creates an item"
        params do
            requires :name, type: String
        end
        post do
            Store.insert(params) |> json
        end
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 主管

要启动代理，我们只需将其添加到`lib/elixirrest.ex` :
中的监督树中

```
defmodule Elixirrest do
    use Maru.Router

    def start(_type, _args) do
        import Supervisor.Spec, warn: false

        children = [
            worker(Elixirrest.Agent, [])
        ]

        opts = [strategy: :one_for_one, name: Elixirrest.Supervisor]
        Supervisor.start_link(children, opts)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 运行和测试

在`elixirrest`文件夹中运行该命令:

```
iex -S mix 
```

Enter fullscreen mode Exit fullscreen mode

如果您没有看到任何错误，您可以测试 API 端点:

```
curl -XPOST -d "name=test" http://localhost:3030/items
curl http://localhost:3030/items 
```

Enter fullscreen mode Exit fullscreen mode

我把项目推到了这个库的[中，以防你想要检查或贡献。](https://github.com/plutov/elixirrest)

[我博客中的原帖](http://pliutau.com/building-rest-server-with-elixir/)