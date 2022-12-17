# Vue.js 简介-第 3 章-组件(第二部分)

> 原文：<https://dev.to/neradev/an-introduction-to-vuejs---chapter-3---components-part-ii>

## 系列概述

[第一章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-1---basic-setup)
[第二章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-2---components-part-i)
[第三章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-3---components-part-ii)
[第四章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-4---list)
[第五章](https://dev.to/neradev/an-introduction-to-vuejs---chapter-5---conditional-rendering-c97)

## 前言

首先:感谢 dev.to 和 Twitter 上的大力支持！

我在每一章的开头添加了目录，这样你就可以很容易地在它们之间跳转。我建议在阅读本章之前先阅读其他章节。

这一次我们将尝试扩展我们的按钮组件，使它更有用一些。

我会一直把代码上传到这个 github 库。

## 属性

第一个“任务”是扩展我们的按钮，这样我们可以根据我们使用它的地方来设置我们想要使用的文本，因为我们不希望所有的按钮都有文本“点击我！”。

对按钮文本的要求是:

*   可以在使用按钮的地方设置。
*   默认为“点击我！”如果没有给出文本。

解决这个问题的答案是属性。

属性就像函数的参数——只是对组件而言。它们可以通过将要使用的 DOM 来设置。此外，我们可以在模板中显示属性，甚至在函数中使用(我们将在后面看到)。

但是如何定义一个组件的属性呢？关键词是`props`。它也是一个包含多个属性配置的导出对象。

```
import template from './button.html';

export default {
    name: 'vg-button',
    template,
    props: {
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

属性配置可以由多个参数组成。现在我们将专注于`type`、`required`和`default`。

`type`是属性的数据类型。例如，它可以具有值`String`、`Number`或`Boolean`。对于我们的按钮文本，我们将使用`String`,因为它应该是某种文本。

在`required`的帮助下，我们可以定义一个在使用这个组件时需要传递的属性。对于我们的按钮，我将把它保留为不需要的，因为我们提供了一个默认文本。

`default`实际上是属性的默认值。

所以我们的`text`属性的定义应该是这样的。

```
import template from './button.html';

export default {
    name: 'vg-button',
    template,
    props: {
        text: {
            type: String,
            required: false,
            default: 'Click me!'
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

但是我们现在如何在模板中显示这些文本呢？

为此，我们将使用 Vue 的指令。

最简单的方法是只显示文本(实际上这正是我们要找的)。在我们的模板文件中有两种方法可以做到这一点。(`app/components/button/button.html`)

**选项 1**

```
<button role="button" class="button">
    {{ text }}
</button> 
```

Enter fullscreen mode Exit fullscreen mode

**选项二**

```
<button role="button" class="button" v-text="text"></button> 
```

Enter fullscreen mode Exit fullscreen mode

我个人更倾向于第一种选择。

现在我们想将 hello 组件中的按钮文本设置为`Foo Bar`。这其实很简单。在组件的模板文件(`app/components/hello/hello.html`)中，我们只需要向 DOM 节点添加一个属性:

```
<div class="app">
    <h1>Hello World</h1>
    <vg-button text="Foo Bar" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在构建并打开应用程序，您现在应该看到按钮有文本“Foo Bar”。

## 方法

我们当然希望我们的按钮有事可做。开始时，我们希望我们的按钮将文本打印到控制台。

通过导出 Methods 对象，可以很容易地在组件上定义方法。

```
import template from './button.html';

export default {
    name: 'vg-button',
    template,
    props: {
        text: {
            type: String,
            required: false,
            default: 'Click me!'
        }
    },
    methods: {
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你在一个方法中使用`this`,我建议你使用`function`,不要使用箭头函数。通过`this`，我们可以访问我们的财产。因此，如果我们想将我们的`text`属性打印到控制台，它看起来应该是这样的:

```
import template from './button.html';

export default {
    name: 'vg-button',
    template,
    props: {
        text: {
            type: String,
            required: false,
            default: 'Click me!'
        }
    },
    methods: {
        interact: function() {
            console.log(this.text);
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你构建并打开应用程序，你会发现这个按钮不执行这个方法。我们需要将这个方法作为事件函数添加到按钮中。幸运的是，Vue 提供了一种简单的事件绑定方式。因此，我们需要编辑模板。当我们将`@eventname`作为属性添加到一个**本地** DOM 节点时，我们可以向其注册一个函数。这些事件不会自动冒泡，所以您不能在您的自定义组件节点上直接使用它们(我们将在不同的章节中关注这一点)。在我们的例子中，应该是`@click="interact"`。

所以我们的模板(`app/components/hello/hello.html`)现在应该是这样的:

```
<button role="button" class="button" @click="interact">
    {{ text }}
</button> 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在构建并打开应用程序，按钮应该可以正常工作。耶！

## 数据

有时我们需要一些变量来保存数据，而不用在使用时传递它们。当然，我们可以用`required: false`添加属性，并用`default`添加初始值，但是 Vue 提供了另一种方式。我们可以导出一个数据对象或者一个返回对象的函数。我个人喜欢函数返回数据对象的方式。那会是这样的:

```
import template from './button.html';

export default {
    name: 'vg-button',
    template,
    props: {
        text: {
            type: String,
            required: false,
            default: 'Click me!'
        }
    },
    data: () => ({

    }),
    methods: {
        interact: function() {
            console.log(this.text);
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

作为一个使用示例，我们希望我们的按钮计算点击次数，并与按钮文本一起显示。

因此，首先我们将添加一个名为`interactCount`的数据变量，初始值为 0。

```
import template from './button.html';

export default {
    name: 'vg-button',
    template,
    props: {
        text: {
            type: String,
            required: false,
            default: 'Click me!'
        }
    },
    data: () => ({
        interactCount: 0
    }),
    methods: {
        interact: function() {
            console.log(this.text);
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要在每次点击按钮时增加这个数字。为此，我删除了`console.log`并用增量替换它。

```
import template from './button.html';

export default {
    name: 'vg-button',
    template,
    props: {
        text: {
            type: String,
            required: false,
            default: 'Click me!'
        }
    },
    data: () => ({
        interactCount: 0
    }),
    methods: {
        interact: function() {
            this.interactCount += 1;
        }
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们也可以用`this`访问数据变量。

为了现在显示数字，我们需要修改模板并使用指令来显示全文。

```
<button role="button" class="button" @click="interact">
    {{ text }} - {{ interactCount }}
</button> 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在构建并打开应用程序，则每次单击按钮时，按钮文本都会发生变化。

## 搞定

我希望你喜欢这本指南。如果你有任何问题，请在 Twitter 或评论区提问。我会尽量回答一个。我对任何可能的反馈都感到高兴。

*下一章将在未来几天到来。*