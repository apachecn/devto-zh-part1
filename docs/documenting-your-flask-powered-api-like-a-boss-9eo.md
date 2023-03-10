# 像老板一样记录你的烧瓶驱动的 API

> 原文：<https://dev.to/djiit/documenting-your-flask-powered-api-like-a-boss-9eo>

Flask 是构建 web 应用程序的一个非常流行和强大的框架。在过去的几年里，人们用它来创建 REST API，它可以很好地与解耦的现代前端应用程序一起工作。

后端开发团队经常面临的一个挑战是如何让前端开发人员(无论是内部人员还是远程社区人员)轻松创建符合 API 的客户端(web 应用程序、移动应用程序甚至 CLI 工具...)

在野外，它们是许多记录良好的 API 的好例子...以 Twitter API 为例:文档非常棒，用户友好，包含了所有可用的端点和提示及示例。任何一个新的 CS 学生都可以使用它编写一个小的 Python 工具，只需要遵循文档和它的例子。

在@Ooreka，我们决定遵循 [OpenAPI](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) (fka Swagger 2.0)规范，为我们的 Flask 驱动的微服务 API 构建一个可靠的文档。让我们开始吧。

## 3..2..一..医生。

多亏了 [apispec](http://apispec.readthedocs.io/en/latest/) 库，你可以从你的 Flask 代码自动生成一个规范文件(通常命名为`swagger.json`)。其他一些库可以为你做很多神奇的事情，但是`apispec`使用起来非常简单，可以放在你的代码旁边而不会干扰它。

它支持[棉花糖和烧瓶](http://apispec.readthedocs.io/en/latest/using_plugins.html)，允许你重用你的代码来生成一个合适的文档！

让我们编写我们的生成脚本，例如`app/scripts/openapi.py` :

```
from apispec import APISpec

# Create spec spec = APISpec(
    title='My Awesome API',
    version='1.0.42',
    info=dict(
        description='You know, for devs'
    ),
    plugins=[
        'apispec.ext.flask',
        'apispec.ext.marshmallow'
    ]
)

# Reference your schemas definitions from app.schemas import FooSchema

spec.definition('Foo', schema=FooSchema)
# ... 
# Now, reference your routes. from app.views import my_route

# We need a working context for apispec introspection. app = create_app()

with app.test_request_context():
    spec.add_path(view=my_route)
    # ... 
# We're good to go! Save this to a file for now. with open('swagger.json', 'w') as f:
    json.dump(spec.to_dict(), f) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们首先创建一个新的 APISpec 实例，其中包含一些关于我们的 API 的细节。
然后，我们用`APISpec.definition()`添加我们的定义(这里，我们使用棉花糖来定义我们的 API 将如何序列化/反序列化数据)。
最后，我们使用`APISpec.add_path()`将路线添加到我们的 API 规范中。`apispec`将解析您的路由函数文档字符串，所以请确保您在这里添加了一些 OpenAPI YaML 内容，如:

```
@app.route('/foo/<bar_id>')
def my_route(gist_id):
    """ Cool Foo-Bar route.
    - - - # dev.to editor dislike triple hyphen, be sure to remove spaces here.
    get:
        summary: Foo-Bar endpoint.
        description: Get a single foo with the bar ID.
        parameters:
            - name: bar_id
              in: path
              description: Bar ID
              type: integer
              required: true
        responses:
            200:
                description: Foo object to be returned.
                schema: FooSchema
            404:
                description: Foo not found.
    """
    # (...)
    return jsonify(foo) 
```

Enter fullscreen mode Exit fullscreen mode

您将最终得到一个有效的 JSON API 规范。现在，让我们看看如何引导一个 HTML 版本向世界展示它！

## 浏览这一切

一个非常酷的工具是来自[API . guru](https://apis.guru/)的家伙的 [ReDoc](https://github.com/Rebilly/ReDoc) Javascript 库。我们将使用它以一种方便的方式呈现生成的 JSON 规范。

Redoc 基本上是一个简单的缩小的 JS 文件，你可以将它包含在一个裸的`index.html`文件中，并告诉它你的`swagger.json`在哪里。它使用了一个非常简洁的 3 栏设计:一个导航侧栏，一个包含 API 端点定义的中间部分，第三栏专门用于请求或响应示例。

```
<!DOCTYPE html>
<html>
  <head>
    Cool API Documentation
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style> body { margin: 0; padding: 0; } </style>
  </head>
  <body>
    <redoc spec-url='./swagger.json' hide-loading></redoc>
    <script src="https://rebilly.github.io/ReDoc/releases/latest/redoc.min.js"> </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

是啊，真快。点击查看真实世界的例子[。](https://rebilly.github.io/RebillyAPI/)

## 包装完毕

OpenAPI 提供了许多选项，为了简明起见，我没有在这里介绍。您可以将您的服务器的真实端点添加到 doc 中，添加关于您的路由的参数和响应的许多细节，在您的路由函数 docstring 中提供将被解析并添加到您的规范中的示例，等等...

作为最后一个技巧，阅读一下 [Flask CLI](http://flask.pocoo.org/docs/0.12/cli/) 文档，看看如何轻松地将您的生成脚本挂接到 Flask 的命令行界面(这将为您提供一些像`FLASK_APP=main.py flask generate_doc`这样的强大命令)。哦，一定要把这个放到你的持续集成例程中，以使你的 API 文档与你的 API 保持同步！

干杯！

*注:本帖最初发布于[媒体](https://becominghuman.ai/documenting-your-flask-powered-api-like-a-boss-b423a7c0f45e)。*