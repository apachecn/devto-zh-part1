# 2017 年推出的 Angular 4 功能

> 原文：<https://dev.to/amitkhirale/10-major-features-introduced-in-angular-4>

借助 Angular 非凡而神奇的特性，Web 应用程序开发正在提供自发的、交互式的和干净的应用程序。angular 社区包含了 angular 4 的许多高级变化，这就是主要版本号转移到 Angular 4 的原因。跳过版本 3 的主要原因是路由器包在版本 3.x 中，因此团队没有将所有东西都升级到 3.0，而是将路由器升级到 4.0，而是选择将所有 ng 模块的版本升级到 4.0。让我告诉你一个重要的消息，angular 社区已经决定在三个不同的阶段发布一些基于时间的版本:

-->每周都会发布有棱角的补丁
- >少数小版本之后会有大版本
- >最后每 6 个月会有一个大版本

## 现在让我们跳入 Angular 4 中引入的主要变化:

## 棱角万能:

服务器端呈现是将虚拟 DOM 中相同的 HTML 内容发送到服务器的行为，以便浏览器根据请求适当地呈现这些内容。这使得蜘蛛和网络机器人能够在必要的时候挖掘你的网站并索引内容。

在单页面应用出现之前，服务器端渲染在规范的 Web 应用开发中并不存在。这类应用使用虚拟 DOM 概念，抽象 DOM 呈现。需要一种通过服务器请求呈现 DOM 的技术。

该项目完全保持在 Angular 之外，但从 Angular 4 开始，Universal 将成为 Angular 的核心模块。

## 键入脚本 2.4:

Type Script 是由微软创建和维护的开源编程语言，可以免费实现。它是 JavaScript 的超级集合，主要提供可选的静态类型、类和接口。也是为开发大型应用程序和将编译器转换成 JavaScript 而设计的。Angular 4 引入了它们，很快你就可以激活新的 strictNullChecks。

## [T1】StrictNullChecks:](#strictnullchecks)

遗憾的是，改进的类型检查的一些部分不能包含在 Angular 4 中[目前],因为在 RC 阶段发现了一些不兼容性。不过，有计划在 4.1 版中包含这个特性。

## 模块 ID 已删除:

他们在 angular 4.0 中添加了一个新的手动编写模块的 SystemJS 插件，angular 可以引用你应该提供的相对路径就足够了。它动态地将模板 URL 和样式 URL 中的“组件相对”路径转换为“绝对路径”。

## 更小&更快的应用程序:

Angular 团队在提高速度方面做得非常好。与 Angular 2 相比，Angular 4 的应用更小更快。在做开发的时候，这一点很明显，但是我还没有能够在一个服务器上测试出太多的不同。

## 动画包:

升级 Angular 2 动画现在有自己的包即@ Angular/platform-browser/Animations；现在，import 语句将改为如下所示:

在您的应用模块中导入 BrowserAnimationsModule 模块:

动画现在有了自己的包，即@ angular/platform-browser/animations。

## 模板 ng-template:

修改您的代码并更改模板的所有出现

### 模板

### 到

### ng-模板

你应该使用 ng-template 标签，而不要使用它，因为它会引起与其他标签用法的冲突，但它仍然有效，因此 Angular 团队将其更改为用于 Angular 目的。当你更新到 Angular 4 时，如果你在某处使用了废弃的模板，它会弹出一个警告，所以很容易发现它们。这是一个突破性的变化；因此，根据语义版本规则，他们没有在 Angular-2 中引入这一变化，而只是在 Angular-4 中引入了这一变化。

## ngIf 与 else:

现在也可以在模板中使用 else 语法。

## 管道:

Angular 4 推出了新的标题案例管道。它将每个单词的第一个字母变成大写。

## http:

向 HTTP 请求添加搜索参数已经简化。

## 结论:

一些需要注意的事项:

更新到 Angular v4 可能意味着一些相关模块将会不同步，因此您也必须更新它们。就个人而言，我遇到的唯一问题是我更新到 2.2.1 的 TypeScript。

当您使用 else 关键字时，理解 Angular 模板语法的代码编辑器和插件仍然会抱怨语法不正确。这很好，因为您的代码仍然可以工作。插件/编辑器经理会尽快更新。

我想我们已经涵盖了 Angular 4 中引入的所有主要变化。使用 Angular 4.0 的 [Web 应用开发](https://www.angularminds.com/angularjs-development-company.html)将提供更加安全、灵活和可扩展的途径。这确实表明了 Angular 团队致力于使 Angular 成为最好的、更具交互性和更高效的开发工具。

访问原始帖子[Angular 4 中介绍的 10 个主要功能](https://www.angularminds.com/blog/article/10-major-features-introduced-in-angular-4.html)