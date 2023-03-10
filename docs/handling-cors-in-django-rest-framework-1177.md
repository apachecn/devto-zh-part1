# 在 Django REST 框架中处理 CORS

> 原文：<https://dev.to/techiediaries/handling-cors-in-django-rest-framework-1177>

如果您正在使用 Django 和现代前端/JavaScript 技术(如 Angular、React 或 Vue)构建应用程序，那么您很可能使用两个开发服务器作为后端服务器(运行在 8000 端口),一个开发服务器(Webpack)作为前端应用程序。

当使用浏览器的 fetch API、Axios 客户端或 jQuery `$.ajax()`方法(JavaScript XHR 接口的包装器)从前端应用程序向使用 Django REST 框架构建的后端 API 发送 HTTP 请求时，web 浏览器将抛出与同源策略相关的错误。

[跨源资源共享或 CORS](https://www.w3.org/TR/cors/) 通过让现代网络浏览器绕过默认实施的相同源策略，允许客户端应用程序与托管在不同域上的 API 接口。

CORS 使您能够添加一组标题，告诉 web 浏览器是否允许发送/接收来自服务该页面的域之外的域的请求。

您可以在 Django REST 框架中启用 CORS，方法是使用定制的中间件，或者使用 [django-cors-headers](https://github.com/ottoyiu/django-cors-headers/) 包

## 使用自定义中间件

首先创建一个 Django 应用程序:

```
python manage.py startapp app 
```

Enter fullscreen mode Exit fullscreen mode

接下来您需要添加一个中间件文件`app/cors.py` :

```
class CorsMiddleware(object):
    def process_response(self, req, resp):
        response["Access-Control-Allow-Origin"] = "*"
        return response 
```

Enter fullscreen mode Exit fullscreen mode

这将为每个 Django 请求添加一个`Access-Control-Allow-Origin:*`头，但是在此之前，您需要将它添加到中间件类列表:

```
MIDDLEWARE_CLASSES = (
    #...
    'app.CorsMiddleware' 
) 
```

Enter fullscreen mode Exit fullscreen mode

就这样，您现在已经在 Django 后端启用了 CORS。您可以配置这个 middlware 来添加更细粒度的选项，或者您可以使用经过良好测试的包`django-cors-headers`,它与 Django REST 框架配合得非常好。

## 使用 django-cors-headers

首先使用 pip
安装 django-cors-headers

```
pip install django-cors-headers 
```

Enter fullscreen mode Exit fullscreen mode

您需要将它添加到您的项目`settings.py`文件:

```
INSTALLED_APPS = (
    ##...
    'corsheaders'
) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要将`corsheaders.middleware.CorsMiddleware`中间件添加到`settings.py`
中的中间件类

```
MIDDLEWARE_CLASSES = (
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.BrokenLinkEmailsMiddleware',
    'django.middleware.common.CommonMiddleware',
    #...
) 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过添加以下设置
为所有域启用 CORS

```
CORS_ORIGIN_ALLOW_ALL = True 
```

Enter fullscreen mode Exit fullscreen mode

或者仅对指定的域启用 CORS:

```
CORS_ORIGIN_ALLOW_ALL = False

CORS_ORIGIN_WHITELIST = (
    'http//:localhost:8000',
) 
```

Enter fullscreen mode Exit fullscreen mode

您可以从文档中找到更多的[配置选项。](https://github.com/ottoyiu/django-cors-headers/#configuration)

## 结论

在本教程中，我们看到了如何使用定制的 CORS 中间件或 django-cors-headers 包在 Django REST 框架后端启用 CORS 头。