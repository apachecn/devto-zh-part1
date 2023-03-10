# 在 Vue.js 中定义组件模板的 7 种方法

> 原文：<https://dev.to/anthonygore/7-ways-to-define-a-component-template-in-vue-js-3hdh>

在 Vue 中定义组件模板有很多选择。据我计算，至少有七种不同的方法！

在本文中，我们将浏览每种方法的示例，并讨论利弊，以便您知道在任何特定情况下哪种方法是最好的。

## 普通字符串

定义一个 Vue 组件模板的最快速和最简单的方法是向组件定义添加一个`template`属性，并分配一个包含您的标记的常规字符串。

*注:本文最初于 2017/03/27 在 Vue.js 开发者博客上发布[。](https://vuejsdevelopers.com/2017/03/24/vue-js-component-templates/?utm_source=devto&utm_medium=article&utm_campaign=cot)*

不过，这种方法实际上只在代码示例或快速原型中使用，因为除了最简单的模板之外，很难使用任何东西。

*app . js*T2】

```
Vue.component('my-checkbox', {
  template: '<div class="checkbox-wrapper" @click="check"><div :class="{ checkbox: true, checked: checked }"></div><div class="title">{{ title }}</div></div>',
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
}); 
```

##### 详细信息

*   用 HTML 还是 JS？ **JS**
*   需要 polyfill 还是 transpile？**否**
*   需要运行时模板编译器？**是**

> **运行时模板编译器:** Vue 自带内部模块，用于在运行时将 HTML 模板编译成 JavaScript。如果你使用一个在运行时不使用 HTML 标记*的模板选项*，你可以使用一个不包含这个模块的 Vue.js 的特殊构建(因此，更小更快)。

## 模板文字

从 ES2015 开始，一种称为[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)的特殊字符串可以使用反斜杠声明。与常规字符串不同，这些字符串允许嵌入表达式，并且可以是多行的。

与常规字符串相比，多行特性使得这些字符串在定义组件模板时更有用，因为它们使得标记更具可读性。

*app . js*T2】

```
Vue.component('my-checkbox', {
  template: `
    <div class="checkbox-wrapper" @click="check">
      <div :class="{ checkbox: true, checked: checked }"></div>
      <div class="title">{{ title }}</div>
    </div>
  `,
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
}); 
```

##### 详细信息

*   用 HTML 还是 JS？ **JS**
*   需要 polyfill 还是 transpile？**是**
*   需要运行时模板编译器？**是**

> 旧的浏览器可能不支持这个 ES2015 特性，所以尽管你可能应该[传输](https://babeljs.io/)你的代码以确保安全。

## X-模板

用这种方法，你的模板被定义在 index.html T2 文件的脚本标签中。脚本标签被赋予类型`text/x-template`，并在组件定义中由 id 引用。

有利的一面是，这个方法允许您在 HTML 文件中编写模板标记。缺点是它将模板与组件定义的其余部分分离开来，因此有点难以推理。

*app . js*T2】

```
Vue.component('my-checkbox', {
  template: '#checkbox-template',
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
}); 
```

*index.html*T2】

```
<div id="app">...</div>
<script type="text/x-template" id="checkbox-template">
  <div class="checkbox-wrapper" @click="check">
    <div :class="{ checkbox: true, checked: checked }"></div>
    <div class="title">{{ title }}</div>
  </div> </script> 
```

##### 详细信息

*   用 HTML 还是 JS？ **HTML**
*   需要 polyfill 还是 transpile？**否**
*   需要运行时模板编译器？**是**

## 内嵌模板

使用这种方法，当使用组件模板时，可以在父模板中定义组件模板。只要确保添加属性`inline-template`就可以了，这样 Vue 就知道在哪里可以找到它。

这种方法和 x-template 有着大致相同的优点和缺点。一个有趣的区别是，由于模板可以在文档主体中定义，因此可以为 SEO 抓取内容。

*app . js*T2】

```
Vue.component('my-checkbox', {
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
}); 
```

*index.html*T2】

```
<div id="app">
  ...
  <my-checkbox inline-template>
    <div class="checkbox-wrapper" @click="check">
      <div :class="{ checkbox: true, checked: checked }"></div>
      <div class="title">{{ title }}</div>
    </div>
  </my-checkbox>
</div> 
```

##### 详细信息

*   用 HTML 还是 JS？ **HTML**
*   需要 polyfill 还是 transpile？**否**
*   需要运行时模板编译器？**是**

> 内联模板和 x-template 可以与后端框架的模板引擎协同工作，例如 Laravel Blade。

## 渲染功能

渲染函数要求您使用纯 JavaScript 定义模板。您需要阅读 [Vue 文档](https://vuejs.org/v2/guide/render-function.html)以获得准确的语法，但是大致的想法是您通过调用`createElement(tag, options, childElements)`来定义模板节点。

这样做的好处是，它不需要任何形式的编译，并且让您完全访问 JavaScript 功能，而不是指令提供的功能。例如，要在一个标记模板中迭代，您只能使用`v-for`，但是在 JavaScript 中，您可以使用任何数组方法。

然而，渲染函数比其他模板选项要冗长和抽象得多，我不认为很多人会喜欢像这样编写整个应用程序。

*app . js*T2】

```
Vue.component('my-checkbox', {
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  },
  render(createElement) {
    return createElement(
      'div', {
        attrs: {
          'class': 'checkbox-wrapper'
        },
        on: {
          click: this.check
        }
      },
      [
        createElement(
          'div', {
            'class': {
              checkbox: true,
              checked: this.checked
            }
          }
        ),
        createElement(
          'div', {
            attrs: {
              'class': 'title'
            }
          },
          [this.title]
        )
      ]
    );
  }
}); 
```

##### 详细信息

*   用 HTML 还是 JS？ **JS**
*   需要 polyfill 还是 transpile？**否**
*   需要运行时模板编译器？**否**

## JSX

JSX 是 JavaScript 的扩展，允许您在 JavaScript 代码中使用特殊的模板语法。

由 React 推广，这是 Vue 中最有争议的模板选项，因为一些开发者认为它丑陋、不直观，是对 Vue 精神的背叛。

然而，JSX 可以用来以一种可读性更强、抽象程度更低的方式编写 Vue 渲染函数。不过，它确实需要你进行转换，因为浏览器无法读取 JSX。

*app.jsx*

```
Vue.component('my-checkbox', {
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  },
  render() {
    return <div class="checkbox-wrapper" onClick={ this.check }>
            <div class={{ checkbox: true, checked: this.checked }}></div>
            <div class="title">{ this.title }</div>
          </div>
  }
}); 
```

##### 详细信息

*   用 HTML 还是 JS？ **JS**
*   需要 polyfill 还是 transpile？**是**
*   需要运行时模板编译器？**否**

## 单列元件

Vue.js 最受欢迎的特性之一是*单文件组件* (SFC)。这些允许你在一个文件中保存所有组件定义的同时编写标记。它们被 vue-loader 编译成渲染函数，这样你也能获得最佳的运行时性能。

要创建一个 SFC，首先要创建一个*。vue* 文件，例如 *Checkbox.vue* ，然后在`template`标签中定义你的模板，在`script`标签中定义组件。然后这个文件被导入到你的主 Vue 应用程序中。

只要你习惯于使用 Vue CLI 或者在你的项目中设置你自己的构建工具，sfc 就是你要走的路。

*checkbox . view*T2]

```
<template>
  <div class="checkbox-wrapper" @click="check">
    <div :class="{ checkbox: true, checked: checked }"></div>
    <div class="title">{{ title }}</div>
  </div>
</template>
<script>
export default {
  data() {
    return { checked: false, title: 'Check me' }
  },
  methods: {
    check() { this.checked = !this.checked; }
  }
};
</script> 
```

##### 详细信息

*   用 HTML 还是 JS？ **HTML**
*   需要 polyfill 还是 transpile？**是**
*   需要运行时模板编译器？**否**

## 获胜者是...

如果你想要一个 TLDR；本文的版本是:使用单文件组件，因为它们是几乎所有场景中最通用和最强大的选项。

也就是说，以上每一项在特定场景中都有优势，应该根据您拥有的*用例*来判断。把每一个都看作是你 Vue.js 工具箱中的另一个工具，也许有一天你会用到它！

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *