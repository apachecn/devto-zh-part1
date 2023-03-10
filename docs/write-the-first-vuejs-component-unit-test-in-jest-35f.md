# 用 Jest 编写第一个 Vue.js 组件单元测试

> 原文：<https://dev.to/alexjoverm/write-the-first-vuejs-component-unit-test-in-jest-35f>

*等等，一本用 Jest 测试 Vue 的 **[书？](https://leanpub.com/testingvuejscomponentswithjest)** 是啊，这篇文章只是第一章*😉

基于 [avoriaz](https://github.com/eddyerburgh/avoriaz) 的官方 VueJS 测试库 vue-test-utils 即将面世。 [@EddYerburgh](https://twitter.com/EddYerburgh) 确实在创造它方面做得非常好。它为在 VueJS 应用程序中编写单元测试提供了所有必要的工具。

另一方面，脸书开发的测试框架使测试变得轻而易举，它具有令人敬畏的特性，例如:

*   默认情况下几乎没有配置
*   非常酷的互动模式
*   并行运行测试
*   开箱即用的间谍软件、存根软件和模拟软件
*   内置代码覆盖率
*   快照测试
*   模块模拟实用程序

也许你已经不用这个工具，只用 karma + mocha + chai + sinon +就可以编写测试了，但是你会发现这有多简单😉。

## 建立一个 vue-测试样本项目

让我们从使用 [`vue-cli`](https://github.com/vuejs/vue-cli) 创建一个新项目开始，对所有是/否问题回答“否”:

```
npm install -g vue-cli
vue init webpack vue-test
cd vue-test 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要安装一些依赖项:

```
# Install dependencies 
npm i -D jest jest-vue-preprocessor babel-jest 
```

Enter fullscreen mode Exit fullscreen mode

制作 jest 理解`.vue`文件需要 [`jest-vue-preprocessor`](https://github.com/vire/jest-vue-preprocessor) ，与 Babel 整合需要 [`babel-jest`](https://github.com/babel/babel-jest) 。

根据`vue-test-utils`，它~~还没有发布，但是现在你可以从源~~将它添加到你的`package.json`:

**更新(2017/10/10)** :已经可以从 npm 安装，因为`beta.1`已经发布。

```
npm i -D vue-test-utils 
```

Enter fullscreen mode Exit fullscreen mode

让我们在`package.json` :
中添加以下 Jest 配置

```
...  "jest":  {  "moduleNameMapper":  {  "^vue$":  "vue/dist/vue.common.js"  },  "moduleFileExtensions":  [  "js",  "vue"  ],  "transform":  {  "^.+\\.js$":  "<rootDir>/node_modules/babel-jest",  ".*\\.(vue)$":  "<rootDir>/node_modules/jest-vue-preprocessor"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 测试一个组件

我将在这里使用单个文件组件，我还没有检查它是否可以通过将它们分割成各自的`html`、`css`或`js`文件来工作，所以让我们假设你也在这样做。

首先在`src/components` :
下创建一个`MessageList.vue`组件

```
<template>
    <ul>
        <li v-for="message in messages"> {{ message }} </li>
    </ul>
</template>

<script>
    export default { 
        name: 'list', 
        props: ['messages']
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

并更新`App.vue`来使用它，如下:

```
<template>
    <div id="app">
        <MessageList messages="messages" />
    </div>
</template>

<script>
import MessageList from './components/MessageList'
export default {
    name: 'app', 
    data: () => ({
        messages: ['Hey John', 'Howdy Paco'] 
    }), 
    components: { MessageList }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我们已经有了几个可以测试的组件。让我们在项目根目录下创建一个`test`文件夹，和一个`App.test.js` :

```
import Vue from 'vue'
import App from '../src/App'

describe('App.test.js', () => {
  let cmp, vm

  beforeEach(() => {
    cmp = Vue.extend(App) // Create a copy of the original component
    vm = new cmp({
      data: { // Replace data value with this fake data
        messages: ['Cat']
      }
    }).$mount() // Instances and mounts the component
  })

  it('equals messages to ["Cat"]', () => {
    expect(vm.messages).toEqual(['Cat'])
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们运行`npm test`(或`npm t`的简写版本)，测试应该运行并通过。既然我们正在修改测试，我们最好在**观察模式** :
下运行它

```
npm t -- --watch 
```

Enter fullscreen mode Exit fullscreen mode

### 嵌套组件的问题

这个测试太简单了。让我们检查输出是否也是预期的。为此，我们可以使用 Jest 惊人的快照特性，它将生成输出的快照，并在接下来的运行中进行检查。在`App.test.js`中前面的`it`后增加:

```
it('has the expected html structure', () => {
  expect(vm.$el).toMatchSnapshot()
}) 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个`test/ __snapshots__ /App.test.js.snap`文件。让我们打开来检查一下:

```
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`App.test.js has the expected html structure 1`] = `
<div
  id="app"
>
  <ul>
    <li>
      Cat
    </li>
  </ul>
</div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

如果您没有注意到，这里有一个大问题:`MessageList`组件也被渲染了。**单元测试必须作为一个独立的单元**进行测试，这意味着在`App.test.js`中，我们想要测试`App`组件，而根本不关心其他任何事情。

这可能是几个问题的原因。想象一下，例如，子组件(本例中为`MessageList`)在`created`钩子上执行副作用操作，比如调用`fetch`,一个 Vuex 动作或者状态改变？这绝对不是我们想要的。

幸运的是，**浅渲染**很好地解决了这个问题。

### 什么是浅层渲染？

[浅层渲染](http://airbnb.io/enzyme/docs/api/shallow.html)是一种确保你的组件在没有子组件的情况下进行渲染的技术。这有助于:

*   只测试你想测试的组件(这就是单元测试的含义)
*   避免子组件可能产生的副作用，例如进行 HTTP 调用、调用存储操作…

## 用 vue-test-utils 测试一个组件

为我们提供浅层渲染和其他功能。我们可以将之前的测试重写如下:

```
import { shallow } from 'vue-test-utils'
import App from '../src/App'

describe('App.test.js', () => {
  let cmp

  beforeEach(() => {
    cmp = shallow(App, { // Create a shallow instance of the component
      data: {
        messages: ['Cat']
      }
    })
  })

  it('equals messages to ["Cat"]', () => {
    // Within cmp.vm, we can access all Vue instance methods
    expect(cmp.vm.messages).toEqual(['Cat'])
  })

  it('has the expected html structure', () => {
    expect(cmp.element).toMatchSnapshot()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您仍然在观察模式下运行 Jest，您将看到测试仍然通过，但是快照不匹配。按`u`重新生成。再次打开检查:

```
// Jest Snapshot v1, https://goo.gl/fbAQLP

exports[`App.test.js has the expected html structure 1`] = `
<div
  id="app"
>
  <!--  -->
</div>
`; 
```

Enter fullscreen mode Exit fullscreen mode

你看到了吗？现在没有孩子被渲染，我们测试了从组件树中完全隔离的`App`组件**。此外，如果你在子组件中有任何`created`或任何挂钩，它们也没有被调用😉。**

 **如果你对**如何实现浅层渲染**感到好奇，查看一下[的源代码](https://github.com/vuejs/vue-test-utils/blob/master/src/lib/stub-components.js)，你会发现它基本上是按下了`components`键、`render`方法和生命周期挂钩。

同样，您可以如下实现`MessageList.test.js`测试:

```
import { shallow } from 'vue-test-utils'
import MessageList from '../src/components/MessageList'

describe('MessageList.test.js', () => {
  let cmp

  beforeEach(() => {
    cmp = shallow(MessageList, {
      // Beaware that props is overriden using `propsData`
      propsData: {
        messages: ['Cat']
      }
    })
  })

  it('has received ["Cat"] as the message property', () => {
    expect(cmp.vm.messages).toEqual(['Cat'])
  })

  it('has the expected html structure', () => {
    expect(cmp.element).toMatchSnapshot()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

在 Github 上找到[的完整示例。](https://github.com/alexjoverm/vue-testing-series/tree/lesson-1)

*最初发布于[alexjoverm . github . io](http://alexjoverm.github.io/2017/08/21/Write-the-first-Vue-js-Component-Unit-Test-in-Jest/)2017 年 8 月 21 日***