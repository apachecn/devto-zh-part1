# Vue.js 简介-第 2 章-组件(第一部分)

> 原文：<https://dev.to/neradev/an-introduction-to-vuejs---chapter-2---components-part-i>

## 系列概述

[第一章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-1---basic-setup)
[第二章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-2---components-part-i)
[第三章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-3---components-part-ii)
[第四章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-4---list)
[第五章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-5---conditional-rendering-c97)

## 前言

在我们开始一些简短的信息之前。

在你阅读第二章之前，请阅读第一章，这样你就有了一个我们可以使用的基本设置。谢谢:)

我会一直把代码上传到这个 github 库。

有些人问我为什么不使用“单个文件组件”(`.vue` files)。我决定写一个关于整个主题的特别章节，向您展示如何配置您的项目来利用它们，并告诉您我对此的看法。

我们今天的章节将更多的是理论，而不是写代码。

## 组件

组件是 Vue.js 的主要部分之一，甚至是主要部分，但实际上什么是组件呢？

让我帮你查一下维基百科。

> Web 组件是一组功能[...]允许在 web 文档和 web 应用程序中创建可重用的小部件或组件。

这是 W3C 规范上下文中 Web 组件的基本定义。但基本上这适用于 Vue 组件。它们是可重复使用的部件，您可以在您的应用程序中使用。小部件可以是一个导航，一个列表，甚至是一个简单的按钮。我个人更喜欢用小写字母命名我的组件，并将所有文件放在一个文件夹中。

在 Vue 中，每个组件至少需要有 2 样东西:

1.  一个名字(很明显)
2.  模板(属于每个组件的渲染 DOM)

让我们来看看上一章的`.js`文件:

```
import template from './hello.html';

export default {
    name: 'vg-hello',
    template
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们从一个`.html`文件导入了一个模板，并导出了一个带有两个键值对的数据对象。这两个键分别是`name`和`template`(如果你不熟悉速记对象属性符号，看看这里的)。

我在组件名前加了前缀`vg-`,因为这使得使用第三方组件的工作更加容易。稍后我将向您展示如何在其他组件中使用这些组件。在那里，名称将等于我们在 DOM 中的标签。

现在让我们简短地看一下我们当前的`.html`文件:

```
<h1>Hello World</h1> 
```

Enter fullscreen mode Exit fullscreen mode

这里我们看到的是被呈现的 DOM，而不是标记，或者当它是根组件时(现在是这样)，它替换了初始 DOM 中已安装的元素。

*提醒:*我们的`index.html`目前看起来是这样的:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />

    Vue Guide
</head>
<body>
    <div id="app"></div>

    <script src="build.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在启动我们的应用程序，打开开发工具并查看 DOM 树，我们应该会看到这个:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />

    Vue Guide
</head>
<body>
    <h1>Hello World</h1>

    <script src="build.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

在 Vue.js 中，直接替换元素和定制标签是我非常喜欢的事情(例如 Angular 4 将定制标签呈现到 DOM 中)。

## 按钮

现在让我们创建另一个组件，一个简单的按钮。我们将在接下来的章节中使用这个按钮，它会随着时间的推移越来越多。

现在我们的按钮应该是这样的:

1.  HTML 按钮标签
2.  具有属性`class="button"`和`role="button"`
3.  有正文`Click me!`

让我们从模板(`src/components/button/button.html` )
开始

```
<button role="button" class="button">Click me!</button> 
```

Enter fullscreen mode Exit fullscreen mode

这个应该很好理解。我们有了带有类、角色和预期文本的按钮标签。现在我们需要定义`.js`文件。(`src/components/button/button.js` )

```
import template from './button.html';

export default {
    name: 'vg-button',
    template
}; 
```

Enter fullscreen mode Exit fullscreen mode

我导入了模板，命名为按钮类`vg-button`，并导出了两者。这就是我们需要做的一切。我现在将向您展示如何在其他组件中使用这些组件。有两种方法，我会告诉你。

## 在全局级别注册组件。

为此，我们需要在我们的`main.js`中添加一些行。

```
import button from 'app/components/button/button';

Vue.component(button.name, button); 
```

Enter fullscreen mode Exit fullscreen mode

您的`main.js`现在可能看起来像这样:

```
import button from 'app/components/button/button';
import hello from 'app/components/hello/hello';
import Vue from 'vue';

Vue.component(button.name, button);

new Vue({
    render: (h) => h(hello)
}).$mount('#app'); 
```

Enter fullscreen mode Exit fullscreen mode

为了在我们的 hello 组件中使用按钮组件，我修改了`hello.html` :

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我在两个元素周围添加了一个额外的`<div>`,因为 Vue 要求每个组件只有一个根元素。

如果您现在构建并打开您的应用程序，您现在应该会看到该按钮。它现在还没有功能，但是应该会有的。您可以尽管添加更多:

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button />
    <vg-button />
    <vg-button />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在应该连看三个了。它们都应该有相同的 DOM，相同的内部文本，并且都不应该做任何事情。

## 在本地注册组件

这基本上是我喜欢的方式，因为在单元测试中测试呈现的 DOM 要容易得多(我们将在后面的章节中看到)。我将在下一章中使用这种方法，但我不会强迫你使用它(一如既往)。

为此，你需要调整你的`hello.js`。我们需要导入组件，然后导出使用的组件。

```
import button from 'app/components/button/button';
import template from './hello.html';

export default {
    name: 'vg-hello',
    template,
    components: {
        [button.name]: button
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我向包含所用组件的对象添加了一个新属性。如果我们知道像以前一样在模板中使用相同的 HTML，按钮应该仍然存在，而不需要全局注册它们。

## 搞定

我希望你喜欢这本指南。如果你有任何问题，请在 Twitter 或评论区提问。我会尽量回答一个。我对任何可能的反馈都感到高兴。

*下一章将在未来几天到来。*