# 用 MoonJS 创建简单的概要应用程序

> 原文：<https://dev.to/goodnesskay/creating-a-simple-profile-application-withmoonjs>

你好！MoonJs 是一个相对较新的 Javascript 框架，我看到没有教程，我决定尝试一下，然后写一个关于它的基础教程。因此，就像我们对反应的预测一样，我们对反应的预测也是“月亮”。MoonJs 的一些关键之处在于，像其他一些 javascript 框架一样，它非常轻便。Moon 使用快速虚拟 DOM，并且可以有效地重新呈现 DOM，最重要的是，MoonJs 很容易学习。
我将构建一个简单的应用程序来显示用户的基本信息，本教程将为使用 MoonJs 提供一个良好的开端。

## 要求

*   JavaScript 知识
*   文字编辑器

## 安装

要安装 MoonJs，有两种方法。一种方式是通过 [Moon-cli](https://github.com/KingPixil/moon-cli) 另一种方式是通过内容交付网络(CDN)，可以在[这里找到](http://moonjs.ga/docs/installation.html)。要设置好我们的项目，请运行以下命令:

```
mkdir moon-tutorial
cd moon-tutorial 
```

以下命令将创建一个名为 moon-tutorial 的新文件夹，并检查目录。这样做之后，在你最喜欢的文本编辑器中打开你的项目，然后创建一个名为 app.html 的文件，然后添加代码行

```
<!DOCTYPE html>
<html>
<head>
 Moon Tutorial
 <meta charset="UTF-8">
 <!-- Development Build -->
 <script src="https://unpkg.com/moonjs/dist/moon.js"></script>
</head>
<body>
</body>
</html> 
```

## 加入月饼的味道

创建一个名为 src 的文件夹，并在其中创建另一个名为 js 的文件夹，然后在该文件夹中创建一个名为 app.js 的文件。在该文件中，我们将启动一个 MoonJs 实例来显示“Hello Pusher of Codes”。我们将通过向 app.js 文件添加以下代码来实现这一点。

```
const moon = new Moon({
  el: ".app",
  data: {
    message: "Hello Pusher of Codes!"
  }
}); 
```

更新您的 app.html，如下所示:

```
<!DOCTYPE html>
<html>
<head>
 Moon Tutorial
 <meta charset="UTF-8">
 <!-- Development Build -->
 <script src="https://unpkg.com/moonjs/dist/moon.js"></script>
</head>
<body>
<div class="app">
  <h1 style="text-align:center;">{{message}}</h1>
</div>
<script src="src/js/app.js"></script>
</body>
</html> 
```

有了上面的内容，您应该会看到类似下面这样的内容:

[![](img/16ae468a891b81dd912ac154bf955299.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JSLJKDuB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/32839/WLyTjSu6TvKKXTF8GlOb_m1.png)

## 建立简单的轮廓

让我们用更多关于用户的信息使我们的应用程序看起来更好。将以下代码添加到 app.html 文件中:

```
<!DOCTYPE html>
<html>
<head>
 Moon Tutorial
 <meta charset="UTF-8">
 <!-- Development Build -->
 <script src="https://unpkg.com/moonjs/dist/moon.js"></script>
</head>
<body>
<div class="app">
  <h1 id="name">{{full_name}}</h1>
  <p id="email">{{email}}</p>
  <p id="phone">{{mobile_number}}</p>
  <p id="description">{{short_info}}</p>
</div>
<style type="text/css">
 .app{
  margin-top:200px;
 }
#name{
  text-align:center;
  color: blue;
 }
#email{
  text-align:center;
  color: blue;
 }
#phone{
  text-align:center;
  color: blue;
 }
#description{
  text-align:center;
  color: blue;
 }
</style>
<script src="src/js/app.js"></script>
</body>
</html> 
```

然后，使用下面的代码对 app.js 文件进行更改

```
const moon = new Moon({
  el: ".app",
  data: {
    full_name: "Goodness Kayode | Pusher of Codes!",
    email:"gtkbrain@gmail.com",
    mobile_number:"+2341188469520",
    short_info:"I am Goodness Kayode also called Pusher of Codes. Linguist turned engineer.Community lover"
  }
}); 
```

Yupp！！！有了这些，你应该可以开始用 MoonJs 创建应用程序了。
[![](img/fe0a5523b64b8ef9a66f2e57d7144bd9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z4_XhTEV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.scotch.io/32839/l5P0COCRD6cTJfZ4GxQ8_m2.png)

## 结论

我希望你喜欢这个教程？我只是触及了框架的表面，但本教程将帮助你如何设置一个 MoonJs 应用程序，以及如何用框架编写一个基本的应用程序。
到存储库的链接是这里的