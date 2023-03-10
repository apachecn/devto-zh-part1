# 深入了解 Visual studio 代码

> 原文：<https://dev.to/samueleresca/get-down-into-visual-studio-code-2l>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

下面这篇文章想**下**进入 Visual studio 代码及其源代码。在嘻哈音乐中,“趴下”这个词来自闪手大师。他能够识别一个 10 秒钟的鼓点，他可以手动编辑从一个鼓点独奏到另一个鼓点独奏，跨越歌曲和流派——扩展节拍，以便最终 MC 可以说唱它。他说:“我想出了一个手动击鼓的系统——大多数唱片中的鼓独奏都是 10 秒钟——所以我必须想出一种方法来复制一张唱片，无缝地将 10 秒钟变成 10 分钟。一旦我能够做到这一点，我就改变了 DJ 播放唱片的方式。”。**这就是趴下的由来。**

[https://www.youtube.com/embed/fo2Hqz0vamU](https://www.youtube.com/embed/fo2Hqz0vamU)

好吧，这篇文章不是关于 get down 的，而是像 the get down 一样，它试图分析、深入到别人的产品、Visual studio 代码中，以此来改进我们的日常代码。通过理解像微软流行的开源编辑器这样的大规模应用程序背后的实践和模式。

## 项目概述

如前所述，Visual studio 代码是一个开源框架，由微软提供支持，你可以在 Github 上找到代码:`git clone https://github.com/Microsoft/vscode.git`该库还提供了一个很酷的教程，以便[在你的机器](https://github.com/Microsoft/vscode/wiki/How-to-Contribute#contributing-to-code)上设置开发环境，并开始参与该项目。顺便说一下，如果你看一下库的标签，你可以看到 Visual studio 代码背后有两个主要的技术: **Typescript** 和**electronic**。

### 打字稿

TypeScript 是 JavaScript 的类型化超集，它编译成普通 JavaScript。我在这几篇文章里已经讲过了:[介绍 typescript](https://samueleresca.net/2015/11/introducing-typescript/) 、[介绍 Typescript:语言特点](https://samueleresca.net/2015/11/introducing-typescript-language-features/)、[扎实使用 Typescript 的原则](https://samueleresca.net/2016/08/solid-principles-using-typescript/)、[控制反转和 Typescript 中的依赖注入](https://samueleresca.net/2017/07/inversion-of-control-and-unit-testing-using-typescript/)。Typescript 非常适用于大型分布式代码库，Visual studio 代码的所有代码都基于它。

### 电子

电子基本上可以运行你的 HTML，JS，CSS 应用程序作为客户端应用程序。它构建跨平台的桌面应用程序，在操作系统输入和输出与您的应用程序之间充当桥梁，并提供统一的层。

## 项目结构

Visual Studio 代码是为可扩展性而生的。许多特性，例如语言，通常作为扩展被支持和威胁。但是，源代码还实现了一个`core`部分，它包含了编辑器的所有核心 API。`core`分为以下几层:

*   `base`:提供跨其他层使用的通用工具；
*   `platform`:为 Visual studio 代码定义服务注入支持和公共服务；
*   `editor`:包装 Visual studio 代码的代码编辑器，名为“摩纳哥”；
*   `languages`:如前所述，由于历史原因，并不是所有的语言都实现为扩展(还没有)；
*   `workbench`:托管“Monaco”编辑器，提供一些核心组件，比如:资源管理器，状态栏，或者菜单栏；

### 基层

提供通用实用程序和 UI 构建块。`base`层被拆分成若干子层:`browser`、`common`、`node`、`parts`、`test`、`worker`；该层中的所有源代码都是为了帮助开发过程而设计的，让我们关注该层中的一些代码，例如 collections.ts 文件: