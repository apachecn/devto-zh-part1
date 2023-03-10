# Web 站点作为非预期的筒仓:从 web 客户端获取数据的问题

> 原文：<https://dev.to/paul_kinlan/web-sites-as-unintended-silos-the-problem-with-getting-data-in-and-out-of-the-web-client-4o9i>

网络作为一个开放的、可互操作的平台已经改变了世界。它允许我们通过一组开放的技术 API，尤其是 Link、HTTP 和 HTML 来查看、交互和交换信息。有了这些简单的工具，我们可以创建复杂的应用程序和服务，这些应用程序和服务可以在服务器之间以及从服务器到使用浏览器的用户之间互操作。

一旦网站被加载到用户的浏览器中，由用户生成并存储在浏览器中的数据就几乎被锁定，除非它被推回到服务器，我认为这是网络的一个隐藏问题。

Web Intents 是一项技术，旨在确保客户端上的网站能够与周围的世界进行交互:用户在设备上与之交互的其他网站和应用程序。Web Intents 是一个死胡同，但问题仍然存在，我们无意中创建了锁定在用户登录和本地数据存储后面的孤岛。

我仍然对完全在客户端从 web 应用程序中获取数据充满热情，我们有许多工具可供我们使用，但让我告诉你，我们真的很难做到。

我喜欢用户浏览器中的每个网站都是它自己的安全沙箱，但我希望在 web 上看到的一件事是如何将数据从用户系统放入沙箱并通过定义的接口将数据从沙箱拉回到用户系统的界面上的一个步骤变化。

我开始写这篇文章是因为我看到 iOS 11 将支持 web 上的拖放 API，并且看了我的好朋友和同事 Sam Thorogood 关于拖放的文章(查看一下)，我想更多地探索这个领域。

**拖放简介**

在某些情况下，我想建立一个像暂存板和粘贴箱一样的应用程序，也就是说，你可以将任何内容放到网页上，然后在以后的日期再取出来，这篇文章记录了我在这个过程中发现的一些问题。

有许多方法可以将数据从主机操作系统放入网站的沙箱中，也有许多方法可以将托管和生成的数据放回主机系统。问题是它难以置信地参差不齐，而且在所有平台上都不一致，这让它成为一个令人沮丧的体验。

# 可用交互

让我们从将数据从客户端设备获取到网站的方式开始:

*   `<input type=file>`
*   用户粘贴手势时从剪贴板粘贴
*   从浏览器外部拖动
*   从主机打开网页

将网站上的数据传回客户端

*   `<a download>`
*   在用户复制手势时将数据添加到剪贴板
*   从浏览器拖到客户端

## 通过选取器上传

我不会说太多细节，但是`<input type=file>`作为一个普通的文件拾取器工作得非常好。

您可以将选取器限制为文件类型`<input type="file" accept="image/png"
/>`。

您可以让用户选择多个文件`<input type="file" multiple />`。

你也可以集成自定义的 pickers，比如 camera `<input type="file"
captuaccept="image/*" capture>`。

`<input>`元素甚至有一个允许您检查所选文件的界面。如果用户选择了一个文件，就很容易将它放入浏览器的沙箱中。

**问题 1** :一旦你有了那个文件，你就不能把任何修改存回主机上的同一个文件，你实际上是在处理文件的一个拷贝。

问题 2 :如果主机更新了文件，而你持有副本，你将看不到更新。

## 下载文件到主机 OS

我们可以简单地使用`download`属性来下载远程资源，比如:`<a href="someurl.html" download="output.html">Download</a>`。

我们也可以在客户端动态生成内容并下载到主机，如下:

```
function download() {
  var url = URL.createObjectURL(new Blob(['hello world at ', Date.now()], {'type': 'text/plain'}));
  var a = document.createElement('a');
  document.body.appendChild(a);
  a.style = 'display: none';
  a.href = url;
  a.download = 'hello.txt';
  a.click();
  URL.revokeObjectURL(url);
}

download(); 
```

Enter fullscreen mode Exit fullscreen mode

它简单而有效，现在也在 Safari 中得到支持。

**问题 1** :无法与系统集成“另存为”功能，这意味着用户无法选择文件在浏览器下载目录之外的位置。

**问题 2** :一旦文件被下载，用户的设备就无法写回文件，一旦文件被下载，它就消失了。

## 使用剪贴板将数据粘贴到网页中

可以截取页面上的用户调用系统粘贴手势时触发的`onpaste`事件，然后变一些魔术。

```
document.body.addEventListener("paste", function(e) {
  // You need to own it.
  e.preventDefault();

  // get all the types of things on the clipboard
  let types = e.clipboardData.types;

  // Get the text on the clipboard
  e.clipboardData.getData("text/plain"); 

  // Get a itterable list of items (Not on Safari)
  e.clipboardData.items

  // Get access to a file on the clipboard
  e.clipboardData.files[0]
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个 API 似乎在许多浏览器中都相对一致。物品放在一边)

总的来说，这个 API 可以很好地将数据放入你的 web 应用程序，你可以访问剪贴板上的文件和文本并使用它，它是安全的，因为用户必须启动系统粘贴操作，你才能访问这些数据。人们确实想知道一个站点是否可以监听粘贴事件并读取用户从未想过会被读取的数据。

**问题 1** :调试很痛苦，`clipboardData`的控制台记录不会显示正确的数据，你必须断点续传。

## 使用剪贴板从网页中复制自定义数据

当页面上的用户调用系统复制和剪切手势时，可以截取触发的`oncut`和`oncopy`事件，然后将您自己的定制内容添加到系统剪贴板中。

```
document.body.addEventListener("copy", function(e) {
  // You need to own it.
  e.preventDefault();

  // Set some custom data on 
  e.clipboardData.setData("text/plain", "Hello World");

  // Add a user generated file to the clipboard
  e.clipboardData.items.add(new File(["Hello World"], "test.txt", {type: "text/plain"}));
}); 
```

Enter fullscreen mode Exit fullscreen mode

乍一看这是惊人的，我应该能够任何我需要的剪贴板，但是有一些问题。

**问题 1** :无法向剪贴板添加文件。

```
document.body.addEventListener("copy", function(e) {
  // You need to own it.
  e.preventDefault();

  // Add a user generated file to the clipboard
  e.clipboardData.items.add(new File(["Hello World"], "test.txt", {type: "text/plain"}));
}); 
```

Enter fullscreen mode Exit fullscreen mode

API 是存在的，但是它看起来并不在任何地方工作。如果您尝试在将数据添加到`clipboardData`对象的同一页面上粘贴，`clipboardData.files`属性为空。如果您尝试将结果粘贴到文件系统，什么也不会发生。然而，如果你粘贴到一个文本域，文件名*被*粘贴。我不知道这是否是一个安全特性，但是无论如何都没有记录下来——如果是这样的话，我怀疑这个 API 的存在。

**问题 2** :在事件中，你应该同步操作所有的剪贴板，这意味着不可能将存储在索引数据库中的数据添加到剪贴板中。

```
document.body.addEventListener("copy", function(e) {
  // You need to own it.
  e.preventDefault();

  // Add a user generated file to the clipboard (Promise)
  getData.then(file => e.clipboardData.items.add(file));
}); 
```

Enter fullscreen mode Exit fullscreen mode

看起来你必须在事件发生的同时改变剪贴板，这严重限制了 API 的能力。

## 从主机拖动到网页

拖放 API 与剪贴板事件 API 有很多共同之处，所以理论上开始使用并不复杂。

要从主机环境中引入数据，您必须管理 drop 事件。首先，确保覆盖浏览器的默认操作(显示文件)，然后就可以访问事件中的数据。

像剪贴板一样，你有`items`和`files`，这样你就可以看到所有从主机拖到页面上的东西。

```
element.addEventListener('drop', e => {
  // Own it. nuff said.
  e.preventDefault();

  // Get the text on the clipboard
  e.dataTransfer.getData("text/plain"); 

  // Get a itterable list of items (Not on Safari)
  e.dataTransfer.items

  // Get access to a file on the clipboard
  e.dataTransfer.files[0]
}); 
```

Enter fullscreen mode Exit fullscreen mode

这看起来真的很不错。

## 从网页拖动到主机

拖放 API 与剪贴板事件 API 有很多共同之处，所以理论上开始使用并不复杂。

有一种非标准的“mime-type”叫做`DownloadURL`。这似乎在 Firefox 或 iOS 中不受支持，但在 Chrome 中受支持。你给浏览器一个获取的 url，一旦它被拖出浏览器，它就会开始下载。

```
element.addEventListener('dragstart', e => {
  // Own it. nuff said.
  e.preventDefault();
  e.dataTransfer.dropEffect = "copy";
  e.dataTransfer.effectAllowed = "all";
  e.dataTransfer.setData("DownloadURL", `image/png:test.png:${$fileURL.href}`)
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是将文件拖出浏览器并放到用户主机操作系统上的唯一已知方法。

**问题 1** : `DownloadURL`完全非标准，只能在 Chrome 中工作。

**问题 2** : `DownloadURL`似乎不能处理 Blob URLs，这意味着在浏览器中创建的文件不能被拖出。

**问题 3** :通过`DownloadURL`管理的请求没有被服务人员处理。

**问题 4** : `dataTransfer`有一个`files`对象，很像`clipboardData`，也很像`clipboardData`接口，给它添加一个文件对拖动操作没有任何作用。

**问题 5** :同样，很像`clipboardData` API，你必须在事件中同步改变`dataTransfer`对象。这使得无法与异步数据存储进行交互。

# 我希望看到的网络平台变化

我认为，我们可以对 web 平台进行一些根本性的改变，以使在客户端从 web 应用程序中获取数据变得更加简单和容易。

## 将 DownloadURL 标准化为拖放

这样做似乎是合理的。网络上几乎没有任何关于该设计的参考资料，规范列表上的任何参考资料似乎都已经随着时间的流逝而消失了。

感觉上浏览器管理`<a download>`的方式类似于今天`DownloadURL`的工作方式，所以这可能是一个好的开始。

## 允许将文件添加到剪贴板并进行拖放操作

我假设它在任何地方都不工作是有原因的，但是我希望能够`dataTransfer.files.add([File])`并且它是剪贴板上的东西，当用户手势完成时被删除。

## 让变异事件的 DOM 事件很好地配合异步操作

浏览器内部发生了许多事件，让您可以更改默认操作，然后改变事件的某些状态(见上文)。这些事件中保存的数据似乎只能在事件执行时更改，而不能作为事件创建的异步操作的结果。

我希望看到这些事件利用 [`ExtendableEvent`](https://developer.mozilla.org/en-US/docs/Web/API/ExtendableEvent) ，这样我们就可以进入`waitUntil`(或者类似的东西)。随着 web 转向数据存储操作，我们与事件交互的方式也应该如此。

## 引入持久文件 API

我们没有一种合理的方法来获取对文件的引用并保存该引用，以便可以轻松地对其进行操作。我们可以在我们的“web 应用程序”环境中做到这一点——将文件持久化到 IndexedDB 并不太复杂——但我们将所有数据孤立在应用程序中，不允许它们轻松地与周围的主机连接。

# 漫长的游戏

目前，浏览器生态系统中有几股发展趋势，一方面，我们有向应用程序和原生对等的竞赛，另一方面，我们有向内容保真的运动——引入网格、字体和性能工具等 API，以确保我们可以快速提供高质量的内容。

我今天谈论的很多内容看起来都属于“让网络成为一个应用程序平台”这一重点，这当然是故事的一部分。如果我们想要与本地体验对等，浏览器和浏览器中的站点应该表现得像是系统的一部分。但我认为还有更多。

我希望看到一个网站，其中站点之间的互连不仅仅是由链接和对服务器的 HTTP 请求来定义的，而是我们能够直接在客户端上实现站点之间的交互。我们应该能够发现用户使用的网站的服务和功能，并与它们无缝交互，同时确保主机平台的习惯用法可用于网络上的所有内容。

我担心的是，从长远来看，即使我们有了 URL，我们的网站也很容易将数据放入沙箱，但不可能放入主机系统，甚至无法放入网络上的其他网站。