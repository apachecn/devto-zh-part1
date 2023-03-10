# 更好的静态故障点

> 原文：<https://dev.to/shindakun/better-static-glitch-sites-19j>

这里有一个来自[故障支持论坛](https://support.glitch.com/t/support-static-json/1499/6?u=shindakun)的小提示。默认情况下，一旦 Glitch 在 app 文件夹的根目录中没有找到一个`package.json`文件，它就会在您的`./app/`目录中启动一个静态 web 服务器。这并不总是想要的结果——例如，如果使用`grunt`(或者无数其他构建工具中的一个)开始站点生成，并且您不能移除`package.json`，那该怎么办？在这种情况下，您的`package.json`中的启动脚本可能与构建相关，或者试图启动它自己的静态服务器。以下面的片段为例:

```
"scripts": {
  "build": "grunt build",
  "start": "grunt connect:server:keepalive",
  "test": "grunt test",
  "postinstall": "make build"
} 
```

Grunt 将尝试启动它自己的静态服务器，但由于故障，我们很可能无法从互联网上获得。

我们能做些什么呢？幸运的是，Glitch 使用本地网络服务器 T2 作为静态网络服务器。这允许我们将`package.json`保留在原位——这样默认的服务器就不会启动，但是，通过将 start 命令更新为

```
"start": "ws --port 3000 --directory './' --forbid '/.env' --forbid '/.data' --log-format combined", 
```

我们将自己启动本地网络服务器，并向公众开放。如果您使用`.env`或`.data`，请确保您没有关闭禁止开关，否则会发生泄漏。

最后，如果您的静态站点需要在不同的目录中启动，只需更新`directory`开关，例如`--directory './www'`，您就可以开始了。

[![](img/f4c5f097e8f3abdd19ad13f8bfefd4d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qWLZpkoG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.google-analytics.com/collect%3Fv%3D1%26_v%3Dj64%26t%3Dpageview%26_s%3D1%26dl%3Dhttps%253A%252F%252Fdev.to%252Fshindakun%252Fbetter-static-glitch-sites-19j%252F%26ul%3Den-us%26de%3DUTF-8%26dt%3DBetter%2520Static%2520Glitch%2520Sites%26je%3D0%26jid%3D%26gjid%3D%26tid%3DUA-509290-3)