# 容器环境中 5 分钟的 SSH 代理设置

> 原文：<https://dev.to/mastanggt/5-minute-ssh-proxy-setup-in-a-container-environment-32p>

我将告诉您如何将 ssh 从 Docker Hub 拉到 Docker 平台，并使用几个命令启动代理。我用 [Containerum](https://containerum.com/) 做到了这一点，但我相信这个过程对其他 docker 平台来说或多或少也是一样的。

基本上，您需要做的是从 Docker Hub 中取出 SSH 映像，并将其部署到容器中。然后您需要从 GitHub 公开 SSH 和 pull 代理。然后，您只需从 SSH 容器内部运行您的代理。

在本教程中，我使用了 container um CLI([*https://github.com/containerum/chkit*](https://github.com/containerum/chkit))所以请记住，命令是特定于平台的，并且会根据您使用的平台而略有不同。

*   首先，我必须下载并安装 Containerum CLI (chkit)。然后使用登录

```
chkit login 
```

Enter fullscreen mode Exit fullscreen mode

*   用 Debian 映像创建一个容器

```
chkit run debian --image hklcf/debian-ssh-docker --port 22 --port 8989 --memory 512Mi --cpu 400m 
```

Enter fullscreen mode Exit fullscreen mode

22 和 8989 是这里的端口
[![](img/7c0fb4d31ad751673ab98ca3e747cb84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_5ShJDYk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yai2lgqh56bjpk1174oq.png)

*   为您的代理暴露您的 debian 容器和端口

```
chkit expose deploy debian -p ssh:22:TCP -p proxy:8989:TCP 
```

Enter fullscreen mode Exit fullscreen mode

*   使用检查映射端口

```
chkit get svc 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，22 被映射到 34441。8989 映射到 27471。
[![](img/8b29a177558181cd2d8ee0955ec397ae.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ySZbtGWn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w9wlu5e7tqv5zziepzf4.png)

*   接下来——登录 SSH。为此，输入您的 IP 和服务端口——在上面的例子中，我们的 IP 是 x3.containerum.io，SSH 端口是 34441。该映像的默认用户和密码是“root / password”

*   用
    更新系统并安装 wget

```
apt-get update && apt install wget 
```

Enter fullscreen mode Exit fullscreen mode

*   然后安装代理服务器

```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh && \
chmod +x shadowsocks-all.sh && ./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/7aa304ba00aaf8f0c84eab73fb3c661a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ElHAOAgL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0lw7z2dkyfcjaeqlgbse.png)

*   最后，配置您的代理服务器和您的 shadowsocks 客户端，并享受！

如果你有任何问题或者知道在容器中启动 SSH 代理服务器的更好的方法，请在下面评论！