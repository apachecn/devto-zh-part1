# 将角度 CLI 应用与电子集成 IPC

> 原文：<https://dev.to/michaeljota/integrating-an-angular-cli-application-with-electron---the-ipc-4m18>

# 前情提要...

在前面的章节和故事中，我解释了如何将 Angular-CLI 生成的应用程序与 electronic 集成，以及如何用 Typescript 编写相同的电子应用程序。到目前为止，这将允许一个简单的角度应用被包装成一个电子应用，但我们没有办法与电子的主线互动。

*   [种子](https://dev.to/michaeljota/integrating-an-angular-cli-application-with-electron-34mi)
*   [带打字稿的电子](https://dev.to/michaeljota/electron-with-typescript-using-ts-node-8oi)

# 简介

在这篇文章中，我将试着解释如何在角和电子之间得到一个真正的积分，并且能够使用电子的 IPC 进行通信。

## 什么是 IPC？

IPC 是电子设备的进程间通信模块。这样你就可以在你的 web 应用程序和主线程应用程序之间发送消息。要在主线程中发送和接收消息，您必须使用 [ipcMain](https://electronjs.org/docs/api/ipc-main) function 属性。同样，对于渲染器进程，您可能希望使用 [ipcRenderer](https://electronjs.org/docs/api/ipc-renderer) 。

## 如何使用 ipcMain？

使用 ipcMain 是一个简单的 as require it，并使用功能之一，能够为我们。

```
import { ipcMain } from 'electron';

ipcMain.on('ping', (event) => {
    logger('ping'); // Assume there is a logger function that would display 'ping' in console. console object does not work as-is, and it's not so easy to configure it.
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，应用程序将监听一个`'ping'`事件，并在控制台中输出`ping`。很简单。

## (正常情况下)如何使用 ipcRenderer？

在正常情况下，一个简单的`require('electron').ipcRenderer`会让我们访问渲染器线程中的 IPC。因此，按照前面的例子，我们可以做类似于:
的事情

```
const { ipcRenderer } = require('electron');

ipcRenderer.send('ping'); 
```

Enter fullscreen mode Exit fullscreen mode

这将调用主线程中的回调函数。

但是，这在 Angular-CLI 应用程序中不起作用。Angular-CLI 底层使用 Webpack，因此后者将找到`require` *键*字，解释为对 Node.js' `require`函数的调用，并将尝试解析“电子”模块。

## 关于两个`require`年代的故事

`require`自从 2008 年 Node 的第一个版本问世以来，已经有将近 10 年的时间了。但仍然是现代 web 开发中最容易被误解的功能之一。随着`import`和`export`关键字在 Node 中的集成，撰写了几篇文章来解释`require`函数目前是如何工作的。TL；DR:为每个文件注入一个`require`函数，允许节点解析依赖关系。稍后，模块构建者将寻找`import`和`require`，并尝试解析模块，假设这是您想要的。

所以，现在我们知道`require`实际上是一个由节点注入的函数，那么`require`是如何能够在电子渲染器进程中工作的。你可能猜到了，当加载页面时，Electron 在渲染器进程的全局范围内注入了自己版本的`require`函数。因此，尽管它看起来是相同的功能，但它不是。

## 如何在 Angular-CLI 应用中使用 ipcRenderer？(或任何与 Webpack 捆绑的应用程序)

为了在我们的 Angular-CLI 应用程序中使用`ipcRenderer`,我们将利用全局范围。不，我们不会在全局上下文中调用 ipcRenderer，尽管我们可以这样做，但这并不理想。但我刚才说过`require`是电子在全局背景下注入的函数。那么，我们可以只用`require`吗？不。那是因为，正如我也说过的，Webpack 会尝试解决模块需求。实际上还有另一种访问全局变量的方法，那就是使用`window`对象。默认情况下，`window`对象将拥有所有的全局变量，包括`require`。

所以我们可以在应用程序的任何部分使用`window.require`,它会像预期的那样工作。在这种情况下，您不能使用`window.require`在您的 Angular-CLI 应用程序中要求任何模块，但您可以加载您在电子应用程序中设置的任何模块。

## 编写服务

对于这个例子，我们将把`ipc`公开为 Angular 服务，并使用 angular-cli 创建它。我们跟随导游讲述[服务](https://github.com/angular/angular-cli/wiki/generate-service)T3】

```
ng generate service ipc -m app 
```

Enter fullscreen mode Exit fullscreen mode

这将创建我们的服务，并更新我们的`app.module`以将其包含在 Angular 应用程序中。

然后，我们编写代码。我们从从电子模块
导入 IpcRenderer 接口开始

```
import { IpcRenderer } from 'electron'; 
```

Enter fullscreen mode Exit fullscreen mode

但是，在我们的角度项目中没有任何电子模块，这将如何解决？实际上，我们不需要在 Angular 项目中有电子模块，因为当 Typescript resolver 工作时，它会在我们项目的子文件夹中查找`node_modules`。如果你想要额外的安全，或者如果出于任何原因这不是一个期望的行为，你可以安装电子的`@types`，它不会加载孔包。

```
npm install @types/electron 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们在类中添加一个引用属性，以便在加载时保存 ipcRenderer 函数。

```
 private _ipc: IpcRenderer | undefined; 
```

Enter fullscreen mode Exit fullscreen mode

重要的是将其输入为`IpcRenderer | undefined`以便在严格模式下编译代码，因为我们可能会也可能不会加载 ipcRenderer。我们现在编写构造函数，在加载时分配`_ipc`。

```
 constructor() {
    if (window.require) {
      try {
        this._ipc = window.require('electron').ipcRenderer;
      } catch (e) {
        throw e;
      }
    } else {
      console.warn('Electron\'s IPC was not loaded');
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们将首先检查`window`对象是否有一个`require`属性。用这个我们将假设我们在电子内部，然后我们将`try`到`require('electron')`，如果因为任何原因它不工作，它只是抛出一个错误，属性`_ipc`将是未定义的。选中`window`对象中的 require 将允许我们在常规的浏览器环境中运行服务，在这种情况下`_ipc`将没有赋值，而是为`undefined`。

您应该有 Typescript 抱怨`window`没有`require`属性，所以我们需要更新项目的 typings 文件。打开`/src/typings.d.ts`，用下面几行更新:

```
interface Window {
  require: NodeRequire;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，Typescript 不应该困扰我们。

我将为服务添加几个函数，只是为了测试它是否真的如预期的那样工作。

```
 public on(channel: string, listener: Function): void {
    if (!this._ipc) {
      return;
    }
    this._ipc.on(channel, listener);
  }

  public send(channel: string, ...args): void {
    if (!this._ipc) {
      return;
    }
    this._ipc.send(channel, ...args);
  } 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，在这两个例子中，我们检查要分配的属性`_ipc`，然后我们调用我们想要调用的函数。我们公开了我们要调用的函数的同一个函数接口，所以从我们的应用程序中调用它们会非常直观。

最终的服务应该是这样的:

```
import { Injectable } from '@angular/core';
import { IpcRenderer } from 'electron';

@Injectable()
export class IpcService {
  private _ipc: IpcRenderer | undefined = void 0;

  constructor() {
    if (window.require) {
      try {
        this._ipc = window.require('electron').ipcRenderer;
      } catch (e) {
        throw e;
      }
    } else {
      console.warn('Electron\'s IPC was not loaded');
    }
  }

  public on(channel: string, listener: IpcCallback): void {
    if (!this._ipc) {
      return;
    }
    this._ipc.on(channel, listener);
  }

  public send(channel: string, ...args): void {
    if (!this._ipc) {
      return;
    }
    this._ipc.send(channel, ...args);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

## 试探一下

为了测试，我们将调用一个 ipc 信道，并使电子对我们做出响应，并监听该响应。

首先，我们将使用下面的构造函数更新我们的`app.component`:

```
 constructor(private readonly _ipc: IpcService) {
    this._ipc.on('pong', (event: Electron.IpcMessageEvent) => {
      console.log('pong');
    });

    this._ipc.send('ping');
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将更新 Electron 的`index.ts`文件，导入`ipcMain`模块并为`ping`事件设置一个监听器，即响应`pong`。

```
// First we update the import line
import { app, BrowserWindow, ipcMain, IpcMessageEvent } from 'electron';
...
ipcMain.on('ping', (event: IpcMessageEvent) => {
  event.sender.send('pong');
}); 
```

Enter fullscreen mode Exit fullscreen mode

用`npm run electron:start`运行角度应用程序，在电子应用程序中运行`npm start`。您应该会看到一个乒乓记录。

[![pong](img/e7fdcb7683dd2057ac7a15de1b66ee49.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yFbCjhTE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xfyeh5gf2dvz66o54qdk.png)

## 向前移动

在当前的工作流程中仍然有一些可以改进的地方，而且你们中的一些人在使用原生模块时遇到了麻烦。但是，到目前为止，我们已经通过一个简单的角度应用程序，到一个完整的角度/电子集成。我很快会把这些上传到 Github，并在那里保持关注。

## 这就是所有的乡亲

像往常一样，感谢你阅读这篇文章，看看我的其他帖子。献出你的爱，分享给你的朋友。下次见。