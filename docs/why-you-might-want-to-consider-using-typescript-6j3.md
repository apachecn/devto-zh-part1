# 考虑使用 TypeScript

> 原文：<https://dev.to/nickytonline/why-you-might-want-to-consider-using-typescript-6j3>

我不会成为那种告诉你必须使用 TypeScript(或 Flow)的人。我也不打算进入整个 [TS vs. Flow](https://www.google.ca/search?q=flow+vs.+typescript&rlz=1C5CHFA_enCA763CA763&oq=flow+vs.+typescript&aqs=chrome.0.0l4.3590j0j4&sourceid=chrome&ie=UTF-8) 辩论。已经有很多人在这么做了。我想做的只是给出一些你可能会考虑使用 TypeScript 的好理由。在那之后，我把它留给你。

我从 2014 年秋季开始使用 TypeScript 版)。它已经走了很长的路。为了保持这种语言作为 JavaScript 的超集(而不是改变 JS 语言)所付出的努力是惊人的。向安德斯·海尔斯伯格、整个打字团队和 OSS peeps 致敬，是他们成就了这个伟大的项目。

## 为什么打字稿？

所以让我们开始吧。如果您从未使用过像 Java 或 C#这样的静态类型语言，这可能对您来说很陌生。你可能会问自己，为什么要打字稿？

*   类型安全:在开发过程中，如果一个类型不正确，它会在编译时被发现。例如`const index: number = 'hi'; // index can't be a string`或者对象上的属性是否有错别字。这允许我们在开发的早期而不是运行时捕捉错误(因为 JS 是一种动态类型语言)。这里有一个关于静态类型语言和动态语言的很好的解释。
*   使用支持 TypeScript 的编辑器或插件时的重构和智能感知功能。
    *   [Visual Studio 代码](https://code.visualstudio.com)，我的首选编辑器。这是我的设置，vscode.iamdeveloper.com。
    *   [TypeScript Atom 插件](https://atom.io/packages/atom-typescript)
    *   [打字稿崇高文本插件](https://github.com/Microsoft/TypeScript-Sublime-Plugin)
    *   [网络风暴](https://www.jetbrains.com/help/webstorm/typescript.html)
    *   [IntelliJ 终极版](https://www.jetbrains.com/help/idea/typescript.html)
    *   [Visual Studio 2015 的打字稿](https://www.microsoft.com/en-us/download/details.aspx?id=48593)
    *   Visual Studio 2017(包括类型脚本支持)
    *   [alm.tools](http://alm.tools)
    *   我错过了一个吗？
*   Intellisense 当使用有 TypeScript 声明文件的 npm 包时，即使你的项目不是用 TypeScript 编写的(据我所知这只适用于 VS 代码。如果不是这样，请插话。)

> ![VS Code Tips profile image](img/dec0e095ff49e87c4dccb2d99cd0a574.png)VS 代码提示@ vscodetips![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)如果你没有使用 [@typescriptlang](https://twitter.com/typescriptlang) ，在 [@code](https://twitter.com/code) 中仍然可以从中受益。有打码的包 u 导入请购单…[twitter.com/i/web/status/9…](https://t.co/nhHQsCOLRN)2017 年 9 月 21 日下午 18:42[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=910937405267509249)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=910937405267509249)

很好，看起来很棒，但是从不使用 TypeScript 的项目中消费 npm 包怎么办？正如上面简要提到的，即使您的项目不使用 TypeScript，Visual Studio 代码也可以获取声明文件。那么这些申报文件来自哪里呢？

## 打印声明文件

什么是类型脚本声明文件？简而言之，它是一个描述包中有哪些类型的文件。对于基于类型脚本的项目的作者，他们几乎总是[创作类型脚本声明文件](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)。对于不是用 TypeScript 编写的项目，有时，包作者会手工编写声明文件，并在他们的项目中维护它们。然而，在大多数情况下，社区已经开始着手编写不是用 TypeScript 编写的项目的声明文件。它们都存放在一个名为 [DefinitelyTyped](http://definitelytyped.org) 的库中，这是一个由 JS/TS OSS 社区维护的高质量类型定义库。

要为没有自己的声明文件的包添加类型，请安装其等效的`@types`包。例如`npm install lodash --save;npm install @types/lodash --save-dev;`现在，当您在支持 TS 的编辑器中的 TS 项目中使用 lodash 时，您将获得类型化的智能感知。

## 谁在用？

进入 TypeScript 的努力(聪明)工作和热爱并没有被忽视。最近，您可能知道的一些相当大的项目已经迁移到 TypeScript。

注意:我会不时更新这个列表。

> ![Nick Taylor profile image](img/cbed4a206d05e3456100181d0cccb3ed.png)[@ Nicky tonline](https://dev.to/nickytonline)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ housecor](https://twitter.com/housecor)1/最近好像很多大公司都在为大项目迁移到 a 型系统。 [@github](https://twitter.com/github) ，[infoq.com/news/2017/05/g…](https://t.co/WbXrUbR1Xj)，2017 年 9 月 28 日上午 03:23[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=913242699255488512)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=913242699255488512)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=913242699255488512)

*   有趣的是， [Visual Studio 代码](https://github.com/Microsoft/vscode)是用 TypeScript 编写的！
*   Angular 在版本 2 中采用了 Typescript(相关的 [Dart，TypeScript 和 Google 的官方语言](//news.dartlang.org/2017/04/dart-typescript-and-official-languages.html))
*   [空闲时打字——几个人在编码](https://slack.engineering/typescript-at-slack-a81307fa288d)
*   [GitHub 使用电子重写其桌面客户端](https://www.infoq.com/news/2017/05/github-electron-desktop-client)
*   [阿波罗 GraphQL](https://github.com/apollographql)
*   [MobX](https://github.com/mobxjs/mobx)
*   [表格](https://github.com/jaredpalmer/formik)
*   [狂欢](https://github.com/jaredpalmer/razzle)
*   [在 Lyft 打字稿](https://eng.lyft.com/typescript-at-lyft-64f0702346ea)
*   [RxJS](https://github.com/ReactiveX/rxjs)
*   [glimmer.js](https://github.com/glimmerjs/glimmer.js) (烬之名)，也和[有关 glimmer . js:TypeScript 是怎么回事？](https://medium.com/@tomdale/glimmer-js-whats-the-deal-with-typescript-f666d1a3aad0)
*   [Rollup](https://github.com/rollup/rollup)
*   [比轻量级更轻:我们如何用 Preact 和 Glimmer.js 两次构建同一个应用程序| LinkedIn Engineering](https://engineering.linkedin.com/blog/2018/03/how-we-built-the-same-app-twice-with-preact-and-glimmerjs)

我肯定还有其他的，但这些是我所知道的最大的。您可以随时查看 GitHub 中的[趋势类型脚本项目](https://github.com/trending/typescript)。

## 附加资源

这里有一些帮助您入门和运行的附加资源。

*   [打字操场](https://www.typescriptlang.org/play)
*   非官方打字稿游乐场(2019 年 5 月更新)

[![nickytonline](img/6883bc3afaaae319b5553cb8ecf6b599.png)](/nickytonline) [## 增强的打字游戏场

### 尼克·泰勒(他/他)2 月 26 日 191 分钟阅读

#typescript](/nickytonline/an-enhanced-typescript-playground-49j6)

*   [打字稿深潜](https://www.gitbook.com/book/basarat/typescript)
*   [打字基础](https://typescriptcourses.com/typescript-fundamentals)(大免费课程)
*   [马里乌斯·舒尔茨的博客](https://mariusschulz.com/blog/series/typescript-evolution)
*   [Quokka.js](https://quokkajs.com) (支持类型脚本的 js 实时便笺)
*   TypeScript 团队关于开发的 Type | Treat 系列

[![typescript](img/8ae8511072ee22a9256c0dee95a9a694.png)![](img/203e1af28d22a33bd0cbf9b26a5ecb23.png)](/typescript) [## 键入| Treat 2021 -第 1 天

### orta for typescript Oct 26 ' 212min read

#typescript #challenge](/typescript/type-treat-2021-day-1-6an)

*   在 Twitter 上关注的账户:
    *   官方打字稿账号 _ [@typescriptlang](https://twitter.com/typescriptlang) )
    *   Daniel Rosen water([@ drosenwater](https://twitter.com/drosenwasser)
    *   Mohamed hegazzy([@ fdsmars](https://twitter.com/fdsmars)
    *   marius Schulz([@ mariuschurz](https://twitter.com/mariusschulz)
    *   巴萨拉特阿里赛义德([@巴萨拉特](https://twitter.com/basarat))
    *   James Henry ( [@mrjameshenry](https://twitter.com/mrjameshenry) )
    *   如果我错过了一个你认为应该在这个列表中的，让我知道。

总而言之，如果您希望在代码库(Intellisense、重构)上快速扩展团队，并通过类型检查捕捉愚蠢的错误，TypeScript 是一个很好的选择。

感谢 [@drosenwasser](https://twitter.com/drosenwasser) 、[@ richsevira](https://twitter.com/RichSeviora)和 [@nojvek](https://twitter.com/nojvek) 花时间阅读这篇博文。💯 🔥

有问题或意见吗？在推特上给我打电话。

附言[生日快乐打字稿](https://twitter.com/typescriptlang/status/914918151556448256)！