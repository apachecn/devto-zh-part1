# 基于消息的 API

> 原文：<https://dev.to/kspeakman/message-based-apis>

REST APIs 似乎已经接管了 dev mindshare。我认为部分原因是它们如何很好地映射到已经理解的概念，如数据库。数据库表是名词，REST 资源是名词。数据库有 4 个操作:插入、选择、更新、删除；REST 有 4 个 HTTP 动词 POST、GET、PUT、DELETE。又称[积垢](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)。

虽然 REST APIs 对于不受信任的客户端来说是直接访问数据库的一个很好的选择，但是对于应用程序 API 来说，它们通常是一个不太合适的选择。试图以这种方式使用它们最终会导致与试图使用数据库作为集成点相同的问题。参见[集成数据库](https://martinfowler.com/bliki/IntegrationDatabase.html)。

> 对于应用程序 API，我指的是不仅仅是数据库上的 CRUD 包装器的 API。除了检查空白字符串之外，还涉及一些业务逻辑。

应用程序 API 经常需要处理跨越多个“资源”的用例或用户故事。与集成数据库非常相似，当您在 REST 中对其建模时，客户端必须知道要接触的所有资源，并在此过程中处理故障。换句话说，客户负责用例处理。在实践中，我经常看到服务器上的“REST”操作影响其他资源并产生其他副作用，因为让客户端管理这些太不方便了。问题是现在手术有不明显的副作用。比如触发一封邮件。它使得原本应该是通用的操作实际上不能再用于其他目的。

***好吧，够背景。*T3】**

为了避免这些问题，我一直在开发基于消息的 API。在这种 API 中，客户端只需构造一个消息(通常是一个序列化为类似 JSON 的网络格式的类)，并将其发送到 API 端点。它类似于 RPC，除了参数被打包成一个一级概念——一条消息——它代表一个用例。HTTP 消息 API 端点可能如下所示。

```
/api/{message_type} 
```

Enter fullscreen mode Exit fullscreen mode

`{message_type}`通常类似于类名。调用这个 API 的一些可能的例子。

```
POST /api/Register
POST /api/BulkRegister
POST /api/CancelRegistration
POST /api/StartCourseMaterial
POST /api/GradeExam

GET /api/SearchRegistrations
GET /api/SearchCompletions 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，每条消息都表达了其意图。在服务器上，我从 URL 中读取消息的名称。使用这个名字，我查找 a)如何将请求解析为对象，b)处理消息并生成响应的函数。处理函数可以读取多个实体或其他 API。它也可以写信到多个地方，发送电子邮件等。完成用例所需的一切。由于这不是直接将数据库表公开为端点，*基于消息的 API 可以与后端数据结构完全解耦*。

> 随着项目的增长，消息作为一级概念还会带来一些有趣的可伸缩性选项。例如，消息可以排队或路由到其他节点。

在这个设置中，权限也相当不错。从请求 URL 中逐字读取消息的名称，查看该项是否存在于用户的权限列表中。由于这些操作是特定于业务逻辑的，而不是特定于数据库操作的，因此它们完全符合业务需求。例如，企业可能希望教师能够创建课程，但不能设置某些管理参数。我可以为带有所有选项的 CreateCourse 和带有有限选项的 CreateLimitedCourse 创建一个消息。反正我得给这些不同的情况写代码！

> 遵循 [CQS 原则](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation)，我也倾向于将旨在产生副作用的消息归类为命令，将请求数据的消息归类为查询。但这只是对消息传递的进一步细化。

顺便说一下，没有什么可以阻止您对 CRUD 操作使用 REST 端点，对行为操作使用基于消息的端点。

*在[第二部分](https://dev.to/kspeakman/message-based-api-part-2-67c)中深入探讨信息传递。*