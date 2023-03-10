# 将 Angular-CLI 应用程序与电子-种子集成

> 原文：<https://dev.to/michaeljota/integrating-an-angular-cli-application-with-electron-34mi>

# 将你的项目与电子集成

Electron 是一个使用 HTML、CSS 和 JS 等 web 技术开发桌面应用程序的平台。将 ng-cli 应用程序与 electronics 集成起来似乎更简单。

## 电子的双包结构

如果你熟悉电子，你可能记得这两种封装结构。我们将使用一个相似的结构，但是有一个关键的不同:在一个常规的双包结构中，`/app/`文件夹将包含电子应用程序。我们将这个文件夹命名为`/electron/`，另一个`/app/`文件夹将包含我们编译的 Angular 应用程序。

看起来我们使用了两个封装结构，但我们只是将电子部分与应用程序的其余部分分开。

## app 配置的变化

*   在我们的 Angular 应用程序`package.json`中创建一个脚本。

```
"electron:start":  "ng build --watch -op=electron/app" 
```

Enter fullscreen mode Exit fullscreen mode

这将在我们的电子文件夹应用程序中的应用程序文件夹内构建 Angular 应用程序，并将在每次更改时重建。

*   将新的输出文件夹添加到`/electron/app`的`#compiled output`部分的`.gitignore`中。
*   修改我们 Angular app 的`index.html`的`base`标签，如下:

```
<base href="./"> 
```

Enter fullscreen mode Exit fullscreen mode

在斜线前添加一个点将允许电子查找服务的文件，并且没有一个`404`错误将被抛出。

这将是所有的修改，我们需要将应用程序与电子集成。

## 创建我们的电子 app

*   我们需要在项目中创建一个`electron`文件夹。在里面，我们将运行`npm init`命令来创建电子`package.json`。这个文件夹将是我们的电子根文件夹。
*   我们添加电子作为开发依赖。

```
npm install -D electron 
```

Enter fullscreen mode Exit fullscreen mode

*   我们为我们的电子程序创建入口点。

*index.js*

```
const { app, BrowserWindow } = require('electron');
const path = require('path');
const url = require('url');

// Keep a global reference of the window object, if you don't, the window will
// be closed automatically when the JavaScript object is garbage collected.
let win;

function createWindow() {
  // Create the browser window.
  win = new BrowserWindow({ width: 800, height: 600 });

  // and load the index.html of the app.
  win.loadURL(
    url.format({
      pathname: path.join(__dirname, 'app', 'index.html'),
      protocol: 'file:',
      slashes: true,
    }),
  );

  // Open the DevTools.
  win.webContents.openDevTools();

  // Emitted when the window is closed.
  win.on('closed', () => {
    // Dereference the window object, usually you would store windows
    // in an array if your app supports multi windows, this is the time
    // when you should delete the corresponding element.
    win = null;
  });
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on('ready', createWindow);

// Quit when all windows are closed.
app.on('window-all-closed', () => {
  // On macOS it is common for applications and their menu bar
  // to stay active until the user quits explicitly with Cmd + Q
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

app.on('activate', () => {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (win === null) {
    createWindow();
  }
});

// In this file you can include the rest of your app's specific main process
// code. You can also put them in separate files and require them here. 
```

Enter fullscreen mode Exit fullscreen mode

## 运行 app

*   打开终端，移动到 Angular 项目，运行`npm run electron:start`启动 Angular 应用程序。

*   打开第二个终端，移动到项目中的`electron`文件夹，运行`electron .`启动电子应用程序。

您应该会看到一个窗口，里面运行着 Angular 应用程序。

## 向前移动

我们现在有一个非常简单的角度应用程序运行在电子渲染器进程中。如果我们修改了角度应用程序或电子应用程序，我们需要关闭电子窗口并再次运行`electron .`。这可以使用插件和构建系统来优化，但这是电子的开箱即用行为。

您可能会注意到，电子应用程序的入口文件是用普通 Javascript 编写的。这是因为 Electron 只运行普通的 Javascript，为了运行其他语言，比如 Typescript，你应该使用一个插件，比如`ts-node`，或者一个构建系统。

## 系列

*   [种子](https://dev.to/michaeljota/integrating-an-angular-cli-application-with-electron-34mi)
*   [带打字稿的电子](https://dev.to/michaeljota/electron-with-typescript-using-ts-node-8oi)
*   [IPC](https://dev.to/michaeljota/integrating-an-angular-cli-application-with-electron---the-ipc-4m18)