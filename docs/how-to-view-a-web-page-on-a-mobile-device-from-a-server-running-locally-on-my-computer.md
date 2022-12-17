# 如何从本地运行在我的计算机上的服务器查看移动设备上的网页

> 原文：<https://dev.to/monicag/how-to-view-a-web-page-on-a-mobile-device-from-a-server-running-locally-on-my-computer>

开发网站的时候，我喜欢在本地电脑上运行。这让我可以很快看到我所做的改变。但在本地运行网络服务器意味着我无法在手机上查看。我该如何解决这个问题？通过将 web 服务器设置为监听 0.0.0.0。现在我可以从手机上看到我的作品了！但是什么是 0.0.0.0 呢？我们来探索一下。

在本地运行 web 服务器意味着它正在监听 127.0.0.1 IP 地址。这是环回地址，它不向网络接口卡传递任何数据包。这意味着 web 服务器只监听来自主机的请求。因此，web 服务器需要监听另一个地址。输入 0.0.0.0 IP 地址。这个 IP 地址意味着监听“本地机器上的所有 IPv4 地址”。我的计算机的 IP 地址是 10.0.1.22，所以 web 服务器将监听该 IP 地址上的请求。它仍然会监听 127.0.0.1。在我的手机上，我可以通过 http://10.0.1.22:port 访问我的网站。注意:您的移动设备需要与您的主机在同一个网络上。

一些例子:

**哲基尔**

```
jekyll serve --watch --drafts --host 0.0.0.0 
```

Enter fullscreen mode Exit fullscreen mode

在我的移动设备上，我将输入 URL:[http://10 . 0 . 1 . 22:4000](http://10.0.1.22:4000)

**烧瓶**

```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(host='0.0.0.0') 
```

Enter fullscreen mode Exit fullscreen mode

在我的移动设备上，我将输入 URL:[http://10 . 0 . 1 . 22:5000](http://10.0.1.22:5000)

但是，为什么要用 0.0.0.0 而不是 10.0.1.22 呢？您可以使用您的 IP 地址来代替。但是如果你的 IP 地址改变了，你需要记得在你的网络服务器上改变它。此外，使用 0.0.0.0 还允许您在主机上通过 127.0.0.1 访问您的站点。

# 参考文献

*   [https://en.wikipedia.org/wiki/0.0.0.0](https://en.wikipedia.org/wiki/0.0.0.0)
*   [https://en.wikipedia.org/wiki/Localhost](https://en.wikipedia.org/wiki/Localhost)
*   [https://en . Wikipedia . org/wiki/Loopback # Virtual _ Loopback _ interface](https://en.wikipedia.org/wiki/Loopback#Virtual_loopback_interface)
*   [https://jekyllrb.com/docs/configuration/](https://jekyllrb.com/docs/configuration/)
*   [http://flask.pocoo.org/docs/0.11/api/](http://flask.pocoo.org/docs/0.11/api/)
*   [https://tools.ietf.org/html/rfc5735#section-3](https://tools.ietf.org/html/rfc5735#section-3)

这篇文章最初发表在我的博客上。