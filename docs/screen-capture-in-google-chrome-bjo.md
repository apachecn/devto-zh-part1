# 谷歌浏览器中的屏幕截图

> 原文：<https://dev.to/twilio/screen-capture-in-google-chrome-bjo>

要在 WebRTC 视频聊天应用程序中构建屏幕共享功能，您必须首先能够捕捉屏幕上的内容。在这篇文章中，我们将看到如何在一个网络应用程序中使用谷歌浏览器的截屏功能。

[![](img/f18720517a189494d984d93a022d6bcf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wBmGc0B3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0vprn83g9ixzeib0rk7z.png)

## 你需要什么

为了构建这个项目，您需要:

*   [谷歌浏览器](https://www.google.com/chrome/index.html)
*   文本编辑器
*   本地 web 服务器——我喜欢用 [servedir](https://www.npmjs.com/package/servedir) 来做这样的事情

## 屏幕共享

目前，Chrome 不允许你通过`mediaDevices` API 访问屏幕内容。有一个针对`getDisplayMedia`方法的[草案规范，但是还没有浏览器实现它。对于最终用户来说，网络上的屏幕共享有很多](https://w3c.github.io/mediacapture-screen-share/)[安全问题](http://www.ietf.org/mail-archive/web/rtcweb/current/msg06755.html)，所以浏览器正在小心处理这个特性。

那我们现在能做什么？

嗯，在 Chrome 中，我们可以编写一个扩展，让我们能够访问屏幕、应用程序窗口和浏览器标签。Chrome 扩展具有扩展的权限和 API，允许访问页面上常规 JavaScript 不能访问的资源。

让我们编写一个扩展来访问屏幕截图，然后在页面上显示结果。

## 构建一个 Chrome 扩展

我们将要构建的扩展非常简单。它由两部分组成:一个描述扩展的`manifest.json`文件和我们想要运行的脚本，我们称之为`extension.js`。

### 扩展清单

创建一个新目录来构建我们的应用程序，一个用于扩展本身的目录，以及文件`manifest.json`和`extension.js`。

```
mkdir screen-capture
cd screen-capture
mkdir extension
touch extension/manifest.json extension/extension.js 
```

Enter fullscreen mode Exit fullscreen mode

在您最喜欢的文本编辑器中打开`manifest.json`。我们需要向清单添加一些基本的细节:名称、描述、我们的扩展版本以及我们需要的清单文件格式的版本，在本例中是 2。

```
{
  "name": "Desktop Capture",
  "description":
    "Allows you to capture your desktop for use in video applications",
  "version": "0.1.0",
  "manifest_version": 2
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要描述扩展是如何工作的。将以下内容添加到清单中:

```
 "manifest_version": 2,
  "background": {
    "scripts": ["extension.js"],
    "persistent": false
  },
  "externally_connectable": {
    "matches": ["*://localhost/*"]
  },
  "permissions": ["desktopCapture"]
} 
```

Enter fullscreen mode Exit fullscreen mode

这告诉 Chrome 这个扩展实际上做了什么。

首先，它在后台运行文件`extension.js`。`"persistant": false`选项表示我们正在构建一个[事件页面](https://developer.chrome.com/extensions/event_pages)。这意味着当不需要扩展时，它会被卸载，不会占用系统资源。

成为 [`externally_connectable`](https://developer.chrome.com/extensions/manifest/externally_connectable) 意味着该扩展可以接收来自匹配 URL 上的网页的消息。在这种情况下，这是一个开发扩展，将只适用于`localhost`。如果您想将它构建为您站点的扩展，您需要在这里添加您自己的域匹配代码。

最后，权限部分意味着我们想要访问 Chrome 扩展的[桌面捕获 API](https://developer.chrome.com/extensions/desktopCapture) 。当我们为扩展本身编写代码时，我们将看到它是如何工作的。

### 分机代码

要构建事件页面，我们需要创建一个事件监听器。我们将等待来自网页的[消息，因此打开`extension.js`并开始输入:](https://developer.chrome.com/apps/messaging#external-webpage) 

```
chrome.runtime.onMessageExternal.addListener((message, sender, sendResponse) => {

}); 
```

Enter fullscreen mode Exit fullscreen mode

[`chrome.runtime`](https://developer.chrome.com/apps/runtime) 是我们用来响应扩展外部事件以及返回关于扩展本身的其他细节的 API。当从扩展之外接收到消息时，触发`onMessageExternal`,因此我们需要监听它。当我们的侦听器被调用时，它接收三个参数，我们从页面发送的消息，一个 [`runtime.MessageSender`](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API/runtime/MessageSender) 对象和一个我们最多可以使用一次将消息发送回页面的函数。

一旦该事件触发，我们就需要使用 [`chrome.desktopCapture.chooseDesktopMedia`](https://developer.chrome.com/extensions/desktopCapture#method-chooseDesktopMedia) 来弹出屏幕共享选项。我们需要传递一组源来捕获。[这些可以是“屏幕”、“窗口”、“标签”或“音频”](https://developer.chrome.com/extensions/desktopCapture#type-DesktopCaptureSourceType)我们将在发送的消息中从网页传递它们。我们还将发送消息的选项卡传递给扩展。最后，我们需要传递一个回调函数，一旦函数为我们请求访问的流返回一个`streamId`，这个回调函数就会被调用。为了让我们异步使用`sendResponse`函数，我们还需要在侦听器函数的末尾添加`return true`。

```
chrome.runtime.onMessageExternal.addListener((message, sender, sendResponse) => {
  const sources = message.sources;
  const tab = sender.tab;
  chrome.desktopCapture.chooseDesktopMedia(sources, tab, (streamId) => {

  });
  return true;
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们得到了对回调的响应，我们就差不多完成了扩展。我们需要做的就是检查用户是否授权访问屏幕，并使用`sendResponse`将不成功的响应或流的 ID 发送回网页。

```
chrome.runtime.onMessageExternal.addListener((message, sender, sendResponse) =>; {
  const sources = message.sources;
  const tab = sender.tab;
  chrome.desktopCapture.chooseDesktopMedia(sources, tab, (streamId) => {
    if (!streamId) {
      sendResponse({
        type: 'error',
        message: 'Failed to get stream ID'
      });
    } else {
      sendResponse({
        type: 'success',
        streamId: streamId
      });
    }
  });
  return true;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们需要为这个版本的扩展编写的全部内容。现在我们需要在使用它之前安装它。

### 安装分机

在 Chrome 中安装一个你正在开发的扩展来进行测试是非常简单的。只要在浏览器的地址栏输入 chrome://extensions 就可以打开扩展设置页面。

然后，要安装扩展，你需要选中“开发者模式”框，然后选择“加载未打包的扩展…”。从对话框中，导航到保存扩展的目录，并选择整个目录。

上传后，您需要记下您的分机 ID。我们将在下一部分用到它。

[![The Chrome extensions page. Make sure to check the 'Developer mode' check box, then use the 'load unpacked extension...' button to load in your extension. The extension ID is shown in the list of extensions.](img/f3eaaf72451c2ed62355b40f684265a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sylMx7EV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2017/10/a6j_tELs5IdYTELnS_v2W7sroB_PuUNIAVhggHaroRhquoy0YjlWbCT195NvNaZVVBiSRXlWJUFnsZ7r90ICUEyiPysiZ53kqAoWxWD2xEWCG46xur_4f-v_AQv7zA6IrYFpNAY.png)

## 建筑截屏

在这篇文章的剩余部分，我们将会看到如何使用我们的新扩展来获取屏幕截图。现在我们将在网页上的一个`<video>`元素中显示它。在另一篇文章中，我们将研究在视频聊天中使用流，这样我们就可以远程共享我们的屏幕。

首先在项目目录中创建名为`chrome`的目录，并在其中创建一个名为`index.html`的新 HTML 页面。添加以下标记:

```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Screen
</head>

<body>
  <h1>Show my screen</h1>

  <video autoplay id="screen-view" width="50%"></video>
  <button id="get-screen">Get the screen</button>
  <button id="stop-screen" style="display:none">Stop the screen</button>

  <script>
  </script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个基本的 HTML 页面，里面有一个`<video>`元素来显示我们的结果，两个按钮来启动和停止屏幕截图，还有一个`<script>`块，我们将在这里编写其余的代码。

我们将从收集将要使用的 DOM 元素开始编写代码。我们还为稍后将向扩展发出的请求设置了一个对象。请记住，我们可以提供我们想要选择的来源。在这个应用程序中，我们将能够从整个屏幕、应用程序的窗口或 Chrome 的标签中进行选择。您还需要之前将扩展加载到 Chrome 时的扩展 ID。将此添加到您的`<script>`块中:

```
(() => {
  const EXTENSION_ID = 'YOUR_EXTENSION_ID';

  const video = document.getElementById('screen-view');
  const getScreen = document.getElementById('get-screen');
  const stopScreen = document.getElementById('stop-screen');
  const request = { sources: ['window', 'screen', 'tab'] };
  let stream;

})(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，开始捕捉桌面。当我们点击按钮进入屏幕时，我们向扩展发出请求，并在回调中得到响应。

```
 let stream;
  getScreen.addEventListener('click', event => {
    chrome.runtime.sendMessage(EXTENSION_ID, request, response => {

    });
  });
})(); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们得到响应，我们就检查它是否成功。如果是这样，我们可以获取扩展返回给我们的流 ID，并将其传递给`mediaDevices` API。如果没有，那么我们记录访问被拒绝。

```
 chrome.runtime.sendMessage(EXTENSION_ID, request, response => {
      if (response && response.type === 'success') {
        navigator.mediaDevices.getUserMedia({
          video: {
            mandatory: {
              chromeMediaSource: 'desktop',
              chromeMediaSourceId: response.streamId,
            }
          }
        });
      } else {
        console.error('Could not get stream');
      }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们将选项传递给`mediaDevices.getUserMedia`的视频选项。`chromeMediaSource`应该被设置为`'desktop'`，而`chromeMediaSourceId`是我们从扩展中得到的流 ID。

我们需要编写的其余代码与常规的`getUserMedia`代码一样。该函数返回一个用流解析的承诺，因此我们需要将流设置为页面的`&lt;video&gt;`元素的源。我们还将隐藏 get screen 按钮，显示 stop screen 按钮，并捕捉任何可能发生的错误。

```
 navigator.mediaDevices.getUserMedia({
          video: {
            mandatory: {
              chromeMediaSource: 'desktop',
              chromeMediaSourceId: response.streamId,
            }
          }
        }).then(returnedStream => {
          stream = returnedStream;
          video.src = URL.createObjectURL(stream);
          getScreen.style.display = 'none';
          stopScreen.style.display = 'inline';
        }).catch(err => {
          console.error('Could not get stream: ', err);
        }); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以编写代码来处理停止屏幕捕获。这将监听按钮上的点击，获取流的曲目并停止它们。它还从`&lt;video&gt;`中移除`src`，并反转可见按钮。

```
 } else {
        console.error('Could not get stream');
      }
    });
  });
  stopScreen.addEventListener('click', event =&gt; {
    stream.getTracks().forEach(track =&gt; track.stop());
    video.src = '';
    stopScreen.style.display = 'none';
    getScreen.style.display = 'inline';
  });
})(); 
```

Enter fullscreen mode Exit fullscreen mode

这是所有的代码。让我们运行这个。

### 捕捉屏幕

我们需要做的最后一件事是在本地主机上提供这个 HTML 文件。我通常使用一个名为 [servedir](https://www.npmjs.com/package/servedir) 的 npm 模块。如果安装了 Node.js 和 npm，可以用
安装

```
npm install servedir -g 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用命令行导航到保存文件的目录，并通过输入:
在 localhost:8000 上提供该文件

```
serve . 
```

Enter fullscreen mode Exit fullscreen mode

如果您有另一种方法用于在本地主机上提供静态文件，您也可以使用这种方法。

打开页面，点击“获取屏幕”按钮，选择您想要共享的屏幕、窗口或选项卡。您已经捕获了您的屏幕！

[![The screen capture feature we just built. Clicking a button shows a dialog that allows you to select which screen, window or Chrome tab to share and then shows it in a video.](img/4fc779cf7d7f58060978d7e9ccb9bebd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DpEPPo87--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2017/10/chrome.gif)

## 下一步

如果你没有写下所有的代码，你也可以在 GitHub repo 中查看结果。

我们在这里所做的一切都是针对 Chrome 浏览器的，但事实证明你也可以在 Firefox 中访问屏幕截图。理想情况下，我们可以将它封装在一个能在两种浏览器上无缝运行的函数中。请留意关于如何达到这一阶段的更多博客文章。

我们已经实现了所需扩展的一个非常小的版本来访问屏幕截图。要获得更强大的版本，请查看 Twilio 文档中的屏幕截图指南[。您还可以在那里找到更多关于如何在视频通话中使用 Twilio Video 的这一新功能进行屏幕共享的信息。](https://www.twilio.com/docs/api/video/screen-capture-chrome?code-sample=code-onmessageexternal-event-listener&code-language=js&code-sdk-version=default)

你有一个有趣的浏览器截屏用例吗？请在下面的评论中告诉我你的想法或任何问题。或者你也可以在推特上发微博到 [@philnash](https://www.twitter.com/philnash) ，或者发邮件到 philnash@twilio.com[。](mailto:philnash@twilio.com)

* * *

Google Chrome 中的 *[截屏最初发布于 2017 年 10 月 12 日](https://www.twilio.com/blog/2017/10/screen-capture-in-google-chrome.html) [Twilio 博客](https://www.twilio.com/blog)。*