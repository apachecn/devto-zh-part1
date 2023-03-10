# Amber Web 框架及其开箱即用特性介绍

> 原文：<https://dev.to/eliasjpr/introduction-to-the-amber-web-framework-and-its-out-of-the-box-features-39c6>

[![](img/baa9b66575115b9496fa81c985ca8557.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_ghKdKIx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/docelic/amber-introduction/master/amber.png)

### **介绍 Amber Web 框架**
及其开箱即用的特性

Amber 使构建 web 应用程序变得简单、快速和愉快。T3】

# 简介

**Amber** 是用[水晶](http://www.crystal-lang.org)编写的 web 应用框架。主页在 amberframework.org 的，文档在 [Amber Docs](https://docs.amberframework.org) ，Github 库在 [amberframework/amber](https://github.com/amberframework/amber) ，聊天在 [Gitter](https://gitter.im/amberframework/amber) 或 FreeNode IRC channel #amber。

Amber 的灵感来源于 Kemal、Rails、Phoenix 等框架。它很容易习惯，而且比 Rails 之类的框架更直观。(但它确实从 Rails 继承了一些好的概念。)

本文档描述了 Amber 开箱即用的所有内容，按照逻辑顺序进行了排序，便于随时重复查阅。晶体级别不描述；希望来这里的读者对[水晶及其特性](https://crystal-lang.org/docs/overview/)有一个成型的了解。

# 安装

```
git clone https://github.com/amberframework/amber
cd amber
make # The result of 'make' is one file -- command line tool bin/amber

# To install the file, or to symlink the system-wide executable to current directory, run one of:
make install # default PREFIX is /usr/local
make install PREFIX=/usr/local/stow/
make force_link # can also specify PREFIX=... 
```

Enter fullscreen mode Exit fullscreen mode

安装或链接后，`amber`是您将使用
创建或管理琥珀色应用程序的命令。

# 创建新的琥珀 App

```
amber new <app_name> [-d DATABASE] [-t TEMPLATE_LANG] [-m ORM_MODEL] [--deps] 
```

Enter fullscreen mode Exit fullscreen mode

支持的数据库有 [PostgreSQL](https://www.postgresql.org/) (pg，默认) [MySQL](https://www.mysql.com/) (mysql)，以及 [SQLite](https://sqlite.org/) (sqlite)。

支持的模板语言有[俚语](https://github.com/jeromegn/slang)(默认)和 [ecr](https://crystal-lang.org/api/0.21.1/ECR.html) 。

俚语极其优雅，但与 HTML 的传统认知大相径庭。ECR 类似于 HTML，与鲁比 very 非常相似，但与俚语相比就不那么一般了，但如果你打算使用一些 HTML 站点模板(例如来自 [themeforest](https://themeforest.net/) )的页面是 HTML + CSS 或 SCSS，它可能是你的应用程序的最佳选择。(或者你也可以试试 [html2slang](https://github.com/docelic/html2slang/) ，它将 html 页面转换成俚语。)

在任何情况下，您都可以在一个项目中组合各种语言的模板，无论使用哪种语言，都要记住模板是编译到应用程序中的。在运行时，没有在磁盘上查找或在可用模板之间选择。这使得模板速度极快，并且是只读的，这是一个非常受欢迎的附带好处！

支持的 ORM 型号有 [granite](https://github.com/amberframework/granite-orm) (默认)和 [crecto](https://github.com/Crecto/crecto) 。

Granite 是一个非常好、简单、有效的 ORM 模型，您可以在其中编写自己的 SQL(也就是说，所有搜索查询通常看起来都像 YourModel.all("WHERE field1 =？而 field2 =？”，[值 1，值 2])。但是它也有所属/有关系，还有其他一些小事。(如果你碰巧知道并喜欢 Perl 的 DBI，它可能会在某些方面提醒你。)

支持的迁移引擎是 [micrate](https://github.com/juanedi/micrate) 。Micrate 非常简单，您基本上可以在迁移中编写原始 SQL。迁移文件中只有两个关键字，说明后面的 SQL 是向上迁移还是向下迁移。这些关键字是“- +micrate Up”和“- +micrate Down”。

如果提供了参数- deps，Amber 将自动在新目录中运行`crystal
deps`来安装碎片。

# 运行 App

在应用程序目录中创建并运行`crystal deps`后，即可启动应用程序。
(如果您已经使用参数- deps 调用了`amber new`，则没有必要运行 deps；在这种情况下，安柏是为你做的。)

要运行它，您可以使用几种不同的方法。有些当然适合开发，有些适合生产等。:

```
# For development, clean and simple - compiles and runs your app:
crystal src/<app_name>.cr

# For development, clean and simple - compiles and runs your app, but
# also watches for changes in files and rebuilds/re-runs automatically.
amber watch

# For production, compiles app with optimizations and places it in bin/app.
# Crystal by default compiles using 8 threads (tune if needed with --threads NUM)
crystal build --no-debug --release --verbose -t -s -p -o bin/<app_name> src/<app_name>.cr 
```

Enter fullscreen mode Exit fullscreen mode

请注意，花岗岩目前在边缘情况下有问题。例如，如果您创建了一个新模型，但是没有为它指定任何字段，那么在您添加至少一个字段之前，Amber 不会由于编译错误而启动( [#112](https://github.com/amberframework/granite-orm/issues/112) )。

默认情况下，Amber 使用一个新的 Linux 内核中提供的名为“端口重用”的特性。如果您得到一个错误“setsockopt:协议不可用”，这意味着您的内核没有它。请编辑`config/environments/development.yml`并将“端口重用”设置为假。

# 构建 App 和故障排除

不管是使用 Crystal 命令“运行”(默认)还是“构建”，应用程序总是被构建的。只是在运行模式下，生成的二进制文件不会被保存到文件中，而是被执行并在以后被丢弃。

为了加快构建速度，编译开发版本时不使用- release 标志。使用- release 标志，编译时间会明显变长，但是生成的二进制文件具有令人难以置信的性能。

Crystal 缓存编译的部分结果(*。o 文件等。)下，以便更快地进行后续构建。当你用`crystal [run]`而不是`crystal build`运行程序时，这个目录也是存放临时二进制文件的地方。

有时，由于缺少头文件或库，在 C 层构建应用程序会失败。如果 Crystal 不打印实际的 C 错误，它至少会打印导致它的编译器行。

查看实际错误的最好方法是复制粘贴打印的命令，并在终端中手动运行它。错误将会显示出来，并由此很容易确定原因。

`libgc`库到处都有一些问题。Crystal 自带`libgc`，但可能会和系统一冲突。在我的例子中，解决方案是安装然后移除包`libgc-dev`。

# REPL

通常，进入一个交互式控制台(想想 IRB shell)并初始化所有应用程序类是非常有用的。在 Ruby 中，这可以通过 IRB 或类似于`rails console`的命令来完成。

由于其性质，Crystal 没有自由格式的 REPL，但是你可以在应用程序的上下文中保存和执行脚本。一种方法是通过命令`amber x [filename]`。该命令将允许您键入或编辑内容，然后执行脚本。

另一种更专业的方式是通过独立的类似 REPL 的脚本工具 [cry](https://github.com/elorest/cry) 和 [icr](https://github.com/crystal-community/icr) 。`cry`最初是一项实验，是`amber x`的前身，但现在提供了额外的功能，比如在调用`cry -r`时重复编辑和运行脚本。

在任何情况下，“在应用程序上下文中”运行一个脚本仅仅意味着需要`config/application.cr`(通过它，`config/**`)，因此，一定要在`config/application.cr`中列出你所有的需求，这样一切都会按预期工作。

# 文件结构

因此，在这一点上，你可能想知道什么被放置在一个琥珀申请的地方。默认结构如下:

```
./config/                  - All configuration
./config/application.cr    - Main configuration file
./config/initializers/     - Initializers (files loaded at very beginning)
./config/environments/     - Environment-specific YAML configurations
./config/webpack/          - Webpack (asset bundler) configuration
./config/routes.cr         - All routes
./db/migrations/           - All DB migration files (created with 'amber g migration ...')
./public/                  - The "public" directory for static files
./public/dist/             - Directory inside "public" for generated files and bundles
./public/diimg/
./src/                     - Main source directory, with <app_name>.cr being the main/entry file
./src/controllers/         - All controllers
./src/models/              - All models
./src/views/layouts/       - All layouts
./src/views/               - All views
./src/views/home/          - Views for HomeController (path "/")
./src/assets/              - Static assets which will be bundled and placed into ./public/dist/
./src/assets/stylesheets/
./src/assets/fonts/
./src/asseimg/
./src/assets/javascripts/
./spec/                    - Tests (named *_spec.cr) 
```

Enter fullscreen mode Exit fullscreen mode

我更喜欢在应用程序的根目录中直接访问其中的一些目录，并将配置目录命名为`etc`，所以我运行:

```
ln -sf config etc
ln -sf src/assets
ln -sf src/controllers
ln -sf src/models
ln -sf src/views
ln -sf src/views/layouts 
```

Enter fullscreen mode Exit fullscreen mode

# 数据库命令

Amber 在“db”组下提供了一组命令，允许使用数据库。您最可能想运行的简单命令是:

```
amber db create
amber db status
amber db version 
```

Enter fullscreen mode Exit fullscreen mode

在这些命令生效之前，您需要配置数据库
凭证:

首先，创建一个用户来访问数据库。对于 PostgreSQL，这是通过调用类似于:
的东西来完成的

```
$ sudo su - postgres
$ createuser -dElPRS myuser
Enter password for new role: 
Enter it again: 
```

Enter fullscreen mode Exit fullscreen mode

然后，编辑`config/environments/development.yml`并配置“database_url:”以匹配您的设置。如果没有别的，那么“postgres:@”部分应该替换为“yourusername:yourpassword@”。

然后从本节开始尝试数据库命令。

请注意，要使数据库连接成功，所有参数都必须正确(主机名、端口、用户名、密码、数据库名称)，数据库服务器必须可访问，并且数据库必须实际存在(除非您调用' amber db create '来创建它)。如果*在连接到数据库的任何阶段*出现任何错误，错误信息将非常简洁，只说“连接不成功:”。不过，解决方案很简单——只需使用打印的 database_url 手动尝试使用相同的参数连接到数据库，问题很可能会很快暴露出来。

请注意，非生产环境的环境文件以纯文本形式给出。生产环境的环境文件被加密以增加安全性，并且可以通过调用`amber encrypt`来查看或编辑。

# 路线

路线非常容易理解。路由将 HTTP 方法(以及调用它们的路径)连接到琥珀色端的控制器和方法。

琥珀色包括一个奇妙的命令`amber routes`来显示当前路线。默认情况下，路由表如下所示:

```
$ amber routes

╔══════╦═══════════════════════════╦════════╦══════════╦═══════╦═════════════╗
║ Verb | Controller                | Action | Pipeline | Scope | URI Pattern ║
╠──────┼───────────────────────────┼────────┼──────────┼───────┼─────────────╣
║ get  | Amber::Controller::Static | index  | static   |       | /*          ║
╠──────┼───────────────────────────┼────────┼──────────┼───────┼─────────────╣
║ get  | HomeController            | index  | web      |       | /           ║
╚══════╩═══════════════════════════╩════════╩══════════╩═══════╩═════════════╝ 
```

Enter fullscreen mode Exit fullscreen mode

从这个例子中，我们看到一个“GET /”请求将实例化
HomeController，然后调用其中的方法 index()。
该方法的返回值将被返回给客户端。

类似地，这里有一个路由的例子，它将 HTTP POST 请求路由到“/registration”到类 RegistrationController:
中的方法 create()

```
post "/registration", RegistrationController, :create 
```

Enter fullscreen mode Exit fullscreen mode

按照惯例，标准 HTTP 动词(GET、HEAD、POST、PUT、PATCH、DELETE)转到控制器上的标准方法(show、new、create、edit、update、destroy)。然而，没有什么可以阻止你在控制器中将 URL 路由到你想要的任何方法，就像我们在上面对“index”所做的那样。

也支持 Websocket 路由。

针对`config/routes.cr`文件的 DSL 语言在 [dsl/router.cr](https://github.com/amberframework/amber/blob/master/src/amber/dsl/router.cr) 和 [dsl/server.cr](https://github.com/amberframework/amber/blob/master/src/amber/dsl/server.cr) 中定义。

它为您提供了以下顶级命令/块:

```
# Define a pipeline
pipeline :name do
  ...
end

# Group a set of routes
routes :name, "path" do
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

如:

```
Amber::Server.configure do |app|
  pipeline :web do
    # Plug is the method used to connect a pipe (middleware)
    # A plug accepts an instance of HTTP::Handler
    plug Amber::Pipe::Logger.new
  end

  routes :web do
    get "/", HomeController, :index    # Routes to HomeController::index()
    get "/test", PageController, :test # Routes to PageController::test()
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在“路线”中，以下命令可用:

```
get, post, put, patch, delete, options, head, trace, connect, websocket, resources 
```

Enter fullscreen mode Exit fullscreen mode

`resources`是一个宏，定义为:

```
 macro resources(resource, controller, only = nil, except = nil) 
```

Enter fullscreen mode Exit fullscreen mode

除非它被参数`only`或`except`限制，否则它将自动为您的资源定义获取、发布、上传、修补和删除路径，并将它们路由到控制器中的以下方法:

```
index, new, create, show, edit, update, destroy 
```

Enter fullscreen mode Exit fullscreen mode

请注意，目前不可能为同一路径上的 HEAD 和 GET 方法定义不同的行为，因为如果定义了 GET，它也会自动添加匹配的 HEAD 路由。这将导致同一路径存在两条头路由，并触发错误`Amber::Exceptions::DuplicateRouteError`。

# 视图

关于视图的信息可以用要点来概括:

*   琥珀色视图位于`src/views/`
*   它们是使用`render()`渲染的
*   给`render()`的第一个参数是模板名(例如`render("index.slang")`)
*   如果我们在控制器的上下文中，`render("index.slang")`将使用路径`src/views/<controller_name>/index.slang`寻找视图
*   如果我们不渲染局部，默认情况下，模板将被包装在布局中
*   如果没有给出布局名称，默认布局将是`views/layouts/application.slang`
*   模板名称没有不必要的魔力——给定的名称是在磁盘上查找的名称
*   按照惯例，分音以“_”开头，但这不是必需的
*   要渲染局部，请使用`render( partial: "_name.ext")`

# 视图中的变量

在 Amber 中，模板在与控制器方法相同的范围内编译。这意味着您不需要实例变量来将信息从控制器传递到视图。

您在控制器方法中定义的任何变量在模板中都自动可见。例如，让我们将当前日期和时间显示添加到我们的/about 页面:

```
$ vi src/controllers/page_controller.cr

def about
    time = Time.now
    render "about.ecr"
end

$ vi src/views/page/about.ecr

Hello, World! The time is now <%= time %>. 
```

Enter fullscreen mode Exit fullscreen mode

模板实际上是在控制器类中执行的。如果你在上面的例子中做了“，那么响应将是“PageController”。因此，控制器上的所有方法和变量在从它呈现的视图中也是可用的。

# 启动服务器

请务必准确解释从运行应用程序到 Amber 开始为应用程序提供服务期间发生了什么:

1.  `crystal src/<app_name>.cr` -你或脚本启动琥珀色
    1.  `require "../config/*"`——作为第一件事，需要`config/*`。包含按字母顺序排列。Crystal 只寻找*。加载 cr 文件，并且只加载 config/中的文件(没有子目录)
        1.  `require "../config/application.cr"` -这通常是`config/`中的第一个文件
            1.  `require "./initializers/**"` -加载所有初始化器。默认只有一个初始化器文件，名为`initializer/database.cr`。这里我们有一个双星(“**”)表示包含所有文件，包括子目录中的文件。包含始终是当前的-首先是方向，然后是深度
            2.  `require "amber"` -琥珀本身是装的
                1.  加载琥珀色使`Amber::Server`类可用
                2.  `include Amber::Environment` -在此阶段，已经确定了环境并从 yml 文件(例如从`config/environments/development.yml`)加载了设置。设置随后可作为`settings`使用
            3.  `require "../src/controllers/application_controller"` -需要主控制器。这是所有其他控制器的基类
                1.  它定义了`ApplicationController`，包含了 JasperHelpers，并设置了默认布局(“application.slang”)。
            4.  `require "../src/controllers/**"` -加载所有其他控制器
            5.  `Amber::Server.configure`块被调用以覆盖任何配置设置
        2.  `require "config/routes.cr"` -这再次调用`Amber::Server.configure`块，但它只关心路由，并输入所有路由
    2.  `Amber::Server.start`被调用
        1.  `instance.run` -隐式创建服务器的单例实例，保存到`@@instance`，并在其上调用`run`
        2.  参考变量`settings.process_count`
        3.  如果进程计数为 1，则调用`instance.start`
        4.  如果进程计数> 1，则在主进程进入睡眠状态时，将分叉所需数量的进程
            1.  Forks 调用 Process.run()并启动完全独立的进程，这些进程从一开始就经历相同的初始化过程。分叉进程将 env 变量“forged”设置为“1”，并将变量“id”设置为它们的进程号。id 以相反的顺序分配(最高数字==第一个分叉)。
        5.  每个进程都调用`instance.start`
            1.  它保存当前时间并打印启动信息
            2.  `@handler.prepare_pipelines`被称为。@handler 是 Amber::Pipe::Pipeline，是 Crystal 的 HTTP::Handler 的子类。调用`prepare_pipelines`来连接管道，以便处理可以工作，并隐式添加 Amber::Pipe::Controller(调用 app 的控制器的管道)作为最后一个管道。这个管道的职责是调用 Amber::Router::context . process _ request，它实际上将请求分派给控制器。
            3.  `server = HTTP::Server.new(host, port, @handler)` - Crystal 的 HTTP 服务器创建完成
            4.  `server.tls = Amber::SSL.new(...).generate_tls if ssl_enabled?`
            5.  Signal::INT 被捕获(接收时调用`server.close`)
            6.  `loop do server.listen(settings.port_reuse) end` -服务器进入主循环

# 上菜请求

与启动服务器类似，准确解释 Amber 处理请求时发生的情况非常重要:

Amber 的应用服务模型基于 Crystal 的内置底层功能:

1.  正在运行的服务器是 Crystal 的 [HTTP::Server](https://crystal-lang.org/api/0.24.1/HTTP/Server.html) 的一个实例
2.  对于每个传入的请求，都会调用处理程序。正如 Crystal 所支持的，handler 可以是简单的 Proc 或 HTTP::Handler 的实例。HTTP::处理程序有一个“下一个”的概念，多个可以连接成一行。在 Amber 中，这些单独的处理程序被称为“管道”，整体处理程序(完整的管道链)是 Amber::Pipe::Pipeline，为了方便起见，它也是 [HTTP::Handler](https://crystal-lang.org/api/0.24.1/HTTP/Handler.html) 的子类。尽管这个处理程序被命名为 Pipeline 并暗示了一个特定的管道，但它实际上知道所有的管道，并能识别和触发适当的管道
3.  在管道中，每个管道(Amber::Pipe::*，最终是 Handler 的子类)都是用一个参数调用的。这个参数按照惯例被称为“上下文”，它是`HTTP::Server::Context`的一个实例，有两个内置方法——`request`和`response`，分别访问请求和响应部分。除此之外，Amber 还添加了其他各种方法和变量，比如`router`、`flash`、`cookies`、`session`、`content`、`route`，以及其他在[src/Amber/router/context . Cr](https://github.com/amberframework/amber/blob/master/src/amber/router/context.cr)中看到的方法和变量
4.  请注意，调用管道链不是自动的；每个管道都需要在其执行的适当点调用`call_next(context)`来调用一行中的下一个管道。没有必要检查下一个管道是否存在，因为当前`Amber::Pipe::Controller`总是被隐式添加为最后一个管道，所以至少有一个管道存在。管道间的状态不是通过变量传递，而是通过修改`context`和其中包含的数据

之后，管线、管道、路线和其他特定于成员的零件开始发挥作用。

所以，详细地说，从一开始:

1.  `loop do server.listen(settings.port_reuse) end` -主循环正在运行
    1.  `spawn handle_client(server.accept?)` -连接被接受后，在新的纤程中调用 handle_client()
        1.  `io = OpenSSL::SSL::Socket::Server.new(io, tls, sync_close: true) if @tls`
        2.  `@processor.process(io, io)`
            1.  `if request.is_a?(HTTP::Request::BadRequest); response.respond_with_error("Bad Request", 400)`
            2.  `response.version = request.version`
            3.  `response.headers["Connection"] = "keep-alive" if request.keep_alive?`
            4.  `context = Context.new(request, response)` -这个上下文已经在[src/Amber/router/context . Cr](https://github.com/amberframework/amber/blob/master/src/amber/router/context.cr)中用 Amber 的扩展进行了扩展
            5.  `@handler.call(context)` - `Amber::Pipe::Pipeline.call()`被称为
                1.  `raise ...error... if context.invalid_route?` -提前检查路线有效性
                2.  `if context.websocket?; context.process_websocket_request` -如果是 websocket，就这样解析
                3.  `elsif ...; ...pipeline.first...call(context)` -如果是普通的 HTTP 请求，调用适当管道中的第一个处理程序

# 静态页面

可以想见，一个网站需要一组简单的“静态”页面。这些页面由应用程序提供服务，但大多不使用数据库或任何复杂的代码。此类页面可能包括关于和联系页面、条件条款等。完成这项工作是微不足道的。

假设，为了简单和分组，我们希望所有“静态”页面都由 PageController 提供服务。我们将所有这些页面分组到一个公共的 web 可访问前缀/page/下，最后我们将页面请求路由到 PageController 的方法。(因为这些页面不是对象，所以我们不需要模型或任何其他东西，只需要一个控制器方法和每页一个视图。)

让我们从创建一个控制器开始:

```
amber g controller page 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们编辑`config/routes.cr`将 URL“/about”链接到 PageController 中的 about()方法。我们在“routes :web”块中做这件事:

```
routes :web do 
  ...
  get "/about", PageController, :about
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们编辑控制器并实际添加关于()的方法。这个方法可以直接返回一些字符串作为响应，或者它可以呈现一个视图，然后展开的视图内容将作为响应返回。

```
$ vi src/controllers/page_controller.cr

# Inside the file, we add:

def about
  # "return" can be omitted here. It is included only for clarity.
  return render "about.ecr"
end 
```

Enter fullscreen mode Exit fullscreen mode

由于这发生在“页面”控制器中，用于查找模板的视图目录默认为`src/views/page/`。我们将在其中创建目录和文件“about . ECR”:

```
$ mkdir -p src/views/page/
$ vi src/views/page/about.ecr

# Inside the file, we add:

Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

因为我们调用了 render()而没有额外的参数，所以模板将默认在默认的应用程序布局`views/layouts/application.cr`中呈现。

就是这样！访问`/about`会到路由器，路由器会调用`PageController::about()`，那个方法会在布局`views/layouts/application.cr`的上下文中渲染模板`src/views/page/about.ecr`，渲染的结果会是一整页，正文中有内容`Hello, World!`。该结果将被返回给控制器，并从那里返回给客户机。

# 资产管道

在琥珀色项目中，原始资产位于`src/assets/` :

```
src/assets/
src/assets/fonts
src/assets/images
src/assets/javascripts
src/assets/javascripts/main.js
src/assets/stylesheets/main.scss 
```

Enter fullscreen mode Exit fullscreen mode

在构建时，所有这些都被处理并放在`public/dist/`下。
JS 资源捆绑到`main.bundle.js`，CSS 资源捆绑到`main.bundle.css`。

目前，webpack 正被用于资产管理。我建议至少换成[包裹](https://parceljs.org/)。为此目的找到一个非 js/非节点/非 npm 应用程序会更好；如果你知道一个，请让我知道。

本节将扩展到包括完整的更换程序。(总的来说，这似乎不会比替换项目的`package.json`文件中的命令和开发依赖项复杂多少。)

# 默认分片

默认情况下，Amber 项目只依赖于几个碎片:

```
amberframework/amber          - Obviously, must depend on Amber
amberframework/granite-orm    - Database ORM
amberframework/quartz-mailer  - Sending and receiving emails
amberframework/jasper-helpers - Helpers for working with HTML in Amber/Crystal
will/crystal-pg               - PostgreSQL connector
amberframework/garnet-spec    - Extended Crystal specs for testing web applications 
```

Enter fullscreen mode Exit fullscreen mode

反过来，这些取决于:

```
luislavena/radix                      - Radix Tree implementation
jeromegn/kilt                         - Generic template interface
jeromegn/slang                        - Slang template language
stefanwille/crystal-redis             - 
amberframework/cli                    - Building cmdline apps (based on mosop)
mosop/optarg                          - Parsing cmdline args
mosop/callback                        - Defining and invoking callbacks
mosop/string_inflection               - Word plurals, counts, etc.
amberframework/teeplate               - Rendering multiple template files
juanedi/micrate                       - Database migration tool
crystal-lang/crystal-db               - Common DB API
jwaldrip/shell-table.cr               - Creates textual tables in shell
askn/spinner                          - Spinner for the shell
crystal-lang/crystal-mysql            - 
crystal-lang/crystal-sqlite3          - 
amberframework/smtp.cr                - SMTP client (to be replaced with arcage/crystal-email)
ysbaddaden/selenium-webdriver-crystal - Selenium Webdriver client 
```

Enter fullscreen mode Exit fullscreen mode

还有基本水晶的内置碎片:

```
http
logger
json
colorize
random/secure 
```

Enter fullscreen mode Exit fullscreen mode

只有被使用的部分最终会出现在编译后的项目中。

现在让我们浏览一下 Amber 应用程序中存在的所有重要的类，这些类对于理解流程是有用的。

# 分机

Amber 为现有的字符串和数字类增加了一些非常方便的扩展。这些扩展位于 [extensions/](https://github.com/amberframework/amber/tree/master/src/amber/extensions) 目录中，但这里列出了当前的扩展:

对于字符串:

```
 def str?
      def email?
      def domain?
      def url?
      def ipv4?
      def ipv6?
      def mac_address?
      def hex_color?
      def hex?
      def alpha?(locale = "en-US")
      def numeric?
      def alphanum?(locale = "en-US")
      def md5?
      def base64?
      def slug?
      def lower?
      def upper?
      def credit_card?
      def phone?(locale = "en-US")
      def excludes?(value)
      def time_string? 
```

Enter fullscreen mode Exit fullscreen mode

对于编号:

```
 def positive?
      def negative?
      def zero?
      def div?(n)
      def above?(n)
      def below?(n)
      def lt?(num)
      def self?(num)
      def lteq?(num)
      def between?(range)
      def gteq?(num) 
```

Enter fullscreen mode Exit fullscreen mode

# 支持套路

在 [support/](https://github.com/amberframework/amber/tree/master/src/amber/support) 目录中有许多不同的支持文件，它们提供了额外的现成例程。

目前，可以在那里找到以下内容:

```
client_reload.cr      - Support for reloading developer's browser

file_encryptor.cr     - Support for storing/reading encrypted versions of files
message_encryptor.cr
message_verifier.cr

locale_formats.cr     - Very basic locate data for various, manually-added locales

mime_types.cr         - List of MIME types and helper methods for working with them:
                        def self.mime_type(format, fallback = DEFAULT_MIME_TYPE)
                        def self.zip_types(path)
                        def self.format(accepts)
                        def self.default
                        def self.get_request_format(request) 
```

Enter fullscreen mode Exit fullscreen mode

# 琥珀色::控制器::底座

这是所有其他控制器继承的基本控制器。源文件在[src/amber/controller/base . Cr](https://github.com/amberframework/amber/blob/master/src/amber/controller/base.cr)中。

对于每个请求，适当的控制器被实例化，并且它的 initialize()运行。因为这是基本控制器，所以这段代码会在每个请求上运行，这样您就可以了解每个控制器的上下文中有哪些可用的内容。

这个控制器的内容和它从包含其他模块中获得的方法足够直观，可以复制到这里，并在必要的地方进行注释:

```
module Amber::Controller
  class Base
    include Helpers::CSRF
    include Helpers::Redirect
    include Helpers::Render
    include Helpers::Responders
    include Helpers::Route
    include Callbacks

    protected getter context : HTTP::Server::Context
    protected getter params : Amber::Validators::Params

    delegate :cookies, :format, :flash, :port, :requested_url, :session, :valve,
      :request_handler, :route, :websocket?, :get?, :post?, :patch?,
      :put?, :delete?, :head?, :client_ip, :request, :response, :halt!, to: context

    def initialize(@context : HTTP::Server::Context)
      @params = Amber::Validators::Params.new(context.params)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

[帮手:【CSRF】](https://github.com/amberframework/amber/blob/master/src/amber/controller/helpers/csrf.cr)提供了这些方法:

```
 def csrf_token
    def csrf_tag
    def csrf_metatag 
```

Enter fullscreen mode Exit fullscreen mode

[助手::重定向](https://github.com/amberframework/amber/blob/master/src/amber/controller/helpers/redirect.cr)提供:

```
 def redirect_to(location : String, **args)
    def redirect_to(action : Symbol, **args)
    def redirect_to(controller : Symbol | Class, action : Symbol, **args)
    def redirect_back(**args) 
```

Enter fullscreen mode Exit fullscreen mode

[助手::渲染](https://github.com/amberframework/amber/blob/master/src/amber/controller/helpers/render.cr)提供:

```
 LAYOUT = "application.slang"
    macro render(template = nil, layout = true, partial = nil, path = "src/views", folder = __FILE__) 
```

Enter fullscreen mode Exit fullscreen mode

[Helpers::Responders](https://github.com/amberframework/amber/blob/master/src/amber/controller/helpers/responders.cr) 帮助控制将返回给客户端的最终状态代码、主体和内容类型。

[帮手::路线](https://github.com/amberframework/amber/blob/master/src/amber/controller/helpers/route.cr)提供:

```
 def action_name
    def route_resource
    def route_scope
    def controller_name 
```

Enter fullscreen mode Exit fullscreen mode

[回调](https://github.com/amberframework/amber/blob/master/src/amber/dsl/callbacks.cr)提供:

```
 macro before_action
    macro after_action 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

我们希望你喜欢这个琥珀的实践介绍！

请随时就您希望在本指南中看到的内容或其他方面提供反馈。谢谢！