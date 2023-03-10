# Angular 教程:使用 ES7 装饰器分离关注点

> 原文：<https://dev.to/k1r0s/angular-tutorial-separation-of-concerns-using-es7-decorators>

本教程旨在创建一个简单的 Angular 应用程序，它解决了“OOP 范例中的一个常见问题，即现实世界中常见的代码重复和隐藏模式，这些问题可以使用控制反转技术来解决，并允许我们执行*声明式编程*。

#### 资源(这篇文章讲的是什么):

*   [https://github.com/k1r0s/angular2-srp-showcase/tree/master](https://github.com/k1r0s/angular2-srp-showcase/tree/master)
*   [https://github . com/k1r0s/angular 2-SRP-showcase/tree/normal-OOP](https://github.com/k1r0s/angular2-srp-showcase/tree/normal-oop)

### App

*   该应用程序应该在其主页中加载一个用户列表。
*   如果一个列表元素被点击，将会出现一个对话框
*   这个对话框应该有一个关于所选用户的简介和两个按钮
*   一个按钮应该可以关闭对话框
*   其他按钮应加载用户特定的“帖子页”
*   点击来自“南猫王”的用户将触发随机异常
*   应缓存异常，应显示错误对话框[2]
*   帖子页面应呈现所选用户的特定**帖子**

[1]用户对话格式/内容不属于本教程的范围

[2]错误对话框格式/内容不属于教程的范围

规则

*   每次我们执行一个 ajax 请求时，我们必须显示一个加载对话框
*   出于缓存目的，所有 ajax 请求都应该被存储

技术细节

用户(作家)资源放在这里:[https://jsonplaceholder.typicode.com/**用户**T3】](https://jsonplaceholder.typicode.com/users)

帖子资源放在这里:[https://jsonplaceholder.typicode.com/**帖子**T3】](https://jsonplaceholder.typicode.com/posts)

### 我们开始吧

你可以按照这些指示，一步一步来。

**准备好你的工作空间**

*   $ git 克隆[https://github.com/k1r0s/angular2-srp-showcase.git](https://github.com/k1r0s/angular2-srp-showcase.git)
*   $ git checkout normal-oop
*   $ npm 安装

**运行代码**

*   $ npm 开始
*   浏览本地主机:4200

**读取代码**

好的，让我们从打开 RC/app/components/writers/writers . component . ts 开始。该部分具有以下职责:

*   调用服务来获取用户
*   呈现用户列表
*   监听用户列表上的点击
*   调用服务以存储用户请求结果和要加载到“帖子屏幕”上的选定用户
*   调用服务以构建一个对话框来呈现所选用户
*   如果所选用户来自“南猫王”，则处理异常

现在让我们看看 src/app/components/user-posts/user-posts . component . ts。这个人有以下责任:

*   从缓存中抓取选定的用户
*   调用服务来获取用户的特定帖子
*   呈现帖子列表
*   调用服务来存储特定用户帖子请求结果

### 常见 OOP 驱动重复:

在 OOP 中，每个方法都是一个与域相关的动作或动词。

横切关注点是一段段代码，通过将基础设施关注点与领域关注点混合在一起，无法理解这个**动作**真正在做什么。

```
 // stuff that matters

  // this stuff only represents "SHOW A DIALOG, DOMAIN SPEAKING"
  // 
  this.dialogRef = this.dialogFactory.open( 
    dialogThatNeedsToBeShowed,
    { data: dialogsRequiredData }
  )

  // stuff that matters 
```

Enter fullscreen mode Exit fullscreen mode

我们来看看[https://github . com/k1r0s/angular 2-SRP-showcase/blob/normal-OOP/src/app/components/writers/writers . component . ts # L41](https://github.com/k1r0s/angular2-aop-showcase/blob/normal-oop/src/app/components/writers/writers.component.ts#L41)的代码