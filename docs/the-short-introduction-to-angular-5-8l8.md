# Angular 5 简介

> 原文：<https://dev.to/kris/the-short-introduction-to-angular-5-8l8>

[![](img/e97dbdc265afd9a8eb00adee5d696c67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gUTdL_c2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ae-MRPe_ePmF0XWGypc0xfg.png)

### 什么是棱角分明？

Angularjs 是一个 javascript 框架，允许你创建**反应式单页应用程序(SPAs)**

什么是反应式单页应用程序？与传统的 web 应用程序相比，我们将 80%的代码放在服务器上，服务器处理所有的事情

[![](img/4e1b37506dcc1d5581ea5ce9d9fff592.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TjqmuU_j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A3v8HAcN5_ny--EGTideYpg.png)

当我们点击其他页面时，得到的是等待重新加载所有页面的空白白屏

因为在过去，用户设备没有太多的性能。今天感谢摩尔定律。我们可以将服务器上的工作转移到用户设备上

这个单一的应用程序将诞生

[![](img/eadf81481da96e8444d0aa8c5073b8e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vgrn7Ica--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A3My-_ZuA0lwT0rOxZJkOLA.png)

Javascript 框架的崛起征服了前端网络，因为用户设备的性能更高，想想一些品牌的智能手机比笔记本电脑有更多的内存

如果您需要查看单页应用程序的角度示例

[https://medium . com/media/44e 7 E0 d 58 f 8 ce 6a 96 eff 16d 71 eeb 3d df/href](https://medium.com/media/44e7e0d58f8ce6a96eff16d71eeb3ddf/href)

这张图片是一个例子，我们可以转到其他页面或标签，而不需要重新加载页面，看起来像本地应用程序，给用户一个非常积极的用户体验

当我们查看页面源代码时

[![](img/02f9873ab38d1472d03959c0fdde9aad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MWpoVCEX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmM7mrUzlW9ZqgF6GfBxJSA.png)

看不出复杂。为什么？因为棱角处理所有一切

看看这是怎么回事[点击这里](http://datatorrent.github.io/malhar-angular-dashboard/#/)

更多角度项目示例[请查看此](https://www.madewithangular.com/)

### 棱角分明的演变

现在 Angular 已经是第 5 版了，在升级到主要版本 Angular 的时候总是会有一些突破性的变化

Angular 2 完全改写了 Angular 1 的不同之处

[角度 3 已经跳过](https://www.infoworld.com/article/3150716/application-development/forget-angular-3-google-skips-straight-to-angular-4.html)

角度 4 最大[从角度 2 突变](https://medium.com/@angularminds/comparison-between-angular-1-vs-angular-2-vs-angular-4-62fe79c379e3)

幸运的是棱角 5 号并没有[突破变化](https://auth0.com/blog/whats-new-in-angular5/)

### **从 Angular CLI 安装 Angular 5**

如果你不介意在下一步之前从这个安装[。Angular CLI 是为生成角度项目结构而构建的命令行工具](https://dev.to/krissnawat/2-ways-to-install-nodejs-on-macos--the-beginners-guide-44d-temp-slug-3476696)

让我们开始进入[角度范围](https://cli.angular.io/)

<figure>[![](img/ccdf053fc4f66679358920385757a5b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SfGLCrHd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvzGcqnBwQCPEPn2EcM3zAA.png) 

<figcaption>棱角分明 cli 官方网站</figcaption>

</figure>

打开终端，复制并运行以下命令

npm i -g @angular/cli

<figure>[![](img/d5010048b7742d16ab4253373b0842e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XYlBfygA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/785/1%2AN76qIHsXSku1BWM6LXlXhA.png) 

<figcaption>安装角形 cli</figcaption>

</figure>

并使用 ng new myapp 生成新应用

[![](img/2a4082789b3c0ad6509f8e1388e4092a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RhbZzWas--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/785/1%2Ahdy1JzUuyJU_jBPaQKE_qQ.png)

然后打开文件夹 cd myapp 和 ng serve

[![](img/dfd634d282fbf2a850daaa2cba9d452e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JMA_WDbs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/785/1%2ApdXidaHk10sPQxZHjMzWlQ.png)

并等待 webpack 成功构建资产

[![](img/4ccb646995215d99c6f419f5260a9f5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7wGwmzD5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/785/1%2AHSL3On16FaxBJ_BWGcokxA.png)

然后转到本地主机:4200

这是工作

<figure>[![](img/4560a1262d318481bae16ee72288c251.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fcZt4sxZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARyRIXXbsaW4ev6ipLnDf6g.png) 

<figcaption>第一张棱角分明的欢迎页</figcaption>

</figure>

然而，您可以使用带脚本标签
的角度独立

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.6.5/angular.min.js"></script> 
```

### **发现项目结构**

使用终端快捷方式代码打开项目文件夹。

[![](img/8ccb644f63ea6c2fb1c8e10801a03934.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--18xid5hw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5VVJsdNUn0W3Esp9EIo0gg.png)

VScode 窗口即时打开当前文件夹

让我们先来探索一下 e2e 文件夹

[![](img/f51b365242946b529afc875e3beaa233.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GQccKj9a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgLsGMczpJEWLcElykd_qbA.png)

该文件夹包含测试文件

[![](img/8f50090b97e6931f972e8e88bb0432d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q14VLK-x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/244/1%2ApaKH3vci7GkkwiXAFmIZPg.png)

node_module 包含依赖库文件

下一个我们要工作的地方

[![](img/5413893b3931658d13b57d0be6d15041.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dp7gvzbf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A52JxXoCfUFC8UwXsorQLZw.png)

src 文件夹包含我们需要的任何其他东西。此文件夹之外的任何文件都是为了支持构建应用程序。

应用程序文件夹包含组件文件

[![](img/ac660a34ec8991344d50dc270993fa81.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7Qpz93I1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/255/1%2AcSUfR5izKPsvqaDCH_IW5w.png)

*   app/app.component.ts —这是我们定义根组件的地方
*   *app/app.module.ts* —要引导的入口角度模块
*   _app/app.component.spec.ts —此组件的测试文件
*   *app/component.html* —包含用于该组件中模板的 html
*   *app/component.css —* 包含在该组件中使用的 css

环境文件夹包含由环境分隔的配置文件

[![](img/547e26f167ef47eccb6ca26febc24654.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NgJVF4W---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/232/1%2AiU6mlSBSKPQU5JJHEkKQqA.png)

*   environment.prod.ts —包含用于生产的环境变量
*   environment.ts —包含用于开发的环境变量

更多信息[请查看此](https://angular.io/guide/quickstart#project-file-review)

### 你好棱角分明

让我们打开应用程序文件夹编辑文件 app . component . html 开始 Hello Angular 5

[![](img/41ef30e5a624d5e9355b76299205b8ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1s5_BluZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALvG7jIYPWj_iY8riWfAK0A.png)

保存并刷新

[![](img/a2eeda98673036c1a7ba3fe521f9b526.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lGVUwBoe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/914/1%2AhWd8T4a3m2j97XgEhvm4TA.png)

这就是改变。

### 结论

这篇文章我介绍了 javascript 框架诞生的原因，并安装了 Angular CLI 和最终版 Hello Angular

### **赞助商**

Devslopes 所有访问会员超过 300 小时的内容。低至每月 20 美元[点击此处查看](http://bit.ly/devslope-checkout?source=collection_home---2------0----------------)

* * *