# ★在 Laravel 应用程序中处理 CORS

> 原文：<https://dev.to/freekmurze/handling-cors-in-a-laravel-application-4o4g>

最近我们发布了 [laravel-cors](https://github.com/spatie/laravel-cors) 。这个包可以为你的 Laravel 应用添加必要的 CORS 头文件。在这篇文章中，我想简单解释一下什么是 CORS，以及如何使用这个软件包。

## 什么是 CORS

想象一下，在浏览器中运行的域 X 的所有 JavaScript 代码都能够向域 Y 发出 http 请求，域 X 上的恶意代码能够在您不知情的情况下与站点 Y 进行交互。在大多数情况下，你不希望这样。幸运的是，所有主流浏览器都只允许网站对自己的域名提出请求。他们不允许 JavaScript 代码对不同域上的站点发出请求。这叫做[同源政策](https://www.w3.org/Security/wiki/Same_Origin_Policy)。

但在某些情况下，你确实希望允许这种行为。想象一个运行在域 X 上的 API，你想通过运行在域 y 上的 JavaScript 来使用它。CORS 代表[跨源资源共享](https://www.w3.org/TR/cors/)。这是合法地戳穿同源政策漏洞的标准化方式。

## 简单的请求

当运行在域 X 上的 JavaScript 对域 Y 执行一个`HEAD` `GET`或某个`POST`请求(带有`application/x-www-form-urlencoded`、`multipart/form-data`或`text/plain`)时，浏览器会添加一个`Origin`头。在域 Y 上运行的应用程序可以使用这个头来检查请求是否被允许。如果服务器用包含域 X 的头`Access-Control-Allow-Origin`响应，那么浏览器将断定请求被允许。如果服务器不这样做，大多数浏览器将不允许域 X 上的 JS 对域 y 执行任何请求。

## 所有其他请求

前一节所涉及的所有请求可能只用于检索一些数据。诸如某些`POST`请求、`PUT`、`PATCH`、`DELETE`之类的所有其他请求可能会修改服务器上的现有数据。对于这些类型的请求，浏览器会在执行实际请求之前发送一个预检请求。

这个预检请求使用了`OPTIONS`动词。浏览器还会发送上一节中提到的 Origin 头。服务器应该返回一个设置了`Access-Control-Allow-Origin`、`Access-Control-Allow-Methods`和`Access-Control-Max-Age headers`的响应。`Access-Control-Allow-Methods`包含允许的 HTTP 动词。`Access-Control-Max-Age`包含不应发送新的预检请求的时间(秒)。

## 使用 spatie/laravel-cors

我们的 [spatie/laravel-cors](https://github.com/spatie/laravel-cors) 包可以为您处理验证和设置所有需要的头。这个包可以通过 Composer
安装

```
composer require spatie/laravel-cors 
```

之后你必须注册`Cors`中间件

```
// app/Http/Kernel.php

protected $middleware = [
    ...
    \Spatie\Cors\Cors::class
]; 
```

您的应用程序现在将允许来自所有域的跨来源请求。您可能希望发布配置文件，并将`allow_origins`键设置为您将允许请求的所有域。下面是该配置文件的默认内容。

```
return [
    /*
     * A cors profile determines which orgins, methods, headers are allowed for
     * a given requests. The `DefaultProfile` reads its configuration from this
     * config file.
     *
     * You can easily create your own cors profile.
     * More info: https://github.com/spatie/laravel-cors/#creating-your-own-cors-profile
     */
    'cors_profile' => Spatie\Cors\CorsProfile\DefaultProfile::class,

    /*
     * This configuration is used by `DefaultProfile`.
     */
    'default_profile' => [

        'allow_origins' => [
            '*',
        ],

        'allow_methods' => [
            'POST',
            'GET',
            'OPTIONS',
            'PUT',
            'PATCH',
            'DELETE',
        ],

        'allow_headers' => [
            'Content-Type',
            'X-Auth-Token',
            'Origin',
            'Authorization',
        ],

        /*
         * Preflight request will respond with value for the max age header.
         */
        'max_age' => 60 * 60 * 24,
    ],
]; 
```

## 使用 CORS 个人资料

假设您想要基于当前登录的用户指定允许的来源。在这种情况下，只读取配置文件的`DefaultProfile`不会剪切它。幸运的是，写你自己的 CORS 简介很容易。有效的 CORS 配置文件是任何扩展了`Spatie\Cors\DefaultProfile`的类。

这里有一个简单的例子，假设您已经在您的用户模型上添加了一个`allowed_domains`列:

```
namespace App\Services\Cors;

use Spatie\Cors\DefaultProfile;

class UserBasedCorsProfile extends DefaultProfile;
{
    public function allowOrigins(): array
    {
        return Auth::user()->allowed_domains;
    }
} 
```

不要忘记在配置文件中注册您的概要文件。

```
// config/cors.php

 ...
 'cors_profile' => App\Services\Cors\UserBasedCorsProfile::class,
 ... 
```

在上面的例子中，我们已经覆盖了`allowOrigins`方法，但是当然你可以选择覆盖`DefaultProfile`中的任何方法。

## 在关闭

看起来，整个 CORS 事件都是为了保护你的服务器，但事实并非如此。它的主要目的是通过不向不需要的其他域发出任何请求来保护浏览器的用户。不要将 CORS 视为保护服务器的机制。

如果您想了解更多关于我们的 laravel-cors 包的信息，请访问 GitHub。你也可以选择使用巴里 vd。霍维尔的 CORS 套餐。我们的包装是对 Barry 的优秀产品 T1 的基本特征的现代改写。我们创建了自己的解决方案，因为我们需要我们的配置[非常灵活](https://github.com/spatie/laravel-cors#creating-your-own-cors-profile)。

请务必查看我们团队之前制作的包。那里可能有一些对你的下一个项目有用的东西。