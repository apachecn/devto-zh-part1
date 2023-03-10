# 用 API 蓝图记录您的 API

> 原文：<https://dev.to/albertofdzm/documenting-your-api-with-api-blueprint-e0m>

本文介绍了如何使用 API 蓝图标准为将来的客户提供 API 使用信息。在开始开发之前，设计未来 API 端点的结构也很有帮助。

***注:**这是关于**用 Express** 构建 API 系列文章的第 4 篇。基于我上一篇关于[**mongose、MongoDB 和 Express**](http://onlythepixel.com/2017/01/05/mongoose-mongodb-and-express/) 的帖子，我将继续开发[生成的代码](https://github.com/AlbertoFdzM/another-todo-api/tree/post/03)。*

天哪，自从我上次提交以来已经过去了很长时间，现在当我看 API 时，有一些端点我已经不太记得了，因为我上次开发了它们。现在让我们通过使用 [API 蓝图语法](https://apiblueprint.org/) 用**记录 API 来修复它。**

API Blueprint 是基于**[【MSON】](https://apiblueprint.org/documentation/mson/tutorial.html)**的一个非常基本的标准，由于其编写相对简单，因此在 API 的生命周期中很容易维护。

## 记录正确

我将在我的项目中创建一个名为`docs`的新文件夹来存储关于我的 TODO API 和“*的所有文档，这是为什么呢？和你的代码在同一个仓库里？*“你可能会问，这是因为我认为**docs 文件应该尽可能接近你的代码**，以确保它们由开发 API 的人更新和维护。还因为我可以用它们来用一些好的工具对照文档测试 API。

让我们停止喋喋不休，开始编码吧！

## 定义 API

首先，我将定义 API 主信息:

### `/docs/main.md`

```
FORMAT: 1A

# Another TODO

Another TODO API is that, another TODO API. 
```

Enter fullscreen mode Exit fullscreen mode

在第一行，我设置了文档使用的格式(1A ),我的 API 的名称和一个简短的描述。这很容易，在 API 通用描述之后，就可以定义 API 资源了。

#### API 资源

```
FORMAT: 1A

...

# Group Tasks

Resources related to the tasks in the API.

## Tasks Collection [/tasks] 
```

Enter fullscreen mode Exit fullscreen mode

***注:**注意省略号`...`为排除代码。*

现在这里有几个**关键字，它们将定义我们的 API 模式**，第一个是`Group`，它定义一个部分来对一些资源进行分类，另一个是第二个头末尾的`[/tasks]`定义，它定义一个端点。现在开始行动。

#### 动作

```
...

### List All Tasks [GET]

+ Response 200 (application/json)

        [
          {
            "__v": 0,
            "updatedAt": "2017-04-09T16:15:37.066Z",
            "createdAt": "2017-04-09T16:15:37.066Z",
            "_id": "586e88217106b038d820a54e",
            "isDone": false,
            "description": "test"
          },
          {
            "__v": 0,
            "updatedAt": "2017-04-09T16:15:37.067Z",
            "createdAt": "2017-04-09T16:15:37.067Z",
            "_id": "586e88337106b038d820a54f",
            "isDone": false,
            "description": "test"
          }
        ] 
```

Enter fullscreen mode Exit fullscreen mode

这一行发生了很多事情，首先在标题中有一个关键字`[GET]`指示请求的动词，然后是一个**响应定义**(它们必须以`+`、`*`或`-`开头，就像一个降价列表项)，后面是状态代码`200`(强制)和响应`Content-Type`、`application/json`(可选)，最后是一个响应体示例(示例对象必须缩进 8 个空格或 2 个制表符)。

那`POST`呢？

```
...

### Create a New Task [POST]

+ Attributes
    + description: `Buy milk` (string) - Task description
    + isDone: false (boolean, required) - Done status

+ Request (application/json)

        {
          "description": "Buy milk",
          "isDone": false
        }

+ Response 201 (application/json)

        {
          "__v": 0,
          "updatedAt": "2017-04-09T16:30:42.010Z",
          "createdAt": "2017-04-09T16:30:42.010Z",
          "_id": "586e88337106b038d820a54f",
          "isDone": false,
          "description": "Buy milk"
        } 
```

Enter fullscreen mode Exit fullscreen mode

与`GET`非常相似，但是这次我也指定了`Request`和`Attributes`的定义。

在`Attributes`中，有一个表示请求体可能属性的项目列表，每个项目都有适当的名称、示例值、类型(如果需要)和简短的描述。

在`Request`中，我定义了一个请求体对象的例子。

是时候对付 URI·帕拉斯了

#### URI Params

对于必须通过用 params(在我的例子中是`/tasks/:id`)攻击 URL 来访问的资源，有一种方法可以定义它们:

```
## Task [/tasks/{taskId}]

+ Parameters

  + taskId: `586e88337106b038d820a54f` (string)

### View a Task [GET]

+ Response 200 (application/json)

        {
          "__v": 0,
          "updatedAt": "2017-04-09T16:30:42.010Z",
          "createdAt": "2017-04-09T16:30:42.010Z",
          "_id": "586e88337106b038d820a54f",
          "isDone": false,
          "description": "Buy milk"
        } 
```

Enter fullscreen mode Exit fullscreen mode

URL 中用大括号`{taskId}`定义的属性将与`Parameters`部分中同名的定义相关。`Parameters`部分使用与`Attributes`相同的术语。

#### 数据结构

在每个部分定义相同的响应有点苛刻，因此您可以在文档中定义一个`Data Structures`部分来存储一些基本的数据结构。

```
## Data Structures

### Task

+ _id: `586e88337106b038d820a54f` (string, required) - Task's ID
+ description: `Buy more milk` (string, required) - Task's description
+ isDone: false (boolean, required) - Done status
+ createdAt: `2017-04-09T16:30:42.010Z` (string, required) - Task's created date
+ updatedAt: `2017-04-09T16:30:42.010Z` (string, required) - Task's update date 
```

Enter fullscreen mode Exit fullscreen mode

然后在端点定义中重用它们。

```
### Edit a Task partially [PATCH]

+ Attributes
    + description: `Buy more milk` (string) - Task description
    + isDone: true (boolean) - Done status

+ Request (application/json)

        {
          "description": "Buy more milk"
        }

+ Response 200 (application/json)

        + Attributes (Task) 
```

Enter fullscreen mode Exit fullscreen mode

在这里的`Response`定义中，我使用了我的`Attributes`数据结构。

## 就是这样！

我想我已经涵盖了所有的基本情况，你可以阅读这个关于 API 蓝图的高级教程，并定义一个更加健壮的 API。

像往常一样，你可以在 GitHub 上[检查代码，你可以在养蜂场](https://github.com/AlbertoFdzM/another-todo-api/tree/post/04)查看[的另一个待办事项 API 文档](http://docs.anothertodo.apiary.io/)