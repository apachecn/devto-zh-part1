# 从头开始创建购物应用程序(第 1 部分)

> 原文：<https://dev.to/samwsoftware/creating-a-shopping-app-from-scratch-part-1-16bk>

[![](img/eb30bf891890c501670590913a6c9f1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--R81iI7xy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AH_HClUxBtZ6hEIKBxdUyZw.gif)

#### 能让我找到一份好工作的项目！

我即将结束为期 6 个月的东南亚旅行和攀登。那意味着我需要重新进入现实世界，找一份工作。我已经申请了很多公司，并在其中一些公司取得了进展。

一个这样的公司是和数字。他们是一家数字咨询公司，主要为公司提供指导，开发软件，教授个人和团队。这是给我这个任务的公司。

这个项目也是我建立一个更大的个人项目的好方法。我做过的很多项目要么是功能最少的小型网站，要么是团队的一部分。这将让我体验到在更大的项目中工作所带来的一切:规划、管理以及最终的部署和维护。

### 任务

简要的总结是，我必须设计和原型的解决方案，并可以提供帮助英国鞋店。

他虚构的鞋店有一个过时的每季度更新的网站，一个处理所有客户支持问题的朝九晚五的呼叫中心，以及几乎不存在的社交媒体或广告。

[![](img/c6f65bebf609a1662d3bac0aa471aeff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IYwn7tCA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/671/1%2AZvuSn6Inbb5t3zXwy4MBWg.png)

[![](img/c268eaecae80f52334b81e4045d0dd47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yQutcASE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ah_zM5NoBdDXEtRM0Zf0rAw.png)

### 我最初的想法

首先，也是最明显的是提供一个新的网站。简报讨论了对移动应用程序的需求，但应用程序的参与度正在下降。普通美国人在一个月内不会安装任何应用程序，这可能是由于移动友好网站和渐进式网络应用程序的增加。

可以集成一个聊天机器人来处理大部分的客户服务请求，比如退货和交货变更。这让支持人员有时间去处理实际问题，同时提供了一个全天候处理这些请求的方法。再也不用等到周一早上 9 点，只为了等上 3 个小时！

聊天机器人还可以进一步开发。像 [foodie](https://medium.com/@MarutiTech/how-to-order-food-conveniently-through-chatbot-12f2cfa82f59) 这样的机器人允许顾客通过机器人聊天点餐。这可以用来帮助客户找到他们喜欢的产品，并通过信息服务或网站本身订购。如果需要的产品没有库存，这个机器人也可以用来寻找替代品。

#### 其他货色

除了 web 开发之外，还有所有关于敏捷商业实践、广告、社交媒体、客户服务和员工培训的东西，但我会把这些从这篇文章中去掉。

### 如何原型

[![](img/025f753d704d0645d3e6ac184f41a4a1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L2ITbRw---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/638/1%2Axj4fBOgIMREUucPYxI3RLQ.jpeg)

我决定要努力做好这件事。我想知道你是如何设计原型的。这是我找到并使用的流程。

1.  定义**人物角色、问题场景**和**用户故事。**
2.  人物角色帮助你理解客户
3.  问题场景是人物角色可能遇到的问题
4.  用户故事是你的产品解决这些问题所需要做的事情。比如一个需求的简化描述。
5.  找到具有相似角色、问题场景和用户故事的现有解决方案。将此作为你原型的*灵感*(尽可能复制和窃取)。
6.  一边走一边测试你的用户故事。
7.  每个用户故事都应该定义一个你想在应用中使用的特性。

### 我的过程

#### 1 —定义**人物角色、问题场景**和**用户故事。**

**人物角色**

马克，34 岁，是一名超市服务员。喜欢浏览鞋子，晚上看电视时经常用 iPad。

53 岁的玛丽是一名经理。她所有的网上购物都是在电脑上进行的。知道她想要什么，并希望尽可能简单。工作很长时间，早上 8 点出门，晚上 7 点才回家。

杰斯，19 岁，大学生。几乎全靠手机、脸书和推特生活。喜欢翻看自己喜欢的鞋子，但无法证明全价品牌鞋的合理性。

**问题场景**

由于我的笔记本电脑没电了，我希望能够用手机点餐。

我订的鞋子不合脚，而且是周五晚上。我想在鞋子打折或者打折的时候买。

网站坏了。我该怎么告诉他们？

**用户故事**

我可以登录一个账户。
T5 我可以更改我的账户详情。我可以按类别浏览鞋子。

我能找到某品牌适合我尺码的鞋子。

我可以在手机或者平板上浏览。我可以搜索我想要的鞋子。我可以在我的篮子里放一只鞋。

我可以结账，通过贝宝或卡支付。

我可以随时查看我现有的订单。

我可以随时整理退货。

### 2 —找到相似的解决方案并复制

这很容易，因为该产品是一个电子商务平台，有数百万可供选择。我选了一个我以前用过的，看起来不错的——Asos。

我想说我不是设计师。我以前试过从头开始设计网站，它们看起来很丑！这次我听从了毕加索的建议:

> 好的艺术家临摹；伟大的艺术家偷窃——毕加索

这里有几个截屏来说明我为什么选择 Asos。

[![](img/eb49d0c353a378aaf0166a4f6375aafb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_KK5mnj6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/363/1%2A64x8AoDwKi3kjim4Oi1xcA.png)

[![](img/299761e4e920f387e2e1cb2a3383a2da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YjYwZ1B7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/383/1%2AzYE3NEIW6oA6P0ABgsd2Xw.png)

[![](img/cc882959a97ef047a9762112654d1cad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sN6X8zhs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/439/1%2ADWTg2bI_e3dgD7cygc3PlQ.png)

[![](img/efef0ce7da7fd5198c3cd3d977ffc9a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fSlAMQ_1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APZVaBTdoiahiZfXFGGGazw.png)

[![](img/ebd744f867615cb09fa19ce6dea0dde5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Q5eK52Gh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5pGfxPUsT5WONhlyFRXg0w.png)

[![](img/29744a6baab7b4a980630cd91ff2195d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t9YwpF7v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkMvjuQc8ibnL2CAZ1JlD1g.png) 

<figcaption>简洁的设计和移动友好</figcaption>

这些屏幕截图显示了桌面和移动模式下的网站。它们有很棒的移动设计功能，比如搜索过滤器是从右边滑入的，进一步滑动以选择值。以此为指导，我应该能做出好看的产品。

#### 电子商务样板文件

我的第一个计划是为 React 和 Node 找到一个现有的电子商务样板，并在那里修改它。我在 Insoft 上找到了[这个样板文件](https://github.com/insoftpub/storefront),并进行了测试。这是一些令人印象深刻的代码，我通过阅读它学到了很多东西，但我决定不使用它。

我选择从头开始做的主要原因是，这是为了展示我能做什么和我知道什么，而不仅仅是为了能够将 css 添加到别人的代码中。如果我把它作为一个商业产品来做，我可能会想用样板文件来加速这个过程，但这次不会。

当我完成这个项目时，我计划删除项目特定的代码，这样我就有了自己的样板文件，可以用于未来的项目。

### 其实在做一些编码

为了启动这个项目，我从 Google oAuth 开始。我没有真正的理由这样做，除了这是我在最近几个项目中所做的。这也意味着建立我的数据库(MongoDB ),并在我的谷歌开发者仪表板中配置应用程序。这样做了几次后，我对自己正在做的事情充满信心。

接下来是基本的前端。我使用 create-react-app 进行设置，然后清空了 *src* 文件夹。我从另一个项目中复制了一个标题，设置了路径并运行了我的网站。我运行了谷歌登录，它的工作。

用户故事—我可以登录一个帐户—完成！

### 创建主页

我真正想做的第一件事是做好主页。这将是一个快速的工作，并会使我的项目启动有点好。

我得到了一些鞋子的库存图片，并写道“你的鞋子会带你去哪里？”有男女分类的链接。按照我的要求把它布置出来比我想象的要困难得多。在过去的 6 个月里，我大部分时间都在关注后端技术，并没有真正做任何 CSS。

我努力让两个性别选项相邻，然后在屏幕更小的时候让它们换行。我在谷歌上搜索了很多我以前肯定能做的事情，但最终我还是把它们整理好了。

我最终只使用了 row 和 col，因为这是让它在 web、平板电脑和移动设备上工作的最简单的方法。