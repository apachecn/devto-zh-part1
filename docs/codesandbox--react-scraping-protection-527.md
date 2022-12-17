# CodeSandbox —反应刮擦保护

> 原文：<https://dev.to/divyenduz/codesandbox--react-scraping-protection-527>

#### TL；DR；这是 CodeSandbox 的粉丝贴！

### 简介

CodeSandbox 的主要目标之一是在浏览器中提供完全原生的离线开发体验，我只是写了一个小组件，并用它做了一个 [Github 存储库](https://github.com/divyenduz/React-Scraping-Protection)，全部在 CodeSandBox 中。

[https://www.youtube.com/embed/5lR29NsJKW8](https://www.youtube.com/embed/5lR29NsJKW8)

我刚试了一下，它看起来很结实。这是组件的嵌入(再次使用，是的，你猜对了！CodeSandbox):-

[https://medium . com/media/EC 59550 D7 C5 be 9 ce 0 BD 95 e 1922 ef6 f 55/href](https://medium.com/media/ec59550d7c5be9ce0bd95e1922ef6f55/href)

我还更改了底层存储库的名称，CodeSandbox 能够处理这些。

值得注意的事情:-

1.  所有提交都是通过 CodeSandbox 完成的。
2.  Github 集成(尽管是测试版)看起来很可靠。
3.  它集成了[部署到现在！](https://zeit.co/now)
4.  [Monaco editor](https://github.com/Microsoft/monaco-editor) (基于 VSCode)非常时尚，提供了一种可靠的离线式体验，具有自动完成和点击跳转功能。
5.  与 Medium 集成(上面的链接是粘贴到 Medium 中的 CodeSandbox 链接)。
6.  [通过网络界面添加 npm 模块的能力](https://hackernoon.com/how-we-make-npm-packages-work-in-the-browser-announcing-the-new-packager-6ce16aa4cee6)。

### 组件—电抗器保护

组件本身是非常基本的。目标是能够延迟呈现字符串，以防止 bot 抓取。

当然，这不适用于客户端应用，尽管人们普遍认为谷歌能够理解 Javascript。

这个组件是为 React 组件在服务器端使用像 [next.js](https://github.com/zeit/next.js) 这样的框架呈现的情况而设计的。

我还没有在 SSR 环境中测试过这个组件(包括本地和生产环境)！我只是在玩 CodeSanbox，API 可能会改变。

暂时就这样吧！编码快乐！

* * *