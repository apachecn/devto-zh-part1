# 创建-反应-应用反应-路由器和 Heroku

> 原文：<https://dev.to/darraghor/create-react-app-react-router-and-heroku-3j2b>

我一直在组装一个 React 应用程序，我需要便宜、快速的主机，所以我把它放到了 Heroku 上。

### 配置 Heroku

社区已经为 create-react-app 整合了一个构建包。基本上 2 分钟就能部署完毕。

假设您已经配置了 heroku 命令行工具，以下要点描述了设置构建包的命令:

[https://gist . github . com/darraghoriordan/c3d 355237246 df 395 BD 4c 5 ffedf 002d 1 # file-heroku-sh](https://gist.github.com/darraghoriordan/c3d355237246df395bd4c5ffedf002d1#file-heroku-sh)

### 配置您的应用

这很神奇，但是您的应用程序仍然需要一些路由配置。如果你使用 react 路由器，当你试图直接打开一个路由时，你将从 nginx 得到一个 404。

heroku build pack 将您的站点作为静态 react 应用程序部署在 heroku 上，因此您必须告诉 heroku，对于所有路由，它应该只进入您的根/索引文档，以便 react-router 可以处理应用程序路由。create-react-app aps 中的默认根文档是 index.html

因此，要在 Heroku hosting 上修复您的站点，请使用以下配置将名为“static.json”的文件添加到您的根文件夹(在 packages.json 旁边)。

[https://gist . github . com/darraghoriordan/008569 feb 8 cc 319 e 338 ad 11 FB 68 a 4647 # file-heroku _ config-js](https://gist.github.com/darraghoriordan/008569feb8cc319e338ad11fb68a4647#file-heroku_config-js)