# Vue.js 简介-第 5 章-条件渲染

> 原文：<https://dev.to/neradev/an-introduction-to-vuejs---chapter-5---conditional-rendering-c97>

## 系列概述

[第一章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-1---basic-setup)
[第二章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-2---components-part-i)
[第三章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-3---components-part-ii)
[第四章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-4---list)
[第五章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-5---conditional-rendering-c97)

## 前言

我离开了一段时间，没能为你们写一篇文章。但是今天我们继续我们的 Vue.js 系列。让我们讨论 Vue 中的条件渲染，并尝试修改我们的列表，以便我们可以将列表用作有序列表和无序列表。

我会一直把代码上传到这个 github 库。

## 条件

我们都知道如何在 JavaScript 中进行条件执行。通常你会有这样的东西

```
if (a === b) {
    // Execute some code if it is true
} else {
    // Execute some code if it is false
} 
```

Enter fullscreen mode Exit fullscreen mode

即使有时候你需要这样的东西:

```
if (a === b) {
    // Execute some code if the first condition is true
} else if (b === c) {
    // Execute some code if then second condition is true
} else {
    // Execute some code if both conditions are false
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，有时你想渲染某些东西，只有当某个条件为真或者不为真的时候。幸运的是，Vue 提供了更多名为`v-if`、`v-else`和`v-else-if`的指令。

## v-if

v-if 是最简单的一种。您可以将它添加到您只希望在特定情况下呈现的相应 DOM 节点中。例如，我们希望有序列表和无序列表的可能性有所不同。当然，我们可以简单地复制我们已经存在的列表，但是我们也可以基于一个布尔表达式来改变呈现的标签。

首先，我向列表组件添加了一个额外的属性:

```
import template from './list.html';

export default {
    name: 'vg-list',
    template,
    props: {
        items: {
            type: Array,
            required: true
        },
        ordered: {
            type: Boolean,
            required: false,
            default: false
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

(如果你还没有这方面的线索，请阅读前面的章节；))

现在我们来看看我们的 HTML 模板。我们希望在`ordered === false`时呈现一个`ul`标签，在`ordered === true`时呈现一个`ol`标签。当您将`v-if`指令添加到 DOM 节点时，您将值设置为您想要检查的布尔表达式。

```
<ol v-if="ordered === true" class="list">
    <li v-for="item in items" class="list-element">{{ item }}</li>
</ol>
<ul v-if="ordered === false" class="list">
    <li v-for="item in items" class="list-element">{{ item }}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

当然，你可以简单地将布尔值改为简写表达式

```
<ol v-if="ordered" class="list">
    <li v-for="item in items" class="list-element">{{ item }}</li>
</ol>
<ul v-if="!ordered" class="list">
    <li v-for="item in items" class="list-element">{{ item }}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

如果您知道将启动应用程序，您应该会得到一个错误，因为它不允许在一个组件中有多个根节点。在 Vue 历史的某个时刻，如果周围没有额外的容器元素，就不可能做这样的事情。但是自从 [v2.0.1](https://github.com/vuejs/vue/issues/3878) 以来，他们使得在使用`v-if`和`v-else`时拥有多个根节点成为可能。

## v-else

`v-else`指令总是对之前在同一节点级别调用的最后一个`v-if`或`v-else-if`指令做出反应。您不需要为这个新的“属性”设置任何值。它的行为类似于 JavaScript 代码中常见的`else`块。

为了适应现在我们的代码，我们只需要删除第二个`v-if`指令，并用一个`v-else`指令替换它。

```
<ol v-if="ordered" class="list">
    <li v-for="item in items" class="list-element">{{ item }}</li>
</ol>
<ul v-else class="list">
    <li v-for="item in items" class="list-element">{{ item }}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在启动你的应用程序，它应该可以正常工作，而不会在你的开发者工具中出现错误信息。

## v-else-if

我想不出我会在什么地方给你这样的东西。实际上，我更喜欢重新思考你的组件结构，也许把它们分成多个(为了共享逻辑，你可以使用`mixins`，但是关于这个我们将在后面讨论)。这就是为什么我不会深入探讨这个问题。

基本上它的行为就像是其他两者的结合。它需要一个布尔表达式作为值，并且总是对同一节点级别上的最后一个`v-if`或`v-else-if`指令做出反应。

如果你想进一步阅读，查看 API 文档。；)

## 传递布尔参数

在写那篇文章的时候，我注意到自己在如何将一个布尔值从 HTML 模板传递给一个组件方面缺少一些知识。通常，我会使用一个属性并将它绑定到组件上，但是当使用这样一个列表组件时，这样做毫无意义。刚刚这样做的时候:

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button text="Foo bar" />
    <vg-list :items="items" ordered="true" />
    <vg-list :items="items" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们将得到一个*“预期的布尔值，得到的是字符串。”*错误。这就是为什么我查了一下，它其实很简单。你只需要将布尔值绑定到组件:

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button text="Foo bar" />
    <vg-list :items="items" :ordered="true" />
    <vg-list :items="items" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

**或**

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button text="Foo bar" />
    <vg-list :items="items" v-bind:ordered="true" />
    <vg-list :items="items" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是可行的，因为`v-bind`指令的值作为 JavaScript 代码执行，返回值被传递给绑定的组件。

## 搞定

我希望你喜欢这本指南。如果你有任何问题，请在 Twitter 或评论区提问。我会尽量回答一个。我对任何可能的反馈都感到高兴。

*下一章将在未来几天到来。*