# 开源工具和工作代理

> 原文：<https://dev.to/booyaa/open-source-tools-and-work-proxies-adn>

我经常在工作中使用许多开源工具，最初我从 node 和 npm(用于我们的前端)开始，最近是 python、Go，当然还有 Rust。

不幸的是，许多工具，期望你能直接访问互联网，如果你在工作代理的后面，大多数将不能连接到他们的注册库或者从源代码控制库中下载代码。

更糟糕的是，工作代理通常需要 Windows 身份验证。因此，与其将您的凭证保存在一个纯文本文件中，您可能更喜欢使用类似于 [Fiddler](https://www.telerik.com/fiddler) 的东西。Fiddler 是一个优秀的诊断工具，用于排除 web 应用程序的故障，一个额外的特性是它还提供了一个本地代理(通常监听端口 8888)，使用您现有的浏览器代理设置。

之后，只需要配置您的工具来使用这个本地代理。请注意，如果你不能在浏览器中访问像 GitHub 或 T2 NPM 这样的网站，你不会神奇地通过这种方式访问，这些限制将继续存在。

下面是让您的开源工具与这个本地代理一起工作的方法列表:

## 环境变量(env vars)

将这些设置作为最低要求，大多数工具将查找这些环境变量，并应该开始工作。

```
HTTP_PROXY=http://127.0.0.1:8888
HTTPS_PROXY=http://127.0.0.1:8888 
```

Enter fullscreen mode Exit fullscreen mode

这与 [rustup](https://rustup.rs) 一起工作，

## npm

```
npm config set proxy http://127.0.0.1:8888
npm config set https-proxy http://127.0.0.1:8888 
```

Enter fullscreen mode Exit fullscreen mode

## 货物

编辑`%USERPROFILE%\.cargo`并添加以下内容:

```
[http]
proxy = "http://127.0.0.1:8888" 
```

Enter fullscreen mode Exit fullscreen mode

## 去吧

```
git config --global http.proxy http://127.0.0.1:8888 
```

Enter fullscreen mode Exit fullscreen mode

这应该可以解决 golang 和可能的货物的问题(当拉依赖时)。

## 蟒蛇(Anaconda)

编辑`%USERPROFILE%\.condarc`并添加以下内容:

```
channels:
- defaults

# Show channel URLs when displaying what is going to be downloaded and
# in 'conda list'. The default is False.
show_channel_urls: True
allow_other_channels: True

proxy_servers:
    http: http://127.0.0.1:8888
    https: http://127.0.0.1:8888 
```

Enter fullscreen mode Exit fullscreen mode

## vscode

编辑您的用户设置文件并添加

```
 "http.proxy":  "http://127.0.0.1:8888",  "http.proxyStrictSSL":  false, 
```

Enter fullscreen mode Exit fullscreen mode