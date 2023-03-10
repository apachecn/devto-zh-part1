# Django 开发服务器的域名

> 原文：<https://dev.to/djangotricks/domain-name-for-django-development-server>

[![Domain Name for Django Development Server](img/f5e4ed011a0b125be5c9bd2997e27eaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aLsHNFC2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-zm4go5DZdwA/WWKsoU3scII/AAAAAAAABzk/k0LsaAsY2ncVmU-AJh6R-0oX5dCkIwEkwCLcBGAs/s1600/domain-name-for-django-development-server.png)

浏览网页时，你通常通过域名访问网站，然而，在开发 Django 网站时，你通常通过 IP 地址访问它，这难道不奇怪吗？通过域名浏览你的本地网站不是很方便吗？让我们看看通过域名访问本地开发服务器有哪些可能性。

## 通过 IP 地址访问

你大概从用 Django 开发的第一天就把下面这句台词背得滚瓜烂熟，闭着眼睛都能打出来？

```
(myenv)$ python manage.py runserver 
```

Enter fullscreen mode Exit fullscreen mode

当您运行管理命令`runserver`时，它会启动一个轻量级 Django 开发服务器，默认情况下，该服务器会监听本地机器端口`8000`上的 HTTP 请求，而默认情况下，HTTP 网站运行在`80`上，HTTPS 网站运行在`443`上。在浏览器中输入`http://127.0.0.1:8000`,你可以点击你的 Django 项目。

请注意，这是一个本地地址，不能从网络中的其它设备访问。从他们的计算机访问相同地址的其他人将会看到他们自己的计算机上的 web 服务器所提供的内容，如果有任何 web 服务器在那里运行的话。

本地网络中的每台设备都有自己的互联网协议(IP)地址。IP 地址有两种版本:IPv4，通常由 4 个用点分隔的十进制数字组成(例如`197.160.2.1`)，IPv6，由用冒号分隔的十六进制数字组成(例如`[fe80::200:f8ff:fe21:67cf]`)。IP 地址可以自动设置，并在您连接到网络时动态生成，您也可以手动设置并使其成为静态的。例如，网络中的打印机通常有一个静态地址，而手机或平板电脑则有一个动态附加的 IP 地址。

如果您想要从网络中的其他设备访问您电脑上的响应网站，我建议您在网络设置中手动设置 IP 地址。拥有一个每次连接到同一个网络时都不会改变的地址要方便得多——您可以将它标记为书签，或者在不同的配置文件中使用。只是不要让它与网络中其他设备的 IP 地址冲突。

然后运行本地开发服务器，传递 IP 地址`0.0.0.0`和端口`8000` :

```
(myenv)$ python manage.py runserver 0.0.0.0:8000 
```

Enter fullscreen mode Exit fullscreen mode

`0.0.0.0`是一个特例。它允许你通过任何分配给你的电脑的 IP 地址访问网站:`0.0.0.0`或`127.0.0.1`，或在你的网络设置中设置的地址。要通过这些地址访问网站，您必须在 Django 设置`ALLOWED_HOSTS`中列出这些 IP 地址。

此外，这允许您不仅从您的计算机，而且从任何智能手机、平板电脑或同一本地网络中的另一台计算机，通过您的计算机的 IP 地址，例如`http://197.160.2.7:8000`，检查您正在构建的网站。此外，通过相同的 IP 地址，您可以从虚拟机访问网站。例如，通过在 Mac 上的 Parallels Desktop 中安装 Windows，您可以测试 Django 网站在 Opera、Microsoft Edge 或 Internet Explorer 中的行为。

## 本地主机的域名

有时，您希望使用唯一的主机名来访问您正在开发的网站。这是必要的，当你有子域名，导致网站的不同部分(如`http://aidas.example.com`应显示我的个人资料)，或当你需要测试社会认证(如使用 Python 社会认证)。

处理方法之一是[配置一个`hosts`文件](https://support.rackspace.com/how-to/modify-your-hosts-file/)，它允许手动将主机名映射到 IP 地址。不幸的是，`hosts`文件不支持通配符条目，例如`<anything>.example.com`，因此对于任何新子域名，您都需要作为基于 Unix 的操作系统上的超级用户或 Windows 上的系统管理员来修改该文件。

更好的方法是使用指向本地主机 IP 的通配符域名:`127.0.0.1`。您可以自己在域提供商处设置它，或者使用可用的服务之一。

例如，Scott Forsyth 的 [localtest.me](http://readme.localtest.me) 允许您拥有无限个指向本地主机的通配符条目。因此，所有下列领域将显示在本地主机网站:

```
http://localtest.me:8000
http://myproject.localtest.me:8000
http://aidas.myproject.localtest.me:8000 
```

Enter fullscreen mode Exit fullscreen mode

无论您需要使用哪个域，不要忘记在 Django 项目设置中将它们添加到`ALLOWED_HOSTS`中。

这允许在脸书使用身份验证或通过 PayPal 支付(除了即时支付通知，我们稍后会谈到)。

你也可以测试子域分辨率。例如，Django 上下文处理器可能解析子域并添加一些上下文变量，或者中间件可能解析子域并重写路径或重定向到特定视图。

不幸的是，你不能用这样的地址从 iPhone 或 iPad 上测试这个网站。而且把自己域的[地址记录](https://my.bluehost.com/cgi/help/whats-an-a-record)(一条记录)设置成局域网内某台电脑的静态 IP，太不方便了。

## 域名为本地 IP

Basecamp 提供了另一种服务- [xip.io](http://xip.io) ，它允许您使用通配符域条目指向特定的 ip 地址。

假设您的计算机的 IP 地址是`197.160.2.7`，以下所有域将在您的计算机的本地 web 服务器上显示一个网站:

```
http://197.160.2.7.xip.io:8000
http://myproject.197.160.2.7.xip.io:8000
http://aidas.myproject.197.160.2.7.xip.io:8000 
```

Enter fullscreen mode Exit fullscreen mode

将它们添加到项目设置中的`ALLOWED_HOSTS`中，您就可以从本地网络中任何有能力的设备上查看网站。

除非您使用标准端口`80`，否则您必须添加端口号。此外，您的网站将在 HTTP 下显示为不安全，而不是 HTTPS，在某些情况下，您需要在安全的条件下测试 Django 网站，例如，当创建脸书画布应用程序或处理支付时。

## 隧道挖掘

有时你想在黑客马拉松上向其他参与者展示你的新网站。或者你想暂时与感兴趣的同事或朋友分享你的网站。或者你需要测试使用 Webhooks 的服务——HTTP 回调，它在特定事件时向你的服务器发送数据，比如 PayPal 的即时支付通知或者 twilio 的发送短信通知。

做到这一点的一个方法是拥有一个远程的临时网站，并经常部署到该网站上以测试开发结果。为此，您需要一个特定的域和服务器，可能还需要一些自动化部署。您还需要记录所有活动，并在终端中编辑日志文件——无法利用[带有断点](http://djangotricks.blogspot.de/2016/05/debugging-management-commands-in-pycharm.html)的便捷可视化 PyCharm 调试。

这是相当不方便的。幸运的是，这种方法的替代方案是存在的。

隧道是使您的本地主机对公共互联网开放的系统。隧道有一个前端——那是访问网站的服务器，后端——那是你自己的开发机器。通过创建隧道，您可以通过防火墙打开从前端服务器到在指定端口上运行的本地服务器的访问。

最著名的开源隧道系统是[ngrok.com](http://ngrok.com)、 [localtunnel.me](https://localtunnel.github.io/www/) 和[pagekite.net](https://pagekite.net)。让我们逐一看看。

### ngrok.com

虽然它现在还没有被积极开发——上一次提交是在一年多以前——[ngrok](http://ngrok.com)是最流行的一个。在撰写本文时，它已经拥有 [10573 GitHub stars](https://github.com/inconshreveable/ngrok) 。这个工具是用 go 编程语言编写的。

ngrok 是一项免费的增值服务，免费为你提供一个持久会话和一个随机生成的子域，但如果你想定制设置，甚至在你自己的服务器上安装，你必须支付年费。

要为一个本地 Django 项目启动一个隧道，您可以在终端中键入以下内容:

```
$ ngrok http 8000 
```

Enter fullscreen mode Exit fullscreen mode

然后，互联网上的任何人都可以通过在浏览器的地址栏中输入类似于`https://92832de0.ngrok.io`的内容来访问您的`http://127.0.0.1:8000`。

默认的 ngrok 配置还会启动一个在`http://localhost:4040`运行的特殊网站，显示进出 Django 网站的流量详情。

如果你是一个付费用户，并且想为你的网站建立一个自定义的子域，你可以在终端中输入这个启动隧道:

```
$ ngrok http -subdomain=myproject 8000 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个类似于`https://myproject.ngrok.io`的域，在您的本地主机上显示 Django 项目的内容。

在 DNS 配置中使用[规范名称记录](https://en.wikipedia.org/wiki/CNAME_record) (CNAME 记录)，也可以在自定义域名`https://dev.example.com`下的 ngrok 内创建隧道，甚至可以创建通配符条目`https://<anything>.dev.example.com`。

要将访问权限仅限于特定用户，您还可以使用以下命令进行基本身份验证:

```
$ ngrok http -auth="username:password" 8000 
```

Enter fullscreen mode Exit fullscreen mode

### localtunnel.me

这项服务是在一次黑客马拉松上一夜之间创建的，然后被发布和维护，因为它被证明是一个有用的工具。 [Localtunnel.me](https://localtunnel.github.io/www/) 不需要任何用户帐户，它会在随机生成的子域`https://nkfmosjsgh.localtunnel.me`或自定义子域`https://myproject.localtunnel.me`下创建对您的本地主机的临时访问权限，如果可用的话。当您关闭隧道时，不会为您保存地址以供将来使用。

Localtunnel 是免费开源的。如果你想或者需要，你可以在你自己的服务器上安装前端部分，也就是所谓的“本地”。

要启动隧道，您通常需要在终端中键入以下命令:

```
$ lt --port 8000 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要一个自定义域，你也可以输入:

```
$ lt --port 8000 --subdomain myproject 
```

Enter fullscreen mode Exit fullscreen mode

Localtunnel 对于快速临时访问来说相对简单。因此，CNAME 配置和通配符子域是不可能的。

这个项目仍在积极开发中。它是在 Node JS 中编程的，并且在编写时收到了 [4832 GitHub Stars](https://github.com/localtunnel/localtunnel) 。

### pagekite.net

Pagekite 是一个开源的、基于 python 的、按需付费的解决方案。相比之前的项目，它只有 [368 颗 GitHub 星](https://github.com/pagekite/PyPagekite)，但也值得一试。

您可以使用 Pagekite 启动隧道，方法是在终端中输入带有您私人用户域名的命令:

```
$ pagekite.py 8000 myuser.pagekite.me 
```

Enter fullscreen mode Exit fullscreen mode

这将从`https://myuser.pagekite.me`打开对您的本地 Django 项目的安全访问。

对于每个项目，你可以有一个单独的项目地址，像`https://myproject-myuser.pagekite.me`可以这样创建隧道:

```
$ pagekite.py 8000 myproject-myuser.pagekite.me 
```

Enter fullscreen mode Exit fullscreen mode

使用 Pagekite，你可以在域配置中使用 CNAME 设置为你的隧道定制像`https://dev.example.com`这样的域。也有可能暴露非 web 服务，例如 SSH 或《我的世界》服务器。

使用如下命令可以进行基本认证:

```
$ pagekite.py 8000 myproject-myuser.pagekite.me +password/username=password 
```

Enter fullscreen mode Exit fullscreen mode

### Django 项目配置

如果你想在 Django 项目中使用隧道技术，你必须在这里和那里做一些修改:

更改 URL 配置以显示静态和媒体文件，即使在非调试模式下:

```
# urls.py
# ...
import re
from django.views.static import serve

if settings.STATIC_URL.startswith("/"):
    urlpatterns += [
        url(
            r'^{STATIC_URL}(?P<path>.*)$'.format(STATIC_URL=re.escape(settings.STATIC_URL.lstrip('/'))),
            serve,
            # {'document_root': settings.STATIC_ROOT},
        ),
    ]
if settings.MEDIA_URL.startswith("/"):
    urlpatterns += [
        url(
            r'^{MEDIA_URL}(?P<path>.*)$'.format(MEDIA_URL=re.escape(settings.MEDIA_URL.lstrip('/'))),
            serve,
            {'document_root': settings.MEDIA_ROOT},
        ),
    ] 
```

Enter fullscreen mode Exit fullscreen mode

如果你想让不同的应用程序自动识别静态文件，省略`{'document_root': settings.STATIC_ROOT}`。否则，每次更改 CSS、JavaScript 或样式图像文件时，您都必须运行`collectstatic`管理命令。

对暴露的入口进行单独设置。

```
# settings.local_exposed
from .local import *
DEBUG = False
ALLOWED_HOSTS = [...] # enter the domains of your tunnel's frontend
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https') 
```

Enter fullscreen mode Exit fullscreen mode

要使用这些设置，请在您的虚拟环境中运行以下命令:

```
(myenv)$ python manage.py runserver --settings=settings.local_exposed --insecure 
```

Enter fullscreen mode Exit fullscreen mode

这里的`--insecure`指令强制从项目的不同位置自动识别静态文件，即使在非调试模式下。如果您提供的是由`collectstatic`命令收集的静态文件，请忽略它。

### 安全建议

这个安全建议列表并不完整。使用隧道挖掘自担风险。

*   不要让隧道一直开着。不需要时，关闭连接。
*   只与受信任的人共享前端 URL。如果您使 URL 易于记忆或猜测，请为隧道前端设置基本身份验证。
*   关闭 Django 项目中的调试模式。
*   经常备份项目的代码、媒体文件和数据库。
*   不要用生产数据做开发。
*   不要使用敏感数据进行测试:没有真实的密码或实时系统的 API 令牌，使用 PayPal 或 Stripe 支付的沙盒凭据等。
*   如果你不信任隧道服务，你可以在你自己的服务器上设置一个隧道前端。

在 Django development server 中使用隧道技术时，您发现了其他安全问题吗？那么请在评论中分享你的想法。

## 最后的想法

当你在用 Django 开发响应式网站，需要检查它在移动设备上的工作情况时，你可以用`0.0.0.0:8000`运行开发服务器，通过你电脑的 IP 地址在你的 Wifi 网络上访问，也可以用 xip.io 通过域名类比检查。

当您需要检查子域解析时，您可以使用 hosts 文件，配置您的私有子域直接指向您的本地 IP，或者使用 localtest.me、xip.io 或其中一个隧道服务。

当你想调试 Webhooks 以获得关于已执行支付、已收到消息或已完成无服务器流程的通知时，你可以使用 ngrok.com、localtunnel.me、pagekite.net 或其他隧道服务。当然，你也可以设置一个带有日志功能的临时网站，但是这将会带来很多调试上的麻烦。

也许你知道一些其他有趣的解决方案如何处理域和本地开发服务器。如果你有，不要犹豫，在评论中分享你的建议。

*本帖最初发表于[djangotricks.blogspot.com](http://djangotricks.blogspot.com/2017/07/domain-name-for-django-development-server.html)T3】*