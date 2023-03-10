# 为什么你应该尝试琥珀框架

> 原文：<https://dev.to/eliasjpr/why-you-should-try-amber-franework-100n>

# 琥珀水晶网框架

作为一名 web 开发人员，我们都有拥有一个完美的 web 框架的想法，一个工作有趣，不会妨碍您，尽早发现错误，易于扩展，部署在任何地方，适应不同的架构模式，而不会牺牲性能和难以学习的框架。

安布尔·amberframework.org([http://amberframework.org/](http://amberframework.org/))致力于通过利用水晶语言的特性和融合一系列被广泛接受的特性来解决这些痛点。

> Amber 是一个用 Crystal 写的 web 应用框架。Amber 以极快的性能使构建 web 应用程序变得快速、简单和令人愉快。

### 为什么是琥珀框架？

Amber 是一个用 Crystal 写的 web 应用框架。Amber 以极快的性能使构建 web 应用程序变得快速、简单和令人愉快。已经被开发人员广泛接受的聚合概念和特性。

以下是您应该考虑在下一个项目中使用 Amber 的原因:

#### 水晶语

作为一种编程语言，Crystal 提供了具有低级语言性能和特征的高级语法。

*   **语法**——用更少的代码行写一个表现力很强的程序。易于阅读，没有可怕的性能。
*   类型系统 -静态类型检查，因此任何类型错误都会被编译器及早发现，而不是在运行时失败。更少的错误。
*   空引用检查(Null Reference Checks)-在 Crystal 中，所有类型都是不可 nilable 的，nilable 变量被表示为类型和 nil 之间的并集。同样更少的错误。
*   **宏** - Crystal 对元编程的回答是一个强大的宏系统，范围从基本的模板和 AST 检查。
*   **并发模型**——Crystal 使用绿色线程，称为纤程，来实现并发。纤程使用通道相互通信，如在 Go 或 Clojure 中，而不必求助于共享内存或锁。

**熟悉度**

琥珀很好学，很好拿。Amber 具有友好且易于阅读的 Ruby 式语法，与 Ruby、Express、Elixir、Laravel 等其他语言中常见的其他流行 web 应用程序框架共享相同的概念，易于理解和熟悉使用。

**性能**

Amber 面向希望获得更高性能的程序员，他们喜欢在高级脚本中工作，结合了本机执行速度和并发性，因此您会有宾至如归的感觉，而不会因为代码复杂性而失去编程的乐趣。

```
02:28:43 Request | Started 2018-01-07 14:28:43 - 05:00
02:28:43 Request | Status: 200  Method: GET Pipeline: web Format: html
02:28:43 Request | Requested Url: /
02:28:43 Request | Time Elapsed: 94.0µs 
```

**特征的融合**

Amber 开发人员并没有止步于 Crystal 特性，Amber 融合了广泛接受的一系列 web 框架的特性，与当今广泛使用的其他 web 应用程序框架有很多相似之处，特别是 Ruby On Rails。

**控制器**

虽然这根本不是一个新概念，但琥珀并没有通过这个概念来重新发明轮子，而是让开发者保持熟悉的状态。

```
class UsersController < ApplicationController
  # Filters are methods that are run "before", "after" a controller action.
  before_action do
    # runs for specified actions
    only [:index, :world, :show] { increment(1) }
    # runs for all actions
    all { increment(1) }
  end

  after_action do
    # runs for specified actions
    only [:index, :world] { increment(1) }
  end

  def index
    @users = Users.all
    render("index.slang")
  end
end 
```

**视图模板**

Amber 支持广泛的模板语言，支持 4 种不同的模板语言:**俚语**、 **ecr** 、**小胡子**和 **temel** 。由于 Crystal 是一种编译语言，所以所有视图模板都是预编译的，在运行时，所有模板都已经加载到内存中，不需要磁盘读取、复杂的文件缓存或模板引擎计算，从而使您的页面呈现得更快、更流畅。

```
doctype html
html
  head
    meta name="viewport" content="width=device-width,initial-scale=1.0"
    title This is a title
    css:
      h1 {color: red;} 
      p {color: green;}
    style h2 {color: blue;}
  body
    ul 
     - @users.each do |user|
       li = "#{user.name} - #{user.email}" 
```

**管道**

管道是贯穿连接生命周期的一系列可组合的转换。我们在连接生命周期的每一步都与管道进行交互，核心的琥珀色组件，如端点、路由器和控制器，都只是内部管道。管道的基本思想是统一我们操作的“连接”的概念。这与其他 HTTP 中间件层(如 Rack)不同，在 Rack 中，请求和响应在中间件堆栈中是分开的。

```
Amber::Server.configure do |app|
  pipeline :web, :auth do
    plug Amber::Pipe::Logger.new
    plug Amber::Pipe::Flash.new
    plug Amber::Pipe::Session.new
    plug Amber::Pipe::CSRF.new
  end

  pipeline :auth do
    plug Authenticate.new
  end
end 
```

**参数验证**

验证的一个最重要的方面是防止无效数据在程序中的传播。参数验证是您的第一道防线，我们可以为给定端点的数据正确性设置一系列规则。这允许您将端点验证与模型分离，并在执行要求更高的操作之前尽早使请求无效。

```
update_params = params.validation do
  required(:name, "Your First Name is missing!") { |p| p.name? & !p.name.empty? }
  required(:email, "Your email address is invalid!") { |p| p.email? & p.size.between? 1..10 }
  required(:last_name) { |p| p.last_name? }
end 
```

**路由**

出于几个有利的原因，Amber 在一个中心文件中明确定义了路由。使用 **routes.cr** 文件，很容易在较高层次上理解正在为端点执行哪些转换。路由可以被限定到特定的路径，这对于表示 API 版本、CMS、管理区域非常有用。Websocket 路由是一个现有的组件，它们允许在单个 tcp 连接上以较低的开销定义全双工通信信道，便于实时数据传输到服务器。

```
routes :web, "/pages" do
  get "/about", StaticController, :about
  resources "/posts", PostController
  websocket "/chat", ChatSocket
end 
```

**频道**

所有 web-socket 消息都通过通道路由，通道主题是客户端订阅以侦听新消息的地方。通道定义了 3 种可以使用的公共方法:

*   handle_joined -当用户加入通道时调用。
*   handle _ message——当用户向通道发送消息时调用。一个通用的消息处理程序将简单地通过重播向其他订阅者重播消息！方法。
*   当用户离开频道时调用。

```
class HomeController < ApplicationController
  def index
    ChatSocket.broadcast("message", "chat_room:123", "message_new", {"message" => "A new visitor!"})
    render("index.slang")
  end
end 
```

**ORM 不可知论者**

默认情况下，Amber 带有一个名为 Granite::orm 的活动记录模式 ORM。你可以配置不同的 ORM 适配器琥珀由于琥珀模型层不依赖于琥珀，你可以自由使用任何晶体可用 ORM 花岗岩，Crecto，詹妮弗和幸运::记录

琥珀色 Cli

Amber 有一个内置的 CLI 工具，让您在开发应用程序时更加轻松。以下是可用命令的列表:

*   **d，部署**——提供服务器并部署项目。
*   **db，数据库** -执行数据库维护任务
*   加密 -加密环境 YAML 文件。[环境| -电子编辑|-无编辑]
*   **x，exec** -执行应用范围内的水晶代码
*   **g，生成** -生成琥珀色类(控制器、迁移、视图、模型、邮件程序等)
*   **n，new** -生成一个新的琥珀色项目
*   **路线** -打印所有已定义的应用路线
*   **w，观察** -启动 amber 开发服务器并根据文件更改进行重建

**系统测试**

Amber 在设计时就考虑到了测试。Amber 尽可能简单地提供您的系统规格。拥有一个内置的系统测试框架，比其他框架运行得更快、更一致。

```
class SomeFeature < GarnetSpec::System::Test
  scenario "user visits amber framework and sees getting started button" do
    visit "http://www.amberframework.org"
    timeout 1000
    click_on(:css, "header a.btn.btn-primary")
    wait 2000
    element(:tag_name, "body").text.should contain "Introduction"
  end
end 
```

Amber Framework 团队在这个项目上不知疲倦地工作，希望鼓励您试运行 Amber Framework，亲自看看它为您作为工程师和您的组织带来了什么价值。

amberframework.org([http://amberframework.org/](http://amberframework.org/))