# 使用 Heroku 配置变量的 Angular 上的后端 URL 环境变量

> 原文：<https://dev.to/ezequielfalcon/backend-url-env-variable-on-angular-using-heroku-config-vars-16f>

## [T1】简介](#intro)

当我第一次为我的新应用程序使用 Angular(前端)和 Node(后端)时，我对 web 框架的工作方式感到惊讶。这是我第一次使用 Javascript 和 Typescript，我不喜欢 MVC 框架(奇怪哈！)所以我避开了 Ruby on Rails 和 ASP .NET。

当我开始测试我的第一个版本时，一切都很顺利..后来我找到了赫罗库。太棒了..它几乎解决了我所有关于托管和筹备/生产场景的问题..但是它没有解决一件事(起初):我的后端 URL。

该应用程序需要它可以发出 HTTP 请求的 URL！我该如何设置这个变量呢？ **ENV 变量！**

## ENV 变量..他们怎么了？

是的，在你的代码中存储一个配置值..

当使用 Node 时，一切都在服务器端运行，访问 ENV 变量真的很容易:`process.env`。
但当你在客户端(如 Angular)时，这就变得更加困难了。我所有的“配置”和“参数”都存储在后端，所以都是通过 API...但是我只需要做一件事就可以达到那个 API...网址！

## 服务器端/客户端

起初，我并没有很好地理解“服务器端”和“客户端”。我花了几个小时寻找从 Angular 的 typescript 中访问 ENV 变量的方法...当然，你不能，你是在浏览器上运行，而不是在服务器上...服务器只提供应用程序，浏览器做“事情”。

## Heroku 上的  Angular app

你可能知道，如果你想在 heroku 上托管一个 Angular 应用程序，你必须设置一个 http 服务器来托管它。常见的做法是使用托管 Angular 应用程序的小 express 应用程序。下面是代码:

```
// Express app
const express = require('express');
const app = express();
// HTTPS only middleware
const forceSSL = function() { 
    return function(req, res, next) {
        if (req.headers['x-forwarded-proto'] !== 'https') {
            return res.redirect(
                ['https://', req.get('Host'), req.url].join('')
            );
        }
        next();
    }
};
app.use(forceSSL());
// Actual host of the static Angular content
app.use(express.static(__dirname + '/dist'));
app.listen(process.env.PORT || 5000, function() {
    console.log("Angular app running!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这非常简单:一个小小的 angular 应用程序，提供 Angular 应用程序内容(ng build: `dist`文件夹的输出)。

但是现在，我可以利用这个小应用了！！用它作为我自己前端的 API！

## 解！

现在，我已经用后端主机的实际 URL 在 Heroku 中建立了一个名为`BACKEND_URL`的配置变量。让我们将 ENV var 添加到这个 express 应用程序中，并通过 HTTP:
提供服务

```
// Express app
const express = require('express');
const app = express();
// HTTPS only middleware
const forceSSL = function() { 
    return function(req, res, next) {
        if (req.headers['x-forwarded-proto'] !== 'https') {
            return res.redirect(
                ['https://', req.get('Host'), req.url].join('')
            );
        }
        next();
    }
};
app.use(forceSSL());
// Actual host of the static Angular content
app.use(express.static(__dirname + '/dist'));

// Nice and done!
app.get('/backend', (req, res) => {
  res.json({url: process.env.BACKEND_URL})
});

app.listen(process.env.PORT || 5000, function() {
    console.log("Angular app running!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 就是这样...或多或少...

现在，我唯一要做的就是有一个早期的 Angular 服务来挖掘这个变量并保存在我的`sessionStorage`！
例如，登录服务:

```
import { Injectable } from '@angular/core';
import {Http, Headers, Response, URLSearchParams} from '@angular/http';
import 'rxjs/add/operator/map';

@Injectable()
export class LoginService {

  private urlPrefix: string;

  constructor(
    private http: Http
  ) {
    this.http.get(window.location.origin + '/backend').map((response: Response) => response.json()).subscribe(urlBackend => {
      sessionStorage.setItem('url_backend', urlBackend.url);
    }, () => {
      console.log('CanÂ´t find the backend URL, using a failover value');
      sessionStorage.setItem('url_backend', 'https://failover-url.com');
    });
  }

  someLoginMethod() {
  //...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我在`sessionStorage`上有了我的后端 URL。我可以继续登录过程和我的所有数据服务！

这不是最优雅的解决方案，但对我很有效。

关于安全性的说明:正如您所看到的，访问前端 API 的`/backend`路径没有认证。在这种情况下没有必要，因为后端 URL 需要是公共的(它是实际的应用程序 API！).你千万不要用这种方法来分享秘密！

**感谢阅读！**

我注意到:这是一个相当简单的问题，你们大多数人肯定已经解决了，但对我来说，这是一个真正的问题，我没有发现太多。当然，我愿意接受任何建议或改进。这是我的第一篇文章！