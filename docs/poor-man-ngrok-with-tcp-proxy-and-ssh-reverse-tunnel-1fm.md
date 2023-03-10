# 穷人的 ngrok 带 tcp 代理和 ssh 反向隧道

> 原文：<https://dev.to/k4ml/poor-man-ngrok-with-tcp-proxy-and-ssh-reverse-tunnel-1fm>

我们有一个私人开发服务器，我们在那里做我们的大部分工作。但是像现在所有现代 web 应用程序一样，它也需要与其他应用程序对话(或者被对话)。一种是通过 webhook，您应用程序通过 http 请求接收来自其他应用程序的通知。

要接收 webhook，您的应用程序需要可以从远程应用程序访问。如上所述，我们的开发服务器在专用网络上。我们唯一能从本地计算机访问它的方法是通过 ssh 隧道。例如，我会以:-
开始我的一天

```
ssh dev-server -L 8000:localhost:8000 
```

Enter fullscreen mode Exit fullscreen mode

然后，当我在远程开发服务器上运行例如`python manage.py runserver`时，我可以从我的笔记本电脑上以 [http://localhost:8000/](http://localhost:8000/) 的身份访问 django 应用程序。但是当然，我们不能通过这种方式接收来自 github 的 webhook。

这个问题的一个常见解决方案是使用 ngrok 之类的服务，我可以在远程开发服务器上运行命令:-

```
ngrok http 8000 
```

Enter fullscreen mode Exit fullscreen mode

ngrok 会给我们随机的子域，比如[https://xxyymmdd.ngrok.com/](https://xxyymmdd.ngrok.com/)，当通过互联网访问时，会将请求转发回我们的 django 开发服务器，上面的 ngrok 命令就是在那里运行的。它很完美，我大部分时间都用它来做我的个人项目。

但是在工作中，我不喜欢使用我们无法控制的第三方服务。一种解决方案是在完全暴露于因特网的小型 vps 上设置代理。我们可以使用 Caddy 或 nginx 来实现这一点。但这是一个永久性的设置。我想要类似上面 ngrok 的东西，在我关闭到远程 dev 服务器的连接后，隧道将会死亡。

第二种选择是启用网关端口的 ssh 反向隧道。所以我可以在开发服务器上运行这样的命令:-

```
ssh -R :8000:localhost:8000 proxy-server-ip 
```

Enter fullscreen mode Exit fullscreen mode

允许连接到代理服务器上的端口 8000，并转发到运行上述命令的主机。但是这样做的一个问题是，你不能得到 TLS 连接到代理服务器的端口 8000。

因此，为了更进一步，我们需要第二个工具的帮助——TCP 代理。如果你搜索一下，实际上有很多，所以我就选择了一个似乎能提供我所需要的。我选了 [tcpproxy](https://github.com/kahlys/tcpproxy) ，一个用 Golang 写的小工具。它有我需要的一切。所以当我想打开一个代理时，我的命令是这样的(在远程开发服务器上运行):-

```
ssh -t -R 9000:localhost:8000 scarif.planet.rocks -l username "tcpproxy -laddr 0.0.0.0:8000 -raddr localhost:9000 -lcert /etc/letsencrypt/live/scarif.planet.rocks/fullchain.pem -lkey /etc/letsencrypt/live/scarif.planet.rocks/privkey.pem -ltls"
Proxying from 0.0.0.0:8000 to 127.0.0.1:9000 
```

Enter fullscreen mode Exit fullscreen mode

然后，这提供了对 https://scarif.planet.rocks:8000/的公共访问，然后被代理到运行在端口 8000 上的 django 开发服务器。好吧，一开始看起来很混乱。但是它要求我只理解这个命令，而不是冗长的文档来知道它在做什么。让我们一步一步来看

1.  打开一个从代理服务器端口 9000 到本地机器端口 8000 的反向隧道(这里的本地是指命令执行的地方)。
2.  在代理服务器上运行 tcpproxy，监听所有网络接口的端口 8000。在这里，我们还启用 tls 并使用 letsencrypt 提供的证书。
3.  在本地接口上将端口 8000 连接转发到端口 9000。
4.  通过 ssh 反向隧道将端口 9000 上的连接转发回我们的本地机器。

这是由 asciiflow 提供的不太好的图表:-

```
 XXXXXXX
                                                     XX        XX
+--------+              +------------+               X
|        |              |            |               XX            X
|        <--------------+            | <-------------+X  Internet    X
|        |              |            |                X              XX
+--------+              +------------+                 XX             X
        8000            9000        8000                XX          XXX
 local                    proxy-server                     X     XXX
                                                            X XX 
```

Enter fullscreen mode Exit fullscreen mode

这种方法还缺少一点，那就是 ngrok 拥有的良好的请求检查器。

## 用球童替代

首先，我们在远程主机上生成所需的 caddy file:-

```
echo "https://scarif.planet.rocks {\n proxy / http://localhost:9000 {\n header_upstream Host dev.app.int\n }\n}" | ssh scarif.planet.rocks -l ubuntu -R 9000:localhost:80 "cat > Caddyfile" 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将立即结束，我们回到本地主机，但它会将文件写入远程主机。该文件将被写成:-

```
https://scarif.planet.rocks {
 proxy / http://localhost:9000 {
 header_upstream Host dev.app.int
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以 ssh，在远程上反向转发端口并运行 caddy:-

```
ssh -t scarif.planet.rocks -l ubuntu -R 9000:localhost:80 "caddy" 
```

Enter fullscreen mode Exit fullscreen mode

这最终在几个方面更好:—

*   我们可以以[https://scarif.planet.rocks/](https://scarif.planet.rocks/)的身份访问该网站。注意，caddy 绑定到端口 80，因为我们使用`setcap`来允许 caddy 绑定到较低端口，而无需成为 root。
*   在本地 apache 或 django 上不需要任何更改，比如添加到 ALLOWED_HOSTS 或设置 ServerAlias。这是因为我们在 caddy 代理请求时设置了主机头。

缺点是我们必须分两步走。我无法在一个 ssh 命令中编写配置文件，然后执行 caddy。得到了这个错误:-

```
Pseudo-terminal will not be allocated because stdin is not a terminal. 
```

Enter fullscreen mode Exit fullscreen mode

## 一期

1.  如果 ssh 连接终止，则终止远程 tcpproxy 进程。使用`ssh -t`似乎可以工作，如果我只是`Ctrl+C`连接，但如果它死了，例如由于网络问题，代理服务器上的 tcpproxy 将保持运行，你必须 ssh 到它和手动杀死进程。[stack exchange 上的这个问题](https://unix.stackexchange.com/questions/103699/kill-process-spawned-by-ssh-when-ssh-dies)给出了一些解决方法，但我还没有尝试过。

## 常见问题

问:**你知道宋承宪网关端口吗？**
答:是的，但是如上所述，它并没有给我们来自公众的 TLS 连接。

你试过 pagekite 吗？
答:是的，但是如果我想托管自己的服务器，我很难理解它是如何工作的。