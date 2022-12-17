# 我如何用 Mux、Go、PostgreSQL 和 GORM 构建 API

> 原文：<https://dev.to/aspittel/how-i-built-an-api-with-mux-go-postgresql-and-gorm-5ah8>

我看到很多关于围棋速度的讨论。根据[基准测试游戏](http://benchmarksgame.alioth.debian.org/u64q/compare.php?lang=go&lang2=node)显示，Go 比 Node 快得多，比 Java 快一点，并能绕过 Python 和 Ruby。尽管在性能水平上，Go 还是有相对不错的开发者体验。分号是隐式的，一些类型是推断出来的，非面向对象的特性使它更加灵活。这还没有提到内置的并发性！我决定我想在 Go 中构建一些东西，看看它是否会成为未来我的应用程序的可行编程语言。

## 设置

我首先得把 Go 下载到我的电脑上。通过 Go 网站下载的程序在我的电脑上无法运行——它一直死机。我最终试图通过自制软件安装它。当你安装 Go 时，你还必须在你的电脑上设置一个`$GOPATH`，它声明了你将创建 Go 项目的工作空间。我很难让它正常工作。最终，我将以下内容添加到我的`.zshrc`中，它终于工作了。

```
export GOPATH=$HOME/go
export GOROOT=/usr/local/opt/go/libexec
export PATH=$PATH:$GOPATH/bin
export PATH=$PATH:$GOROOT/bin 
```

Enter fullscreen mode Exit fullscreen mode

然后，我根据 Golang 网站上的[如何编写 Go 代码](https://golang.org/doc/code.html)教程中的建议，用`$HOME/go/src/github.com/user/rest-api`编写了我的代码。

## 入门

我开始使用 Go 网站上的 Try Go 教程。这是一个很好的介绍教程，我喜欢它的互动性。在简单的介绍之后，我对它感觉非常舒服。对我来说，Go 感觉像是 C++、Python 和 JavaScript 的混搭。对我来说，它并不像某些语言那样过于陌生！

## 最终项目

我很乐意继续学习更高级的概念——在这种情况下，我想构建一个 API。在过去一年左右的时间里，我一直非常倾向于微服务应用，因为我现在的重点是网络应用，所以我想建立一些基于网络的东西。我很难跟踪发送给人们的精彩的编码文章，所以我想建立一个工具，让人们能够跟踪并喊出他们找到的精彩文章。

我承认，在阅读了工具的文档之后，我直接进入了最终项目，而不是像我通常做的那样浏览教程——Go 对我来说非常舒服，尽管我确信专家们会对我进行一系列改进！

我从一个硬编码的 API 开始，里面只有几个项目——类似于 [Francis Sunday 的精彩教程](https://www.codementor.io/codehakase/building-a-restful-api-with-golang-a6yivzqdo)。通过那篇文章，我找到了 Gorilla Mux，它有助于 Go 中的路由。这种语言有一个内置的服务器，所以我不必为这个功能添加太多代码。

然后我想加入一个数据库。我用 PostgreSQL 做几乎所有的事情。我非常依赖它的 JSON 和数组字段，但是我更喜欢关系数据库。我也更喜欢在我的应用程序中使用 ORM，因为它们通常使查询在语法上更加优雅。我找到了 GORM，和它一起工作很棒。它没有内置所有的 Postgres 特性，但是我发现只使用“pq”Go 包就可以很容易地实现我想要的特性。

由于在 Go 中没有太多的资源来创建具有这种堆栈的 API，所以我想比平时更多地浏览一下我的代码。

导入依赖项后，我定义了一个`struct`。结构是“字段的集合”。虽然 Go 不是面向对象的，但对我来说，结构有点像类。您正在定义一个蓝图，然后在代码中创建它的实例。我希望在我的 API 中有几个字段:资源的链接、名称、作者、描述和与之相关的标签。输出侧的`created_at`、`updated_at`、`deleted_at`和`id`字段也增加了 GORM 和 Postgres。唯一棘手的领域是标记——我最终使用了 pq 的 StringArray，因为据我所知，它并没有内置到 GORM 中。

```
type Resource struct {
    gorm.Model

    Link        string
    Name        string
    Author      string
    Description string
    Tags        pq.StringArray `gorm:"type:varchar(64)[]"`
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我写了我的`main`函数。该函数在程序运行时自动运行，并启动程序中的其他操作。我从创建 Mux 路由器开始，它将简化应用程序中的 url 路由。然后，我为数据库连接设置了一个全局变量，这样我就可以在整个应用程序中使用它。我还做了一些错误处理，以防无法连接到数据库。我使用`os.Getenv`与我的`.env`文件中设置的环境变量进行交互。这也让我最终可以非常轻松地部署我的应用程序！我还使用 GORM 迁移了我的数据库，这样无论我使用什么数据库，当我启动我的应用程序时，模式都是正确的。

然后，我为我的应用程序实现了路线。我只有最初想创建的四个——获取全部、获取一个、发布和删除。我可能还会在某个时候添加一个更新的 PUT。我喜欢 Mux 提供的路由，它简单明了。

最后，我在`main`函数的最后一行启动了服务器——它只是指定了要使用的端口和路由器。它还指定在关闭服务器之前记录错误。

```
var db *gorm.DB
var err error

func main() {
    router := mux.NewRouter()

    db, err = gorm.Open(
        "postgres",
        "host="+os.Getenv("HOST")+" user="+os.Getenv("USER")+
        " dbname="+os.Getenv("DBNAME")+" sslmode=disable password="+ 
        os.Getenv("PASSWORD"))

    if err != nil {
        panic("failed to connect database")
    }

    defer db.Close()

    db.AutoMigrate(&Resource{})

    router.HandleFunc("/resources", GetResources).Methods("GET")
    router.HandleFunc("/resources/{id}", GetResource).Methods("GET")
    router.HandleFunc("/resources", CreateResource).Methods("POST")
    router.HandleFunc("/resources/{id}", DeleteResource).Methods("DELETE")

    log.Fatal(http.ListenAndServe(":"+os.Getenv("PORT"), router))
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我编写了我的路由处理函数。每个框架都接受 HTTP 响应和请求作为参数——类似于许多 web 框架。首先，我编写了获取所有资源的路径。我首先创建一个资源数组，然后查询数据库中的所有资源，将结果设置为资源数组。然后我发送响应，这是资源的 JSON。

```
func GetResources(w http.ResponseWriter, r *http.Request) {
    var resources []Resource
    db.Find(&resources)
    json.NewEncoder(w).Encode(&resources)
} 
```

Enter fullscreen mode Exit fullscreen mode

get one resource 路径是相似的——惟一的区别是首先要检索请求参数，以便在 one resource 的查询中使用。

```
func GetResource(w http.ResponseWriter, r *http.Request) {
    params := mux.Vars(r)
    var resource Resource
    db.First(&resource, params["id"])
    json.NewEncoder(w).Encode(&resource)
} 
```

Enter fullscreen mode Exit fullscreen mode

创建功能与前面的路线非常相似。这背后有个有趣的故事——我一时想不出为什么这个函数不工作——没有错误，但当我用 Postman 测试它时，这些字段被填为空白。然后我转到 CURl，它完全正常工作！实际上没有错误，我使用界面的能力下降了！

```
func CreateResource(w http.ResponseWriter, r *http.Request) {
    var resource Resource
    json.NewDecoder(r.Body).Decode(&resource)
    db.Create(&resource)
    json.NewEncoder(w).Encode(&resource)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我创建了删除路径。这一个与前面的相似，我只是在删除指定的一个后返回所有的资源。

```
func DeleteResource(w http.ResponseWriter, r *http.Request) {
    params := mux.Vars(r)
    var resource Resource
    db.First(&resource, params["id"])
    db.Delete(&resource)

    var resources []Resource
    db.Find(&resources)
    json.NewEncoder(w).Encode(&resources)
} 
```

Enter fullscreen mode Exit fullscreen mode

在每一段新代码之间，我在开发过程中对我的代码运行`go run`来检查我的 API。我在这方面没有太多问题——如果我的代码没有编译，错误消息会很清楚并且切中要害。我几乎没有注意到额外的步骤。最后，我`go install`了我的应用程序，然后就可以在我的电脑上运行可执行文件了。我还使用了`godep`进行依赖管理，因为我最终使用了相当多的库！

所有代码我都非常依赖 GORM 文档！这太棒了——用清晰的例子很容易理解！我强烈推荐我在这个应用中使用的所有库。

写完代码后，我还运行了内置的 linter，`gofmt`。我最初是以类似于我的 JavaScript 代码的格式编写的，linter 清理了它。我确实喜欢我的额外间距，但我也喜欢让我的代码如此容易地更好地适应 Go 风格指南！

我认为代码非常简单易懂！我在 Go 中为这个项目工作时获得了很多乐趣。最终的项目在 GitHub 上，并在 T2 在线部署。

## 部署

由于某种原因，我真的对部署这个应用程序感到紧张，因为我的编译语言经验不是基于网络的。事实上，我只为学校项目写过编译语言的代码！我最终按照 Heroku 网站上的步骤部署了 Go 应用程序。我确实不得不改变环境变量的存储方式，但除此之外，这些步骤做得相当好！我根本不需要做什么疯狂的事情！

## 下一步

我真的很喜欢用 Go 写代码。我肯定会在一个项目中再次使用它，尤其是当性能对一个项目很重要的时候。我也很想加入这个项目。我可能会在 API 端添加标签过滤、授权和更新路径。我也可能会添加一个前端到这个应用程序，以便更容易地与 API 交互。这个项目非常有趣，总的来说，从不用电脑到有了最终产品，我只花了大约四个小时！我发现语法很容易理解和实现，我甚至不介意必须处理指针、静态类型或编译！Go 100%得到我的认可，我会把它用在我过去用过的很多语言之上！

**我的一部分[关于学习新事物](https://medium.com/on-learning-new-things/learning-new-things-f4db7f16724)系列**