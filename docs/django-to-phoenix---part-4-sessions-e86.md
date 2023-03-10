# Django 到 Phoenix -第 4 部分:会议

> 原文：<https://dev.to/michaeljones/django-to-phoenix---part-4-sessions-e86>

## 背景

我有一个用 Django 建立的网站，我想开始在[凤凰城](http://phoenixframework.org/)写它的一部分。

*   [第 0 部分:背景](https://dev.to/michaeljones/django-to-phoenix-part-0-6ij)
*   [第 1 部分:开发对接器](https://dev.to/michaeljones/django-to-phoenix-part-1-56h)
*   [第 2 部分:ginx](https://dev.to/michaeljones/django-to-phoenix---part-2-nginx-9kg)
*   [第 3 部分:初始 Phoenix 设置](https://dev.to/michaeljones/django-to-phoenix---part-3-initial-phoenix-setup-4ma)

## 免责声明

我是凤凰城的初学者。如果我做错了什么，我希望得到帮助和建议。

## 起点

在上一篇文章中，我们在我们的 [Docker](https://www.docker.com/) 容器中设置了 Phoenix。我们有 [nginx](https://www.nginx.com/resources/wiki/) 在凤凰城&姜戈之间分流流量。所有的请求都发送到 Django，除了临时的`/elixir`路径，我们将它发送到 Phoenix 进行实验。

在这篇文章中，我们将看看如何访问 Django 会话信息，这样我们的 Phoenix 应用程序就可以知道用户是否登录。我们将不得不:

*   检查会话 cookie
*   检查数据库中的会话表
*   向请求数据添加一些信息，以指示当前是否有用户会话。
*   在页面上显示会话的一些指示。

## 检查会话 Cookie

[官方文档](https://docs.djangoproject.com/en/1.11/topics/http/sessions)告诉我们 Django 使用 cookies 来跟踪会话 id。如果我们在 Chrome 中登录我们的 Django 应用程序，打开[检查器](https://developers.google.com/web/tools/chrome-devtools/)，进入应用程序选项卡，然后点击[cookie](https://developers.google.com/web/tools/chrome-devtools/manage-data/cookies)下的`localhost`条目，然后我们可以看到一个名为`sessionid`的 cookie，其中包含一个字符串。

我不是 cookie 专家，但我的理解是，该域的任何可用 cookie 都会随着每个标准 web 请求自动发送。因此，当我们导航到`/elixir`时，浏览器将发送相同的 cookies。

凤凰号建在一层叫做[塞](https://hexdocs.pm/plug/readme.html)的上面。查看 Plug 的[文档，我们可以看到 cookies 在`conn`数据上作为仙丹](https://hexdocs.pm/plug/Plug.Conn.html#module-fetchable-fields)[地图](https://hexdocs.pm/elixir/Map.html)可用。这是提供给处理请求的函数的标准连接数据。

为了访问这个 cookie，我们将设置一个名为`TimetableWeb.Session`的新 Phoenix 控制器。为此，我们在 Phoenix 项目的目录`lib/timetable_web/controllers/`中创建了一个名为`session.ex`的文件。它将有以下内容:

```
defmodule TimetableWeb.Session do
  import Plug.Conn

  def init(_) do
  end

  def call(conn, _) do
    case conn.cookies["sessionid"] do
      nil ->
        IO.puts "No sessionid found"
      session_id ->
        IO.puts "Session ID: #{session_id}"
    end

    # Return conn
    conn
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这是标准插头格式。目前，我们不需要在`init`函数中做任何事情，但它仍然是必需的。为了从`init`接收数据，`call`函数需要两个参数，但是我们不需要它，所以我们使用一个简单的`_`作为参数名。

至于插件的其余部分，我们在`conn.cookies`图中查找`sessionid`条目和结果上的[模式匹配](http://elixir-lang.github.io/getting-started/pattern-matching.html)。如果我们得到了`nil`，那么我们知道此刻我们没有活动的会话。如果我们得到的不是`nil`，那么我们知道我们必须有会话 ID。目前，我们打印一条适当的消息，并在函数结束时返回`conn`。在 Elixir 中，函数返回最后一个表达式的结果。在那种情况下，不需要显式的`return`关键字。

现在我们需要将我们的插件添加到 Phoenix 请求管道中，以便它能够处理传入的请求。为此，我们编辑了我们的`lib/timetable_web/router.ex`文件，做了如下修改:

```
 pipeline :browser do
     plug :accepts, ["html"]
     plug :fetch_session
     plug :fetch_flash
     plug :protect_from_forgery
     plug :put_secure_browser_headers
+    plug TimetableWeb.Session, repo: Timetable.Repo
   end

   pipeline :api do
     plug :accepts, ["json"]
   end 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们将插件添加到浏览器管道的末端。我们目前没有使用`api`管道，所以我们忽略它。

现在，如果我们进入 Django 应用程序，登录并访问`/elixir`，我们会看到:

```
[info] GET /elixir
Session ID: boj26dqs1v0okevigm2vnsu11gephx21 
```

Enter fullscreen mode Exit fullscreen mode

在 Docker 容器中 Phoenix 应用程序的控制台输出中。

如果我们现在进入 Django 应用程序，注销并访问`/elixir`，那么我们会看到:

```
[info] GET /elixir
No sessionid found 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们似乎成功地获得了会话 cookie。

## 检查会话数据库表

Django 不在会话 cookie 中存储有意义的数据。它只是一个 ID，用于从数据库的 sessions 表中访问数据。[文档](https://docs.djangoproject.com/en/1.11/topics/http/sessions/#technical-details)告诉我们会话数据存储在一个名为`django_session`的表中。如果我们运行`python manage.py dbshell`并在 [Postgresql](https://www.postgresql.org/) 提示符下运行:`\d django_session`，那么我们将得到表的描述:

```
 Table "public.django_session"
    Column    |           Type           | Modifiers 
-------------------+--------------------------+-----------
 session_key  | character varying(40)    | not null
 session_data | text                     | not null
 expire_date  | timestamp with time zone | not null
Indexes:
    "django_session_pkey" PRIMARY KEY, btree (session_key)
    "django_session_expire_date" btree (expire_date)
    "django_session_session_key_like" btree (session_key varchar_pattern_ops) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到有 3 列。`session_key`最有可能映射到我们的`session_id`。`session_data`很可能包含关于会话的有用信息。最后，还有一个`expire_date`来指示会话何时应该被视为“过时”并且不再被遵守。我们还可以看到,`session_key`字段是这个表的主键。

我们想让 Phoenix 应用程序能够检查这个数据库表来检索会话信息。Phoenix 使用一个叫做 [Ecto](https://hexdocs.pm/ecto/Ecto.html) 的层来建模数据库表并与数据库对话。我们将使用一个[混合](https://hexdocs.pm/mix/Mix.html)任务来帮助生成 Ecto 模式。我们将要运行的命令是:

```
mix phx.gen.schema Session sessions session_key:string session_data:string expire_date:date 
```

Enter fullscreen mode Exit fullscreen mode

这生成了一个几乎符合我们需要的 Ecto 模式。问题是它假设我们想要有一个额外的`id`列作为主键。这对很多表都有意义，但对主键是`session_key`列的`django_session`表没有意义。所以我们必须调整最终的模式来通知 Ecto 我们首选的主键。下面是它看起来的样子:

```
defmodule Timetable.Session do
  use Ecto.Schema
  import Ecto.Changeset
  alias Timetable.Session

  @primary_key {:session_key, :string, autogenerate: false}

  schema "django_session" do
    field :session_data, :string
    field :expire_date, :utc_datetime
  end

  @doc false
  def changeset(%Session{} = session, attrs) do
    session
    |> cast(attrs, [:session_key, :session_data, :expire_date])
    |> validate_required([:session_key, :session_data, :expire_date])
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

为了检查这是否有效，我们可以在 Elixir 交互 shell 中进行尝试: [iex](https://hexdocs.pm/iex/IEx.html) 。如果我们从 Phoenix 项目根目录运行`iex -S mix`，那么我们可以做:

```
iex(1)> Timetable.Session |> Timetable.Repo.get_by(session_key: "boj26dqs1v0okevigm2vnsu11gephx21")                                                       
[debug] QUERY OK source="django_session" db=131.4ms                          
SELECT d0."session_key", d0."session_data", d0."expire_date" FROM "django_session" AS d0 WHERE (d0."session_key" = $1) ["boj26dqs1v0okevigm2vnsu11gephx21"]                                     
%Timetable.Session{__meta__: #Ecto.Schema.Metadata<:loaded, "django_session">,                                                                            
 expire_date: #DateTime<2017-11-21 08:31:06.774119Z>,                        
 session_data: "ODNjNWFkMDJh....cl9pZCI6IjEifQ==",  
 session_key: "boj26dqs1v0okevigm2vnsu11gephx21" 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，我们收到了一条记录，其中包含我们感兴趣的会话数据。

如果我们也想检查`expire_date`，我们可以使用 Ecto 查询语法:

```
iex(1)> import Ecto.Query, only: [from: 2]
Ecto.Query
iex(2)> now = DateTime.utc_now()
#DateTime<2017-11-12 10:31:16.153448Z>
iex(3)> (from s in Timetable.Session, where: s.session_key == "boj26dqs1v0okevigm2vnsu11gephx21" and s.expire_date >= ^now) |> Timetable.Repo.one
[debug] QUERY OK source="django_session" db=24.0ms
SELECT d0."session_key", d0."session_data", d0."expire_date" FROM "django_session" AS d0 WHERE ((d0."session_key" = 'boj26dqs1v0okevigm2vnsu11gephx21') AND (d0."expire_date" >= $1)) [{{2017, 11, 12}, {10, 31, 16, 153448}}]
%Timetable.Session{__meta__: #Ecto.Schema.Metadata<:loaded, "django_session">,
  expire_date: #DateTime<2017-11-21 08:31:06.774119Z>,
  session_data: "ODNjNWFkMDJh....cl9pZCI6IjEifQ==",
  session_key: "boj26dqs1v0okevigm2vnsu11gephx21"} 
```

Enter fullscreen mode Exit fullscreen mode

当会话过期时，为了检查它是否没有返回任何东西，我们可以做:

```
iex(1)> import Ecto.Query, only: [from: 2]
Ecto.Query
iex(2)> {:ok, later, 0} = DateTime.from_iso8601("2017-11-22T00:00:00Z")                                                                            
{:ok, #DateTime<2017-11-22 00:00:00Z>, 0}
iex(3)> (from s in Timetable.Session, where: s.session_key == "boj26dqs1v0okevigm2vnsu11gephx21" and s.expire_date >= ^later) |> Timetable.Repo.one
[debug] QUERY OK source="django_session" db=2.9ms
SELECT d0."session_key", d0."session_data", d0."expire_date" FROM "django_session" AS d0 WHERE ((d0."session_key" = 'boj26dqs1v0okevigm2vnsu11gephx21') AND (d0."expire_date" >= $1)) [{{2017, 11, 22}, {0, 0, 0, 0}}]
nil 
```

Enter fullscreen mode Exit fullscreen mode

当在会话`expire_date`之后提供日期时，我们返回`nil`。

## 向请求中添加会话信息

现在我们有了从会话表中提取信息的方法，我们可以更新我们的会话插件来检查有效的会话。我们将把它更新成这样:

```
defmodule TimetableWeb.Session do
  import Plug.Conn
  import Ecto.Query, only: [from: 2]

  def init(opts) do
    Keyword.fetch!(opts, :repo)
  end

  def call(conn, repo) do
    now = DateTime.utc_now()
    case conn.cookies["sessionid"] do
      nil ->
        assign(conn, :logged_in, false)
      session_key ->
        case repo.one(from s in Timetable.Session, where: s.session_key == ^session_key and s.expire_date >= ^now) do
          nil ->
            assign(conn, :logged_in, false)
          _ ->
            assign(conn, :logged_in, true)
        end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们进行了两项主要调整:

1.  `init`函数已经更新，从`opts`中寻找并返回`:repo`引用。`call`函数已经更新，接收`repo`作为第二个参数。
2.  如果`call`函数在 cookie 中找到了一个会话密钥，我们将检查该会话的 sessions 表。如果会话有效，那么我们返回带有新的设置为真的`logged_in`字段的`conn`数据。在其他两种找不到有效 cookie 或会话的情况下，我们返回带有设置为 false 的`logged_in`字段的`conn`。

这还不是很高级。正常的系统也会想从会话表中访问`session_data`,但是我们将把它留给下一篇文章。

## 在页面上显示会话信息

最后一步，我们希望显示用户是否登录。我们现在将保持它非常基本，并更新凤凰城登陆页面的模板`lib/timetable_web/templates/page/index.html.eex`。我们将进行如下更改:

```
 <div class="row marketing">
+
+  <div>
+    <%= if @logged_in do %>
+      Logged In!
+    <% else %>
+      Not Logged In
+    <% end %>
+  </div>
+
   <div class="col-lg-6">
     <h4>Resources</h4> 
```

Enter fullscreen mode Exit fullscreen mode

这使用 [EEx](https://hexdocs.pm/eex/EEx.html) -嵌入的药剂-根据`logged_in`的值显示不同的内容。作为`conn`上的一个字段，`logged_in`值在我们的模板中是可访问的。

现在，当我们登录 Django 应用程序并导航到我们的`/elixir`页面时，我们将看到页面上显示的`Logged In!`。如果我们退出 Django 应用，回到这个页面，我们看到的是`Not Logged In`。

## 结论

我们让 Phoenix 应用程序从 Django sessions 表中读取会话信息。我们能够以基本的方式更新网页，以反映当前用户的登录状态。

在下一篇文章中，我们将扩展这种方法来检查用户表中的用户名。