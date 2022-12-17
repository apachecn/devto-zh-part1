# 通过构建降价应用程序开始使用 Ionic 2

> 原文：<https://dev.to/amit_merchant/getting-started-with-ionic-2-by-building-a-markdown-app>

读者你好！今天我们将深入探讨一下(或在这种情况下)。我们将通过构建一个简单的降价应用程序来了解这一点，该应用程序可以让用户在移动设备上随时预览降价文本。我们将在考虑 Android 平台的情况下构建这个应用程序。

那么，首先*“什么是离子？”*你问。

从它的官方网站，**来看，“Ionic 是漂亮的、免费的、开源的移动 SDK，用于轻松开发本地的、渐进式的网络应用。”**有了 Ionic，你可以使用你已经知道的技术来构建移动应用。没错！都是 HTML，CSS，JavaScript。

是的，是的，我听到你问*“我们已经有了像 [Phonegap](http://phonegap.com/) 这样的框架，为什么还需要 Ionic？”*答案是，像 Phonegap 这样的框架是使用 [Cordova](https://cordova.apache.org/) 构建系统(它们是相当同义的)，而 Ionic 是一个基于 [AngularJS](https://angular.io/) 的应用开发平台，具有谷歌的 [Material Design](https://material.io/) UI，使用 Cordova 为移动平台包装自己。除了在其核心使用 AngularJS，离子还促进..

*   构建渐进式 web 应用程序的功能
*   Live Reload 在开发的每一步都编译和重新部署你的应用程序，这是为傻瓜准备的
*   AoT 编译使得 ionic 应用程序加载速度快如闪电

在本教程中，我们将使用 Ionic 2 来制作我们的 Markdown 应用程序，Ionic 2 在其核心中使用了 [Angular 2](https://angular.io/) 。要在本地构建一个 Ionic 应用，你只需要在你的电脑上安装最新版本的 [Node.js](https://nodejs.org/en/) ，一个最新的浏览器(最好是 Chrome)和一个你选择的文本编辑器。听起来很刺激？让我们开始吧。

#### 安装离子

Ionic 2 应用程序主要通过 Ionic 命令行实用程序(“CLI”)创建和开发，并使用 Cordova 作为本机应用程序进行构建和部署。这意味着我们需要安装一些实用程序来进行开发。

#### 离子 CLI 和科尔多瓦

要创建 Ionic 2 应用程序，你需要安装最新版本的 Ionic
CLI 和 Cordova。安装 Ionic CLI 和 Cordova 进行本地应用开发:

```
 $ npm install -g ionic cordova 
```

Enter fullscreen mode Exit fullscreen mode

这将需要一些时间来安装和准备使用。

> 您可能需要在这些命令前面添加“sudo”来全局安装实用程序，或者在 Windows 的情况下，您需要在管理员模式下打开命令提示符。在脆弱的网络中安装`Ionic`时，你可能会出错，但坚持下去，你肯定会安装它。

一旦安装了`ionic`和`cordova`，我们可以使用下面的命令生成一个基本的 Ionic 应用:

```
 $ ionic start ionic-markdownify --v2 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，我们添加了–v2，因为我们希望使用 Ionic 2 构建我们的应用程序。如果您想使用 Ionic 1 构建应用程序，请省略–v2。

这将生成一个名为的文件夹，其文件夹结构如下:

```
.
+-- hooks
+-- node_modules
+-- plugins
+-- resources
|   +-- android
|   +-- ios
|   +-- wp8
+-- src
|   +-- app
|   +-- assets
|   +-- pages
|       +-- about
|       +-- contact
|       +-- home
|       +-- tabs
|   +-- theme
+-- www
+-- .editorconfig
+-- .gitignore
+-- .io-config.json
+-- config.xml
+-- ionic.config.json
+-- package.json
+-- tsconfig.json
+-- tslint.json 
```

Enter fullscreen mode Exit fullscreen mode

目录包含了我们的应用程序将要使用的所有页面。在我们的应用程序中，我们将只处理`home` dir。

要运行我们的应用程序，请进入创建的目录，然后运行`ionic serve`命令，在浏览器中测试您的应用程序！

```
 $ cd ionic-markdownify 
    $ ionic serve 
```

Enter fullscreen mode Exit fullscreen mode

这将启动我们的应用程序，我们可以通过 [http://localhost:8100 看到我们的应用程序在运行。](http://localhost:8100./)这是一个基于选项卡的基本应用程序，如下所示:

[![](img/de8511e5e5a0cfddfff502eea5ad7811.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--syoS_Js---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2ABII77Y1WBJVoF46J.png)

接下来，为了制作 Markdown 应用程序，我们将首先修改我们的文件`src/pages/home/home.html`。我们将把`<ion-content></ion-content>`中的内容替换为:

```
<h2>Welcome to Markdownify!</h2>
<ion-item>
  <ion-textarea rows="6" [(ngModel)]="plainText" placeholder="Strat writing your markdown below..."></ion-textarea>
</ion-item> 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，我们使用了`Ionic`的内置文本区组件`<ion-textarea>`，它在特定的操作系统环境(在我们的例子中是 Android)中为文本区提供了原生的触感。我们还使用`[(ngModel)]="plainText"`绑定了`ion-textarea`，这将帮助我们在`src/pages/home/home.ts`中获取它在`class HomePage`中的值

接下来，我们将添加一个切换按钮，用于在 Textarea 和
Markdown 预览之间切换。补充一下吧。

```
<h2>Welcome to Markdownify!</h2>
<ion-item>
  <ion-label>Convert to Markdown</ion-label>
  <ion-toggle></ion-toggle>
</ion-item>
<ion-item>
    <ion-textarea rows="6" placeholder="Strat writing your markdown below..."></ion-textarea>
</ion-item> 
```

Enter fullscreen mode Exit fullscreen mode

在这之后，我们将在现有的一个之后添加另一个`<ion-content></ion-content>`,我们将使用它来保存降价的 HTML 预览。我们将在这个组件中添加`[hidden]="!toggleVal"`,以便仅在切换状态改变时显示。我们对`<ion-textarea>`所在的`<ion-item>`也做了同样的事情。布线后，所有这些`src/pages/home/home.html`将看起来像这样:

```
<ion-header>
  <ion-navbar>
    <ion-title>Home</ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
  <h2>Welcome to Markdownify!</h2>
  <ion-item>
    <ion-label>Convert to Markdown</ion-label>
    <ion-toggle color="secondary"></ion-toggle>
  </ion-item>

  <ion-item [hidden]="toggleVal">
    <ion-textarea rows="6" placeholder="Strat writing your markdown below..."></ion-textarea>
  </ion-item>
  <ion-content [hidden]="!toggleVal">
    <div [innerHtml]="content"></div>
  </ion-content>
</ion-content> 
```

Enter fullscreen mode Exit fullscreen mode

为了使我们的`<ion-textarea>`达到最大高度，我们将在`src/pages/home/home.scss`中添加以下 CSS 代码:

```
textarea {
  max-width: 100%;
  width: 100%;
  height: 100vh;
} 
```

Enter fullscreen mode Exit fullscreen mode

在此之后，我们将添加`[(ngModel)]="toggleVal"`到`<ion-toggle>`来跟踪相同的值，还将添加`(ionChange)="convert()"`来跟踪切换的变化。

此时，我们的应用程序的`Home`选项卡将如下所示:

[![](img/a62b0b344ac4aee1e9e7d77067705278.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZDBpMIh5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AhA9YImcUX6CpaCxY.png)

然后我们将把`convert()`函数添加到`home.ts`中，如下:

```
convert(this) {
    if(this.toggleVal==true){
      if(this.plainText && this.plainText!=''){
        let plainText = this.plainText

        this.markdownText = marked(plainText.toString())
        this.content = this.markdownText
      }else{
        this.toggleVal=false
      }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，这个函数将检查开关的当前状态，并基于此将 Markdown 转换成相关的 HTML。还要注意，我们已经基于 textarea 的值维护了
> 切换的状态。

为了将 Markdown 转换成 HTML，我们将使用`marked`库。要安装它，请在 CLI 中启动以下命令:

```
 $ npm install marked --save 
```

Enter fullscreen mode Exit fullscreen mode

这将把`marked`安装到我们的项目中。为了在我们的应用程序中使用它，现在让我们在`src/pages/home/home.ts`
的顶部添加下面一行

```
import marked from 'marked'; 
```

Enter fullscreen mode Exit fullscreen mode

除此之外，使用`<div [innerHtml]="content"></div>`的引用，我们将从`@angular/core`
添加 Angular 的 [ViewChild](https://angular.io/docs/ts/latest/api/core/index/ViewChild-decorator.html)

```
import { Component, ViewChild, Input } from '@angular/core'; 
```

Enter fullscreen mode Exit fullscreen mode

并将它添加到*班级主页*如下:

```
 @ViewChild(Content) content: Content; 
```

Enter fullscreen mode Exit fullscreen mode

在添加了所有这些之后，我们的`src/pages/home/home.ts`在这一点上会看起来如下:

```
import { Component, ViewChild  } from '@angular/core';
import { Content } from 'ionic-angular';

import { NavController } from 'ionic-angular';
import marked from 'marked';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  @ViewChild(Content) content: Content;

  constructor(public navCtrl: NavController) {
    //console.log(marked('I am using __markdown__.'));
  }

  convert(this) {
    if(this.toggleVal==true){
      if(this.plainText && this.plainText!=''){
        let plainText = this.plainText
        this.markdownText = marked(plainText.toString())
        this.content = this.markdownText
      }else{
        this.toggleVal=false
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这基本上概括了我们整个 app。现在，在你的浏览器中点击 [http://localhost:8100](http://localhost:8100/) ，你会看到我们漂亮的小应用程序正在运行！

[![](img/6c2ce9872ab969475ee79348f9a07494.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_M8OQPpI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AM7xvK-2UXuWJ4U9V.gif)

你也可以在[这里](https://github.com/amitmerchant1990/ionic-markdownify)查看这个应用的整个代码库。

本教程的整体思想是通过构建一个真实世界的应用程序让您开始使用 Ionic 2，并理解 Ionic 2 的一些概念。你可以改进这个特别的应用程序。一些改进包括..

*   实现滑动手势，以摆脱切换，使用户只需向左滑动，以获得预览。
*   实现 Markdown 的编辑工具，如粗体、斜体、下划线、代码等。
*   实现一个文本编辑器来代替文本区域。

您还可以使用 [Ionic Package](http://blog.ionic.io/build-apps-in-minutes-with-ionic-package/) 为您喜欢的任何平台(Android、iOS、Windows Phone OS)打包您新创建的应用程序并进行分发。

要了解更多关于 Ionic 2 的信息，你可以遵循这个[文档](http://ionicframework.com/docs/v2/components/#overview)并通过一大堆 Ionic 2 组件进行调整。

感谢阅读。