# 一个新的角度服务工作者——创建自动渐进式 web 应用程序。第一部分:理论

> 原文：<https://dev.to/webmaxru/a-new-angular-service-worker-creating-automatic-progressive-web-apps-part-1-theory-1cjj>

[![](img/53568cb10863d480e04d32e5716e1bc4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dgIQr9X3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A75SOUIEW4izzHUoSYmxnTg.png)

> 声明:有这篇文章的“ [Part 2: practice](https://medium.com/@webmaxru/a-new-angular-service-worker-creating-automatic-progressive-web-apps-part-2-practice-3221471269a1) ”可用。

作为一名“未来用户界面”工程师，我喜欢尝试最新版本的框架、库、API——所有与 web 前端开发相关的东西。从这个意义上来说，Angular 和 [Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/) 分别对我来说是完美的，因为不断的开发、更新、变化(有时是突破性的)，但它们的组合是一种爆炸性的混合物。

让我们来探索 Angular 团队在他们向渐进式 web 应用的移动中所采取的方向。最近发布的[Angular 5 Release Candidate 0](https://github.com/angular/angular/releases/tag/5.0.0-rc.0)引入了一个新的 Angular Service Worker (NGSW)，这是我们的主要话题。

先说一点历史。Angular PWA 的第一个概念于 2016 年初推出，[以“移动工具包”的名义在 Google IO’16](https://www.youtube.com/watch?v=vAb-2d1vcg8)上展示。相应的网站还在，但是文档已经过时了，甚至对于 NGSW 的旧版本/当前版本也是如此(这就是为什么我没有给出它的链接)。作为一名与 Angular 团队密切合作的谷歌开发专家，我尽最大努力来解决这种信息缺乏的问题。我在[的几十个会议](http://lanyrd.com/profile/webmax/past/speaking/)上介绍 Angular Service Worker，包括主要的 Angular 活动——ng-conf，举办私人和公共研讨会，并在当前(beta 16)版本的@angular/service-worker 上维护一个[“社区驱动”文档](http://bit.ly/pwa-angularsummit-2017)。但现在是时候继续前进了，在今年所有计划的下一次会议(AngularMix、国际 JavaScript 会议、Angular Connect、SoCal Bootcamp、NG-BE)上，我将介绍/培训最新的 NGSW，它是 Angular 5 的一部分。

正如在[对应的拉请求](https://github.com/angular/angular/pull/19274)中所述，新的服务人员是“现有服务人员的概念衍生物”。这是真的。这个想法非常相似:我们通过只提供一些 JSON 配置，而不是手动编写代码，用服务人员来启动我们的应用程序。但是实现和配置设置是不同的。对于熟悉 NGSW beta 16 的人来说，主要区别在于:

*   尚未与 Angular CLI 集成，但包含了自己的迷你 CLI
*   服务人员自身的设置文件和我们创建的设置文件现在更明确地分开了。实际上，我们用任何名称编写自己的 JSON 文件(我们称之为服务工作者*配置文件*)并使用上面提到的 CLI 为服务工作者构建一个名为 ngsw.json 的文件(我们称之为服务工作者*控制文件*)。我发现这个更新非常有用:在当前版本中，ngsw-manifest.json 与 web app manifest 经常混淆，而且合并自动生成和手动编写的清单的想法也不太明显。
*   没有插件的概念。目前还不清楚如何扩展功能。

与其他服务工作者生成器(如[工具箱](https://dev.to/webmaxru/using-workbox-2-and-angular-to-create-a-progressive-web-app-part-1-app-shell-jp4-temp-slug-4987000)，sw-precache)的主要区别在于，您不重新生成服务工作者文件本身，您只更新它的控制文件。

### 快速入门

> 声明:这只是 Angular Service Worker 的第一个版本。并不是所有的东西都能在那里正常工作，而且对开发者来说也不是很友好。此外，实现的细节以及公共 API 可能会在下一个版本中更改。

在我们的实验中，让我们用我的 PWA 豚鼠应用程序——p water。这是最简单的 Angular 5 RC0 应用程序，由琐碎的 NodeJS 后端提供支持。PWAtter 可以加载 tweet feeds 并订阅推送通知。GitHub 上有代码:[https://github.com/webmaxru/pwatter/tree/ngsw/](https://github.com/webmaxru/pwatter/tree/ngsw/)

Angular Service Worker 还没有和 Angular CLI 集成，所以你在 node_modules/@angular 里看不到 service-worker 包在搭建了一个新的 app 之后，我们来明确安装最新版本:

```
npm install [@angular/service-worker](http://twitter.com/angular/service-worker)@next --save 
```

我们需要从已安装的软件包中获得什么:

*   **@ angular/service-worker/ngsw-worker . js**—服务人员本身。目前只包含了非最小化版本。我们必须将该文件复制到我们的 dist 文件夹中，并注册为服务人员。
*   **。bin/ngsw-config** — NGSW 命令行界面
*   由@angular/service-worker 公开的 ServiceWorkerModule—用于在 angular 客户端应用程序中注册并与服务人员通信。

#### 流程如下:

**在我们的应用中:**

我们使用至少 3 个选项中的任何一个来注册服务人员:

*   将注册脚本添加到 index.html
*   在 bootstrapModule()之后的 main.ts 中使用相同的代码
*   采用“NGSW”方式并使用 ServiceWorkerModule 的 register()方法，让我们选择这个选项: