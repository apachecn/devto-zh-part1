# Vue.js 简介-第 4 章-列表

> 原文：<https://dev.to/neradev/an-introduction-to-vuejs---chapter-4---list>

## 系列概述

[第一章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-1---basic-setup)
[第二章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-2---components-part-i)
[第三章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-3---components-part-ii)
[第四章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-4---list)
[第五章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-5---conditional-rendering-c97)

## 前言

有时你想要呈现一个列表。该列表可以是一些项目符号、图表列表、多个内容项，...

但是我们如何用 Vue.js 做到这一点呢？这个问题我会试着和你一起回答。

我会一直把代码上传到这个 github 库。

## 需求

我们需要一份清单。当然要从一个组件开始。

```
import template from './list.html';

export default {
    name: 'vg-list',
    template
}; 
```

Enter fullscreen mode Exit fullscreen mode

尽管这个组件需要一个属性来传递我们想要呈现的列表项。该属性应该是必需的，并且具有类型`Array`。

(`src/components/list/list.js` )

```
import template from './list.html';

export default {
    name: 'vg-list',
    template,
    props: {
        items: {
            type: Array,
            required: true
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你不明白我们在这里做了什么，我推荐你阅读第二章和第三章[。](https://dev.to/neradev/an-introduction-to-vuejs---chapter-3---components-part-ii)

## 模板

呈现列表的实际魔术发生在我们的模板文件中。

让我们看看普通的 HTML 列表是什么样子的。

```
<ul class="list">
    <li class="list-item">List Item 1</li>
    <li class="list-item">List Item 2</li>
    <li class="list-item">List Item 3</li>
    <li class="list-item">List Item 4</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

正如我们可以看到的，我们总是重复这个`li`标签，只是改变相应的文本。令人高兴的是，Vue 提供了一些 API 来自动完成这项工作。为此，我们使用指令`v-for`。使用`v-for`,你可以遍历一个数组并使用特定的值来呈现一个 DOM 元素甚至一个定制组件。让我们看看最初是什么样子的。

(`src/components/list/list.html` )

```
<ul class="list">
    <li v-for="" class="list-element">List item</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

那个`v-for`属性的值通常看起来像这样:`<iteratee> in <array>`
，这实际上看起来像 JavaScript 中的一个简单 for-each 循环。在我们的例子中，我们想要迭代我们的`items`数组，并使用每一项来显示它。(预计该数组只包含普通字符串)

```
<ul class="list">
    <li v-for="item in items" class="list-element"></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

现在要显示它，我们只需使用文本指令:

```
<ul class="list">
    <li v-for="item in items" class="list-element">{{ item }}</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

## 利用它

我修改了我们的`hello`组件，向它添加了一个数据属性，用一个简单的字符串数组作为值。而且我已经导入了`list`组件

```
import button from 'app/components/button/button';
import list from 'app/components/list/list';
import template from './hello.html';

export default {
    name: 'vg-hello',
    template,
    components: {
        [button.name]: button,
        [list.name]: list
    },
    data: () => ({
        items: ['foo', 'bar', 'buz']
    })
}; 
```

Enter fullscreen mode Exit fullscreen mode

但是我们如何将这个条目数组放入列表组件呢？如果我们像以前一样尝试简单地添加变量作为属性，我们会得到一个错误。

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button text="Foo bar" />
    <vg-list items="items" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

所以这不起作用，因为`items`的值现在被解释为一个字符串(这意味着`items`不是被转换为字符串，而是实际上传递了单词`items`)。我们需要将该数据属性绑定到列表。这可以通过两种不同的方式来实现。

*选项 1*

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button text="Foo bar" />
    <vg-list v-bind:items="items" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

*选项二*

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button text="Foo bar" />
    <vg-list :items="items" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我个人更喜欢使用第二个选项，因为它可以帮助你节省打字时间。使用 bind 方法，你可以传递普通的属性甚至方法。

如果您现在构建并打开您的应用程序，我应该会看到您的列表呈现。当然，我们目前只能呈现一个无序列表，而不是有序列表，但是我们将在下一章看到如何工作。

## 搞定

我希望你喜欢这本指南。如果你有任何问题，请在 Twitter 或评论区提问。我会尽量回答一个。我对任何可能的反馈都感到高兴。

[下一章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-5---conditional-rendering-c97)