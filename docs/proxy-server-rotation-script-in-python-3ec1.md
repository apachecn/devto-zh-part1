# Python 中的代理服务器旋转脚本

> 原文：<https://dev.to/kashaziz/proxy-server-rotation-script-in-python-3ec1>

[![Proxy Server Rotation Script in Python](img/c60f62cf1b2acb68708c091f12b3e2e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zDQDYAfY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/18eaxrnq802o0xin8uew.jpg)

# Python 中的旋转代理服务器

最近我用 ProxyMesh 代理服务器做了一个项目。ProxyMesh 提供 15 个代理服务器，每个代表一个特定的位置(如 us-DC ), 10 个 IP 地址每天轮换两次。

但是，免费试用版允许您一次使用一个代理服务器。这意味着，如果您正在使用严格限制 IPs 的服务器或 CDN，您必须手动更改代理服务器，以保持在 15 个代理服务器之间轮换。

幸运的是，ProxyMesh 提供了一个 API，可以用来在用户仪表板中添加和删除代理服务器。一旦代理服务器被分配给用户，它就可以被获取和使用。我们所需要的只是一个在代理服务器之间循环的脚本，根据需要删除和添加它们。

基于此，我编写了一个脚本，它将使用 ProxyMesh 代理服务器的 API 在这些代理服务器之间循环。

## 先决条件

ProxyMesh 的帐户，免费试用或付费。在 rotatingproxy.py 中设置用户名和密码

```
self.user = ""
self.password = "" 
```

## 用法

### 设置代理服务器

```
from rotatingproxy import RotatingProxy

rproxy = RotatingProxy() 
```

代理服务器可以随机设置，也可以从可用的代理服务器列表中选择。
活动代理服务器保存在一个文本文件中，可根据需要访问。

```
rproxy.set_proxy(israndom="r")  # select a random proxy server

rproxy.set_proxy(proxy_num=1)   # select proxy server with index=1 from the list of proxy servers. 
```

### 访问代理服务器

```
def get_proxy_from_file():
    # fetches proxy from proxy.txt
    with open("proxy.txt", "r") as f:
        return loads(f.read())

proxy = get_proxy_from_file() 
```

代理现在可以用于请求:

```
import requests
response = requests.get("url-to-fetch", proxies=proxy) 
```

博文:[http://www.kashifaziz.me/proxy-server-rotation-python.html/](http://www.kashifaziz.me/proxy-server-rotation-python.html/)

GitHub:[https://github.com/kashaziz/rotating-proxy-python](https://github.com/kashaziz/rotating-proxy-python)