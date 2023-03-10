# 用 Python 写的即时 Rest Api

> 原文：<https://dev.to/asaf_g6/instant-rest-api-written-in-python-hk9>

## 为什么需要即时 Rest API？

*   您正在开发一个还没有服务器的客户端应用程序
*   你正在试用一个新的库，你想看看它是否运行良好
*   您正在准备一个演示，它需要一个简单的服务器，并且不想麻烦自己实际构建一个服务器
*   你正在某个系统上做测试，需要一个存根

如果你需要它，你可以在这里得到它。

## 难道**真的是**瞬间？

基本上，您只需在 rest api 中定义想要的对象，然后启动服务器。如果你真的很快，一分钟之内就能完成。

## 如何使用

首先，克隆存储库:

```
$ git clone https://github.com/asafg6/instant-rest-api.git 
```

Enter fullscreen mode Exit fullscreen mode

我通常使用 virtualenvwrapper，但这是可选的。

如果你没有，想安装，去[这里](https://virtualenvwrapper.readthedocs.io/en/latest/install.html)。
如果有:

```
$  mkvirtualenv -p python3 instant-api 
```

Enter fullscreen mode Exit fullscreen mode

Instant Rest Api 是用 falcon 构建的，所以必须安装它。

安装依赖:

```
$ cd instant-api
$ pip install -r dependencies.txt 
```

Enter fullscreen mode Exit fullscreen mode

假设我们希望我们的模型是“狗”。所以-它的属性是:名字，品种，颜色，年龄。

让我们将新模型添加到 user_models.py 中。

**user_models.py**

```
 """
your models go here

example:

from model import InstantModel

class User(InstantModel):

    name = ''
    phone_number = ''
    address = ''
    age = 0

"""

from model import InstantModel

class Dog(InstantModel):

    name = ''
    breed = ''
    color = ''
    age = 0 
```

Enter fullscreen mode Exit fullscreen mode

运行服务器:

```
$ python 
```

Enter fullscreen mode Exit fullscreen mode

太好了！它在跑！

让我们测试服务器(在另一个终端窗口中):

```
$ curl http://localhost:8000
{"routes": ["/dog"]} 
```

Enter fullscreen mode Exit fullscreen mode

根 url 为我们提供了可能的路由。

我们来列举一下我们的狗:

```
$ curl http://localhost:8000/dog
{"message": null, "body": [], "status": "OK"} 
```

Enter fullscreen mode Exit fullscreen mode

我们还没有狗，让我们创造一些:

```
$ curl -X POST ht&#8203;tp://localhost:8000/dog -H "Content-Type: application/json" -d '{"name": "lucky", "age": 4, "breed": "collie", "color": "brown"}'
{"message": "object created", "body": {"id": "0"}, "status": "OK"}
$ curl -X POST http://localhost:8000/dog -H "Content-Type: application/json" -d '{"name": "rocky", "age": 2, "breed": "jack russel", "color": "white"}'
{"message": "object created", "body": {"id": "1"}, "status": "OK"}
$ curl -X POST http://localhost:8000/dog -H "Content-Type: application/json" -d '{"name": "rex", "age": 7, "breed": "collie", "color": "white"}'
{"message": "object created", "body": {"id": "2"}, "status": "OK"}
$ curl -X POST http://localhost:8000/dog -H "Content-Type: application/json" -d '{"name": "rover", "age": 1, "breed": "rottweiler", "color": "black"}'
{"message": "object created", "body": {"id": "3"}, "status": "OK"} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们的服务器有一些狗。让我们再列出来看看结果(为了方便起见缩进了):

```
$ curl http://localhost:8000/dog
{
  "message": null,
  "body": [
    {
      "age": 1,
      "breed": "rottweiler",
      "id": "3",
      "name": "rover",
      "color": "black"
    },
    {
      "age": 7,
      "breed": "collie",
      "id": "2",
      "name": "rex",
      "color": "white"
    },
    {
      "age": 2,
      "breed": "jack russel",
      "id": "1",
      "name": "rocky",
      "color": "white"
    },
    {
      "age": 4,
      "breed": "collie",
      "id": "0",
      "name": "lucky",
      "color": "brown"
    }
  ],
  "status": "OK"
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错。

我们也可以过滤我们的结果:

```
$ curl http://localhost:8000/dog?color=white
{
  "message": null,
  "body": [
    {
      "age": 7,
      "breed": "collie",
      "id": "2",
      "name": "rex",
      "color": "white"
    },
    {
      "age": 2,
      "breed": "jack russel",
      "id": "1",
      "name": "rocky",
      "color": "white"
    }
  ],
  "status": "OK"
} 
```

Enter fullscreen mode Exit fullscreen mode

或者按 id 获取一个对象:

```
$ curl http://localhost:8000/dog?id=1
{
  "message": null,
  "body": {
    "age": 2,
    "breed": "jack russel",
    "id": "1",
    "name": "rocky",
    "color": "white"
  },
  "status": "OK"
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用排序、desc、限制和偏移。如果一个对象不存在，我们会得到一个错误

```
$ curl http://localhost:8000/dog?id=999
{"message": "Object 999 not found", "body": {}, "status": "ERROR"} 
```

Enter fullscreen mode Exit fullscreen mode

当然我们也可以更新一个对象:

```
$ curl -X PUT http://localhost:8000/dog?id=3 -H "Content-Type: application/json" -d '{"age": 2}'
{
  "message": "Updated",
  "body": {
    "age": 2,
    "breed": "rottweiler",
    "id": "3",
    "name": "rover",
    "color": "black"
  },
  "status": "OK"
} 
```

Enter fullscreen mode Exit fullscreen mode

最后删除一个对象:

```
$ curl -X DELETE http://127.0.0.1:8000/dog?id=3
{"message": "Deleted", "body": {}, "status": "OK"} 
```

Enter fullscreen mode Exit fullscreen mode

## 它是如何工作的？

您可能已经注意到 Dog 类继承自 InstantModel。main 函数列出了 InstanModel 的所有子类，并像这样创建实例:

```
 import user_models
    for name, klass in inspect.getmembers(user_models,
                                          lambda x: inspect.isclass(x) and issubclass(x, InstantModel)):
        if klass == InstantModel:
            continue
        resource_model = klass() 
```

Enter fullscreen mode Exit fullscreen mode

InstantModel 类使用模型的属性来创建通用 CRUD。对象保存到字典中，也写入磁盘。当类初始化时，它读取服务器在过去写入的内容，并将其加载到内存中以备读取。ViewResource 一般构建为与 InstantModel 接口一起工作。

主要功能为每个模型设置一个资源和一条路线，如下:

```
 view = ViewResource(resource_model)
        route = '/%s' % name.lower()
        app.add_route(route, view)
        routes.append(route) 
```

Enter fullscreen mode Exit fullscreen mode

代码非常简单明了，也非常短。如果你想知道更多，请阅读。

## 总结

这个实用程序非常简单，它是一个很好的存根。

欢迎评论和拉取请求。

感谢阅读。