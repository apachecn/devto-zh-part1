# Elm 中的远程数据请求 API

> 原文：<https://dev.to/johndashkelly/a-remote-data-request-api-in-elm-3e5e>

这篇文章是关于 elm-postgrest 包中的核心抽象，以及这些抽象如何与类似的包相关。

* * *

在 Elm 中，**远程数据请求 API**的设计空间已经见证了它公平的工作份额。

我们有像`lukewestby/elm-http-builder`这样的 API，它们在`elm-lang/http`之上提供了一个薄薄的便利层。

```
addItem : String -> Cmd Msg
addItem item =
    HttpBuilder.post "http://example.com/api/items"
        |> withQueryParams [ ("hello", "world") ]
        |> withHeader "X-My-Header" "Some Header Value"
        |> withJsonBody (itemEncoder item)
        |> withTimeout (10 * Time.second)
        |> withExpect (Http.expectJson itemsDecoder)
        |> withCredentials
        |> send handleRequestComplete 
```

Enter fullscreen mode Exit fullscreen mode

我们有像`krisajenkins/remotedata`这样的 API，它们对远程数据可能呈现的各种状态进行建模。

```
type RemoteData e a
    = NotAsked
    | Loading
    | Failure e
    | Success a 
```

Enter fullscreen mode Exit fullscreen mode

而且，我们有像`jamesmacaulay/elm-graphql`、`jahewson/elm-graphql`、`dillonkearns/graphqelm`、`mgold/elm-data`、`noahzgordon/elm-jsonapi`等 API，它们抽象了`elm-lang/http`来提供一个在它们各自规范的领域语言中很好的 API。我们将这组 API 称为*后端特定请求构建器*。

除了社区的努力之外，Evan 自己也在 Elm 撰写了[数据交换的愿景。尽管这一特定愿景的 API 可能与`elm-lang/http`、`Json.Decode`和`Json.Encode`处于同一抽象层次，而不是后端特定请求构建器，但它使围绕“如何在客户机和服务器之间发送信息”的探索合法化](https://gist.github.com/evancz/1c5f2cf34939336ecb79b97bb89d9da6)

# 设计空间

远程数据请求 API 的设计空间是什么？更具体地说，后端特定请求构建器的设计空间是什么？

在这篇文章中，我们将设计空间定义为:

* * *

一种描述数据模型能力的方法，随后，客户端-服务器应用程序的数据模型构建请求。

* * *

具有以下设计目标:

*   领域语言 vs HTTP -我们希望用他们自己的术语而不是原始的传输协议与我们的后端交互。例如，在 GraphQL 的上下文中，这意味着查询、突变、选择集、片段等。
*   选择 vs 解码器 -我们想谈论我们希望选择什么，而不是我们希望如何解码。
*   **Resources vs JSON**——我们想从我们的数据模型的抽象表示方面来说，而不是它的具体交换格式和/或存储格式。
*   **类型化与非类型化**——我们希望使用应用程序的值而不是查询字符串的连接来组合我们的请求。

让我们再来看看这些设计目标，但这次是以图表的形式:

| 请求生成器 | 模式描述 |
| CRUD 请求
选择
条件
顺序
限制和偏移
分页 | 资源模式
属性
关系
基数 |
| 

<center>*抽象屏障*</center>

 |
| 传输协议 | 交换格式 |
| HTTP:
headers
body
methods
URL:query and fragment
状态码 | JSON
edn
XML
transit
proto buf |

图中的水平分割线代表一个*抽象障碍*。在这种情况下，障碍将后端特定请求构建器(上图)与它们的实现(下图)分开。一层的用户不需要关心下面的细节。本文的剩余部分将在后端特定请求构建器的抽象层次上研究一个 Elm API。

# elm-postgrest

我是`john-kelly/elm-postgrest`的作者；一个抽象了`elm-lang/http`、`Json.Decode`和`Json.Encode`的包，在 PostgREST 的上下文中提供了一个很好的 API。如前所述，这个包属于后端特定请求构建器的范畴。

这篇文章是关于 elm-postgrest 包中的核心抽象，以及这些抽象如何与类似的包相关。所有的例子都将基于从`john-kelly/elm-postgrest-spa-example`开始的工作，这是一个从`rtfeldman/elm-spa-example`到 PostgREST 的几乎完整的端口。对于那些不熟悉 PostgREST 的人，这里有一段来自他们官方文档的摘录:

> PostgREST 是一个独立的 web 服务器，它将您的 PostgreSQL 数据库直接转换成 RESTful API。数据库中的结构约束和权限决定了 API 端点和操作...PostgREST 哲学建立了一个单一的真理来源:数据本身。

这三个部分如何组合在一起的心理模型:

* * *

#### elm-postgrest(客户端)⇄ PostgREST(服务器)⇄ PostgreSQL(数据库)

* * *

如果你想知道，阅读这篇文章不需要 PostgREST 知识，但是，关于 Elm 和 REST、GraphQL、JSON API、Firebase、Parse 或其他远程数据服务器规范的中级知识会有所帮助。

好吧。现在我们有了一些背景，让我们深入研究代码。

# 我们的第一个要求

我们的第一个请求将从我们的远程数据服务器检索所有文章。

对于这个例子，我们将假设我们在`example.com/api/articles`有一个文章资源集合。每篇文章都有一个标题、一个正文和收藏的数量。

在这个例子中，我对代码采用了自顶向下的方法。请记住这一点！后面的章节将帮助您更好地理解前面的章节。

## 类型

我们将从 elm-postgrest 中的 4 种核心类型开始。我提供了每种类型的内部实现，但是，不要陷入定义中。我展示了这个实现，试图将新的 PostgRest 类型建立在您熟悉的基础上。

```
import PostgRest as PG
    exposing
        ( Request
        , Selection
        , Schema
        , Attribute
        ) 
```

Enter fullscreen mode Exit fullscreen mode

*   **请求**——一个完全构建的请求。剩下唯一要做的事情就是将这个值转换成一个`Http.Request`并将它发送到 Elm 运行时。正如我们将要学习的，一个`Request`可以由一个`Selection`和一个`Schema`构成。

```
type Request a
    = Read
        { parameters : Parameters
        , decoder : Decode.Decoder a
        } 
```

Enter fullscreen mode Exit fullscreen mode

*   **选择**—`Selection`是根据数据模型构建请求的主要方法之一。具体来说，`Selection`表示选择哪些字段和嵌入哪些相关资源。

```
type Selection attributes a
    = Selection
        (attributes
         ->
            { attributeNames : List String
            , embeds : List Parameters
            , decoder : Decode.Decoder a
            }
        ) 
```

Enter fullscreen mode Exit fullscreen mode

*   **模式**—`Schema`是描述数据模型能力的方法。*功能*是指我们可以选择的内容、我们可以过滤的内容以及我们可以排序的内容。在这篇文章中，我们只讨论选择。

```
type Schema id attributes
    = Schema String attributes 
```

Enter fullscreen mode Exit fullscreen mode

*   **属性**-`Schema`的单个可选择单元。例如，文章资源有一个标题`Attribute String`。

```
type Attribute a
    = Attribute
        { name : String
        , decoder : Decode.Decoder a
        , encoder : a -> Encode.Value
        , urlEncoder : a -> String
        } 
```

Enter fullscreen mode Exit fullscreen mode

## 请求

这里我们构造了一个`Request`，它将产生一个`List String`。这种类型的心理模型应该与`Http.Request`的相同:“如果我们发送这个`Request`，我们可以期待返回一个`List String`。”

```
getArticles : Request (List String)
getArticles =
    PG.readAll articleSchema articleSelection 
```

Enter fullscreen mode Exit fullscreen mode

在进入下一节之前，我们先来看看`PG.readAll`的函数签名。

```
readAll : Schema id attributes -> Selection attributes a -> Request (List a) 
```

Enter fullscreen mode Exit fullscreen mode

从`readAll`的签名可以看出，一个`Request`可以用一个`Selection`和一个`Schema`来构造。现在让我们来看看我们的`Selection`。

## 选择

`Selection`类型有两个类型参数:`attributes`和`a`。阅读这种类型的心理模型是“如果给定一个`attributes`的`Schema`，可以选择类型`a`的值。”

```
articleSelection :
    Selection
        { attributes
            | title : Attribute String
        }
        String
articleSelection =
    PG.field .title 
```

Enter fullscreen mode Exit fullscreen mode

如果你和`Json.Decode.field`一起工作过，事情会看起来有点熟悉。这是故意的。总的来说，你会发现`Selection` API 和`Decoder` API 非常相似。让我们检查一下`PG.field`的签名:

```
PG.field : (attributes -> Attribute a) -> Selection attributes a 
```

Enter fullscreen mode Exit fullscreen mode

字段`Selection`由用于`Attribute`的点存取器组成。如果我们回想一下`Selection`的心理模型，我们会想起我们需要一个`Schema`来完成`Selection`。鉴于我们的`articleSelection`的第一个类型参数是`{ attributes | title : Attribute String }`，我们的`Schema`很可能本身就有这个`Attribute` s 的记录，我们来看看吧！

## 图式

理论上，我们可以将任何东西作为第二个参数传递给`PG.schema`函数，但是实际上这个值总是一个关于`Attribute` s.
的 Elm 记录

```
articleSchema :
    Schema x
        { title : Attribute String
        , body : Attribute String
        , favoritesCount : Attribute Int
        }
articleSchema =
    PG.schema "articles"
        { title = PG.string "title"
        , body = PG.string "body"
        , favoritesCount = PG.int "favorites_count"
        } 
```

Enter fullscreen mode Exit fullscreen mode

`PG.schema`获取一个`String`和一个`Attribute`的记录，T1 对应于我们资源的路径(例如:example.com/api/*文章*)。这个`Attribute`的记录描述了一个数据模型的能力。在我们的具体情况下，它描述了我们能够选择什么！

我们来看看`Schema`和`PG.schema`在内部是怎么定义的:

```
type Schema id attributes
    = Schema String attributes

schema : String -> attributes -> Schema id attributes
schema name attrs =
    Schema name attrs 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，我们会发现一个`Schema`只不过是一个对`Attribute`记录的包装。这是真的，但是需要强调的是，它是一个对`Attribute`记录的**不透明的**包装。这可能不是很明显，但是正是这个 API 引导用户将功能描述(`Schema`)与请求构建(`Selection`)分开。用户不能写类似于`PG.field mySchema.title`的东西，因为记录是包装的，用户也不能打开`Schema`因为它是不透明的！他们被迫使用软件包提供的功能来组合东西(即`PG.field`)。这个 API 指导用户根据属性的最终记录编写选择！

*希望前面的解释能解释为什么`PG.field`为`Attribute`取一个点存取器，而不是直接为`Attribute`取一个点存取器。*

在继续之前，让我们一起回顾一下这些类型签名:

```
PG.readAll : Schema id attributes -> Selection attributes a -> Request (List a)

articleSelection :
    Selection
        { attributes
            | title : Attribute String
        }
        String

articleSchema :
    Schema x
        { title : Attribute String
        , body : Attribute String
        , favoritesCount : Attribute Int
        } 
```

Enter fullscreen mode Exit fullscreen mode

花点时间理解这一切。这些片段组合在一起的方式非常酷，我们应该感谢 Elm 的可扩展记录系统！

对于那些好奇的人来说，这里有一个类型为`PG.toHttpRequest : PG.Request -> Http.Request`的函数。从那里你可以转换成带有`Http.toTask`的`Task`或者直接转换成带有`Http.send`的`Cmd`。

# 结论

### 我们达到设计目标了吗？

是啊！在我们的示例中，我们构建了一个请求来读取我们的文章集合资源(模式表示)的所有标题(请求构建器),而不是向`api/articles?select=title` URL(传输协议)发出 HTTP GET 请求并解码 JSON 响应(交换格式)。前者是我们在示例中表达请求的方式，后者是一个实现细节。

### 这个设计给我们买了什么？

1.  **类型安全**
2.  **重用**

#### 类型安全

如果`Schema`有效，我们的`Request`也将有效。我们的`Selection`是用 a `Schema`来定义*的，只有`Schema`和`Selection`静态一致，我们才能构造一个`Request`。换句话说，请求构建错误的子集变成静态错误，而不是逻辑错误。*

例如，假设我们在构造`Selection`时打错了`.title`。如果我们的`Schema`正确地描述了我们的远程资源，我们将得到一个很好的编译器消息。让我们来看看那个错误消息！

```
The definition of `articleSelection` does not match its type annotation.

18| articleSelection :
19|     Selection
20|         { attributes
21|             | title : Attribute String
22|         }
23|         String
24| articleSelection =
25|>    PG.field .titl

The type annotation for `articleSelection` says it is a:

    Selection { attributes | title : ... } String

But the definition (shown above) is a:

    Selection { b | titl : ... } a 
```

Enter fullscreen mode Exit fullscreen mode

相当酷。然而...

细心的读者会认为我们只是将逻辑错误从`Decoder`移到了`Schema`。这是真的，然而，不同的是，我们只有 1 个`Schema`用于整个实体，而不是一个`Decoder`用于我们希望解码实体的每种方式。一个`Schema`代表一个远程资源的所有`Selection`能力的单一真实来源。这又减少了解码逻辑错误的表面积。

所以，综上:如果`Schema`有效，我们的`Request`也将有效。

#### 重用

一个`Selection`可以被重用来构造`Request`与*任何* `Schema`有适当的`Attribute` s！例如，如果我们的远程数据服务器同时拥有文章资源和书籍资源:

```
articleSchema :
    Schema x
        { title : Attribute String
        , body : Attribute String
        , favoritesCount : Attribute Int
        }
articleSchema =
    PG.schema "articles"
        { title = PG.string "title"
        , body = PG.string "body"
        , favoritesCount = PG.int "favorites_count"
        }

bookSchema :
    Schema x
        { title : Attribute String
        , pages : Attribute Int
        , authorName : Attribute String
        }
bookSchema =
    PG.schema "books"
        { title = PG.string "title"
        , pages = PG.int "pages"
        , authorName = PG.string "author_name"
        } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用相同的`Selection` :

```
titleSelection :
    Selection
        { attributes
            | title : Attribute String
        }
        String
titleSelection =
    PG.field .title 
```

Enter fullscreen mode Exit fullscreen mode

构建我们的两个独立请求:

```
getArticles : Request (List String)
getArticles =
    PG.readAll articleSchema titleSelection

getBooks : Request (List String)
getBooks =
    PG.readAll bookSchema titleSelection 
```

Enter fullscreen mode Exit fullscreen mode

相当酷。然而...

老实说，我还没有这个重用特性的需求。尽管如此，它仍然有一些让 API 感觉正确的地方。

因此，总而言之:`Selection` API 中的可扩展记录允许我们重用。

### 哪些想法可以在类似的项目中找到出路？

*   `Schema`作为`Selection`能力的单一事实来源
*   `Schema`和`Selection`的分离
*   可扩展记录是这种分离设计的核心
*   `Selection` API 类似于`Decoder` API
*   更多..我们将在以后的帖子中讨论这些问题

# 未来

出于空间、时间和无聊的考虑，我没有在这篇文章中包括所有的`elm-postgrest`包的 API 设计。将来，我可能会写文章来强调这里遗漏的概念。例如:

*   组合选择
*   模式关系和嵌入选择
*   条件和订单
*   创建、更新和删除

感谢阅读。

*如果你想看一些更简单的例子，这里有一个 github 上[例子的链接。看一下每个单独的 git 提交。](https://github.com/john-kelly/elm-postgrest-example)*

如果你想看一个更“真实”的示例应用，这里有一个到[约翰-凯利/埃尔姆-波斯特-斯帕-示例](https://github.com/john-kelly/elm-postgrest-spa-example)的链接。

*如果你有兴趣了解一下`john-kelly/elm-postgrest`的发展，请前往[开发分部](https://github.com/john-kelly/elm-postgrest/tree/dev)。*