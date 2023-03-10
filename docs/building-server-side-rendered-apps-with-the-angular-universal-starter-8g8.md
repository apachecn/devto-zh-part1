# 使用 Angular Universal Starter 构建服务器端渲染应用程序

> 原文：<https://dev.to/techiediaries/building-server-side-rendered-apps-with-the-angular-universal-starter-8g8>

Angular Universal Starter 是一个最小的入门工具，可以快速入门一个同构的 Angular 2+ web 应用程序。你可以简单地从 Github 克隆它，并开始在它的基础上构建你的通用 Angular 应用程序。让我们看看如何做到这一点。

注意:

这篇文章最初发表在我的网站:[技术指南](https://www.techiediaries.com)你可以在这里找到更多关于 Angular、React to NativeScript 和 Ionic 2+的新 web 和移动技术的教程。

首先，您需要在系统上安装 Git。您还需要安装和配置 Node.js 和 NPM。

接下来用以下代码从 Github 克隆通用启动器:

```
git clone https://github.com/angular/universal-starter.git universal-demo 
```

然后在克隆的存储库中导航，并安装所需的依赖项:

```
npm install 
```

成功安装所有需求后，您可以使用以下命令启动开发服务器:

```
npm start 
```

构建应用程序后，您可以使用以下工具创建产品捆绑包:

```
npm run build:prod 
```

## 使用角万能时的一些注意事项

您不应使用特定于浏览器的对象，如窗口、文档、导航器，因为它们只存在于浏览器环境中，并且由于 Angular Universal 使用 Node.js express 服务器，您无法访问它们。但是如果你需要操作 DOM，你需要做什么呢？

您应该只在客户端代码上这样做，并确保将它们包装在 isBrowser 函数中

```
import { isBrowser } from 'angular2-universal' 
```

确保使用角度渲染器类，而不是直接操作本地元素。