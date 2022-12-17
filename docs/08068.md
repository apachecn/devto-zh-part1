# 欺骗浏览器

> 原文：<https://dev.to/georgeoffley/tricking-the-browser>

大家好，我已经做了几个星期的网络项目了。它将包括一个内置的代理，能够隐藏你的活动；只是为了让你所有的安全专业工具可以匿名使用。今天我想看一下我们的委托书上的一些东西。当向网站发出请求时，我们会经历与 HTTP 协议相关的步骤。

这一过程包括的步骤很多，本身就应该有一个完整的职位。现在你可以参考[这个网站](http://celineotter.azurewebsites.net/world-wide-web-http-request-response-cycle/)的步骤。在我们将 IP 地址解析为请求的主机名之后，我们向远程服务器发送第一个请求，同时发送关于我们是谁的详细信息。它包括我们的 IP 地址，我们 pinged 的 DNS 服务器和我们的[用户代理](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)。我们的用户代理只是一个发送到服务器的字符串，详细说明我们正在使用的操作系统、版本号和正在使用的 webkit。例如火狐用户的 Mozilla 或 safari 的 Apple Webkit。

这个脚本的目标是将自定义用户代理插入到标题信息中，以此来欺骗远程服务器我们的真实身份。这是掩盖身份的好方法，在渗透测试或测试网络工具中很有用。网站通常会使用服务器端软件来跟踪 IP 地址和用户代理，以便禁止来自某些远程节点的任何奇怪流量(例如网络爬行)。

这只是概念验证，需要集成到工具中。

下面是我创建的代码:

```
 import random
import requests

user_agents = []
user_agents_file = open("user_agents.txt", "r") # Text file of user agents 
for agents in user_agents_file:
    # cycles through the list adds them to the user_agents array 
    user_agents.append(agents)
    random_agents = random.choice(user_agents)[1:-2] # Takes out parens and new line character, also randomizes array choice 
url = "http://quotes.toscrape.com"
headers = {"user_agents" : random_agents}

r = requests.get(url, headers=headers)

print r.content 
```

让我们稍微分解一下。我们导入我们的请求和随机模块，然后开始工作。我们创建一个名为 *user_agents* 的数组。我们有一个与这个脚本相关联的文件，它包含 900 多个不同用户代理的纯文本，这些用户代理会出现在 HTTP 头中。下一个 for 循环使用我们的空数组，填充它，遍历该数组并随机选择一个插入到我们的头中，并从字符串中删除引号。然后，我们有我们的网址，我们将发送一个请求。使用请求模块，我们实际上可以将信息插入到我们的请求头中。因此， *r = requests.get(url，headers=headers)* 行给出了我们的请求并插入了头部信息。就这么简单。