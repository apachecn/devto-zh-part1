# 学习 Node.js 构建一个简单的 Express API -第二部分

> 原文：<https://dev.to/filipedomingues/learning-nodejs-building-a-simple-express-api---part-ii>

又见面了。在我们开始之前，我想对第一部分的所有反馈表示深深的感谢，希望你也喜欢这一部分。你可以在这里阅读第一部分[，在我的 github](https://dev.to/filipedomingues/learning-nodejs-building-a-simple-express-api-part-i---project-setup)[@ filipodominguesgit](https://github.com/FilipeDominguesGit/fd-node-todo-api)上阅读所有部分的解决方案。

在这一部分，我将主要关注`routes`、`REST`架构以及如何在`Express`项目中利用它。我现在不会过多关注每个路由逻辑，所以请记住会有一些错误和遗漏的验证。我们现在将使用内存模拟数据库，下一部分我们将开始使用`MongoDB`，因为你们都投票支持它。

* * *

# **休息**

因此，在我们开始黑客攻击之前，让我们谈一谈 REST 和我们将在项目中使用的一些基本原则。我不会在这里说太多的细节，所以请随意在评论中提出一些问题。

REST(**Re**presentational**S**tate**T**transfer)是罗伊·菲尔丁在他 2000 年的博士论文中定义的一种建筑风格。这种架构并不局限于`HTTP`,但它通常与 it 相关联。实现 REST 架构的 HTTP web 服务被称为 RESTful web 服务。考虑到这一点，我们来谈谈 RESTful 服务的一些原则和架构约束。

### **资源型**

REST 是一个基于资源的架构，与经典的 RCP web 服务不同，**关注资源**而不是动作。例如:

| RPC API(动词) | REST API(名词) |
| --- | --- |
| [www.example.com/api/createUser](http://www.example.com/api/createUser) | [www.example.com/api/Users](http://www.example.com/api/Users) |
| [www.example.com/api/getTodos](http://www.example.com/api/getTodos) | [www.example.com/api/Todos](http://www.example.com/api/Todos) |

每个资源都应该有一个标识符，这样它就可以被它的 URI 访问。比如:
[www.example.com/api/todos/1](http://www.example.com/api/todos/1)
[www.example.com/api/users/1337](http://www.example.com/api/users/1337)

### **统一界面**

使用 HTTP 协议作为我们的服务器-客户端通信接口，使我们的架构解耦和简化。在 API 请求**上，我们应该使用`HTTP verbs`** 来赋予它们意义。例如:

*   `GET` -读取特定资源或资源集合。
*   `PUT` -更新特定资源或资源集合。如果资源标识符已知，也可用于创建资源。
*   `DELETE` -通过标识符删除资源。
*   `POST` -创建一个新的资源，用于不适合其他动词的操作。

在我们的 API 响应中，我们应该总是**使用正确的`HTTP status codes`** 。最常用的有:

*   2xx 表示成功响应。
*   4xx 表示请求错误(未经验证的请求、缺少参数、未找到请求的资源等)..)
*   服务器错误 5xx。

### **无拘无束地交流**

请求应该有足够的信息，服务器应该能够处理它，而不需要保持状态。如果您需要保存任何类型的状态，请将其保存在客户端或作为服务器端资源。这将使它更容易扩展，这样服务器端的变化将不会影响客户端。

### **多重表象**

您的资源应该独立于它们的表示，因此您应该能够提供同一资源的多种表示(xml、json、csv 等..).使用 HTTP 头`Accept`和`Content-Type`我们可以很容易地做到这一点。这个机制是在 HTTP RFC T4 上定义的，它叫做 T2。

### **链接资源**

您可以并且应该将您的资源与其子资源和可能的操作联系起来。它简化了客户端导航和发现您的 API 的方式。这被称为`Hypermedia as the Engine of Application State`或`HATEOAS`。比如:

```
{  "content":  [{  "amout":  "500",  "orderId":  "123",  "_links":{  "_rel":  "self",  "href":  "/orders/123"  }  }],  "_links":  [{  "_rel":  "order.product",  "href":  "/products/1"  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

我将在以后的博客中发布`HATEOAS`,所以现在不要太担心。

请记住，这是一个非常简单的 REST 定义，但是应该可以帮助您开始阅读这篇文章。现在让我们开始编码我们的路线！

* * *

# **航线**

让我们首先在项目`src`上创建一个名为`routes`的新目录和一个`home.js`文件。在这个文件中，我们将为我们的主路径定义如下的处理程序:

```
// src/routes/home.js

const express = require('express');

// create router
const router = express.Router();

// GET http://localhost:3001/ 
router.get('/',(req,res) => {
  res.send('Hello Dev.to!');
});

module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的，对吧？我们只是创建一个路由器对象来管理我们的路由，并为`GET /`请求添加一个处理程序。

如果您是新手，箭头函数符号可能会有点棘手。为了更清楚起见:

```
const getHandler = function(request,response){
  response.send('Hello Dev.to!');
};

router.get('/',getHandler); 
```

Enter fullscreen mode Exit fullscreen mode

现在，要将这条路线添加到我们的 Api 中，让我们首先在我们的`routes`目录中创建一个`index.js`文件，并添加以下代码:

```
// src/routes/index.js

const express = require('express');
const router = express.Router();

const homeRoute = require('./home');

router.use('/', homeRoute);

module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用这个`index.js`文件使导入其他路径变得简单和干净。

好了，现在我们只差一步了。在`app.js`文件中，我们需要导入我们的路线，并将它们添加到我们的 express 服务器中。

```
// src/app.js

...

const routes = require('./routes'); 
app.use(routes);

... 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们来测试一下！只需在命令行输入`npm start`启动服务器，在 [http://localhost:3001/](http://localhost:3001/) 上打开浏览器。
如果一切顺利，你应该会在浏览器上看到消息 **`Hello Dev.to!`** ！

现在我们知道了如何设置路线，让我们开始实现我们的`todos route`。在`src/routes`上创建一个`api`目录，并添加一个`todos.js`文件。

让我们从列出所有待办事项开始。

```
// src/routes/api/todos.js

const express = require('express');
const router = express.Router();

const inMemoryTodoDB = [
    {id:0,name:'Part I',description:'Write Part I', done:true},
    {id:1,name:'Part II',description:'Write Part II', done:false}
];

router.get('/',(req,res)=>{
  res.status(200)
    .json(inMemoryTodoDB);
});

module.exports = router; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有内存模拟数据库`inMemoryTodoDB`和用于`/api/todos/`请求的`GET`处理程序。这次唯一的不同是我们的响应，我们现在发送一个表示成功的`200 http status code`响应和一个 json 对象形式的 todos 列表。
容易吧？

让我们将这条路线添加到`src\routes\index.js`文件中，这样我们就可以测试它了。

```
// src/routes/index.js
...
  const homeRoute = require('./home');
  const todosRoute = require('./api/todos');

  router.use('/', homeRoute);
  router.use('/api/todos', todosRoute);
... 
```

Enter fullscreen mode Exit fullscreen mode

非常简单明了。
我们现在可以像往常一样启动服务器并在[http://localhost:3001/API/todos](http://localhost:3001/api/todos)上打开浏览器来测试我们刚刚创建的路由。您应该会看到一个包含所有待办事项的`json`对象。
现在让我们添加一条路线，这样我们就可以得到一个具体的待办事项！让我们添加`GET /api/todos/:id`路线。

```
// src/routes/api/todos.js

router.get('/:id',(req,res)=>{

  const { id } = req.params;

  const todoItem = inMemoryTodoDB.filter((todo)=> todo.id==id)[0];

  if(!todoItem){
    res.sendStatus(404);
  }
  else{
    res.status(200).json(todoItem);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

正如您现在看到的，我们正在 uri 上传递`id`。我们可以在 req.params 对象上访问它。我在这里用了一点`Object destructuring`让它更干净。

```
// this:
const { id } = req.params;
// is the same as this:
const id = req.params.id; 
```

Enter fullscreen mode Exit fullscreen mode

***接下来几天我可能会在 javascript one 里做一个关于`destructuring`的帖子。***
现在我们有了`id`，我们将尝试使用`Array.filter`在模拟数据库中找到它。(*如果你对过滤器有任何疑问，请在评论中告诉我。* )
这一次我们的回应将取决于我们能否找到物品。如果我们找到了 todo 项，我们可以像以前一样，将它作为一个 json 对象和一个 200 状态代码发送回去。如果我们没有找到给定`id`的项目，我们将发送一个`404 Not Found`。

现在我们可以列出所有的待办事项并获得一个特定的待办事项，让我们创建一个！

```
// src/routes/api/todos.js

router.post('/',(req,res)=>{

  const { name,description,done } = req.body;

  // getting last used id from our Mock DB 
  const lastId = inMemoryTodoDB[inMemoryTodoDB.length-1].id;
  const id = lastId + 1;

  const newTodo = { id,name,description,done };

  inMemoryTodoDB.push(newTodo);

  res.status(201)
    .location(`/api/todos/${id}`)
    .json(newTodo);

}); 
```

Enter fullscreen mode Exit fullscreen mode

所以我们这里有很多新东西！
我们现在使用`POST`而不是`GET`，这允许我们在请求体上发送数据。
这次我得到了我们需要的信息，从请求体(`req.body`)而不是`req.params`中创建一个新的`todo`。
现在，在响应中，我们发送一个 HTTP 状态代码`201 created`，表示我们已经成功创建了一个新资源，我们添加了带有新资源端点的位置头，最后我们将新资源作为 Json 对象返回。

现在，在我们测试这个路由之前，我们需要添加一个`Express`中间件，它将解析请求体并使其在`req.body`属性下可用。
让我们首先安装依赖项:

```
npm i body-parser --save 
```

Enter fullscreen mode Exit fullscreen mode

并在`src\app.js`上加上，像这样:

```
// src/app.js

const express = require('express');

// Import body-parser
const bodyParser = require('body-parser');

const port = process.env.PORT ||  3001;

const app = express();
// add body-parser middleware
app.use(bodyParser.json());
... 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以启动服务器，并使用[邮差](https://www.getpostman.com/)或者像这样使用`Curl`:
来测试它

```
curl -XPOST -H "Content-type: application/json" -d '{"name":"todo 3","description":"description here 3", "done":false}' 'http://localhost:3001/api/todos/' 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们现在可以添加新的任务了！

现在让我们添加我们的删除路径:

```
// src/routes/api/todos.js
router.delete('/:id',(req,res)=>{

  const {id} = req.params;

  const todoItem = inMemoryTodoDB.filter((todo)=> todo.id==id)[0];

  if(!todoItem)
  {
    res.sendStatus(404);
    return;
  }
  inMemoryTodoDB.splice(inMemoryTodoDB.indexOf((todo)=>todo.id==id),1);

  res.sendStatus(200);

}); 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么新的东西，我们只是在找到时移除`todo`或者在没有找到时返回`404 Not Found`。如果你对这条路线有任何疑问，请告诉我。

现在让我们添加一条路线，将我们的 todo 任务设置为完成或未完成:

```
router.put('/:id/done',(req,res)=>{

  let  { done }  = req.body;
  const {id} = req.params;

  // check if its a boolean 
  if(typeof(done) != 'boolean' )
  {
    res.sendStatus(400);
    return;
  }

  const exists = inMemoryTodoDB.filter((todo)=> todo.id==id).length > 0;    

  if(!exists){
    res.sendStatus(404);
    return;
  }

  inMemoryTodoDB.map((todo)=>{
    if(todo.id == id) {
      todo.done = done;
    }
  });

  res.sendStatus(200);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一不同的是对输入的布尔类型检查:

```
 if(typeof(done) != 'boolean' )
  {
    res.sendStatus(400);
    return;
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果客户端发送一个非布尔值，我们用一个`400 Bad Request`来回复，表明请求有问题。如果输入是有效的，我们可以用给定的`id`找到一个 todo，我们只需更新它的值并用一个`200 OK`回复。

* * *

### **总结**

那么我们今天学到了什么？

*   基本休息原则
*   如何设置基本路线
*   如何使用`HTTP verbs`来表达我们的请求
*   如何使用`HTTP status`代码来表示我们的响应状态

我们的 API 是这样的:

| 动词 | 途径 |  |
| --- | --- | --- |
| 得到 | api/todos | 列出所有 todos 集合 |
| 得到 | api/todos/:id | 使用给定的`:id`返回 todo 任务的表示 |
| 邮政 | api/todos | 向集合中添加新的 todo |
| 放 | API/全部/:id/done | 用给定的`:id`更新 todo 任务的`done`属性值 |
| 删除 | api/todos/:id | 删除给定`:id`的待办任务 |

我将*内容协商*、*超媒体*和*版本控制*从这一部分中去掉，因为我想更详细地探讨这个主题。

今天就这样了。在下一部分，我将开始实现我们的数据库模块，所以如果你想你可以开始安装`MongoDB`。你可以在我的 Github 库[@ filipodominguesgit](https://github.com/FilipeDominguesGit/fd-node-todo-api)上查看我对这部分的解决方案。

* * *

欢迎留下您的反馈或建议！我仍然是博客发布的新手，所以欢迎所有的帮助！