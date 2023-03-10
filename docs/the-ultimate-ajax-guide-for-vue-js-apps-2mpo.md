# Vue.js 应用程序的终极 AJAX 指南

> 原文：<https://dev.to/anthonygore/the-ultimate-ajax-guide-for-vue-js-apps-2mpo>

如果你问两个 Vue.js 开发者“在 Vue 应用中实现 AJAX 的最好方法是什么？”，你会得到三种不同的意见。

Vue 是一个 UI 库，因此没有提供实现 AJAX 的官方方法。有许多不同的方法可以有效地使用，每种方法都有其优缺点，应该根据您的需求来考虑。

在本文中，我将首先向您展示如何让 Vue 应用程序支持 AJAX，然后再介绍管理 AJAX 请求的最有用的模式。我将解释每种模式，给出一个例子，并讨论其优缺点。

## 什么是 AJAX？

AJAX(异步 JavaScript 和 XML)是一种通过 HTTP 从客户端应用程序到 web 服务器的通信方式。如果您想从 Vue.js 应用程序中读取或写入数据，您很可能会考虑 AJAX。

当然，您需要使用一个具有公共可访问端点的 web 服务器，例如`GET /items`。AJAX 将允许您的 Vue 应用程序在其生命周期中的任何时候请求该端点。

*注:本文最初于 2017/08/28 在 Vue.js 开发者博客上发布[。](https://vuejsdevelopers.com/2017/08/28/vue-js-ajax-recipes/?utm_source=devto&utm_medium=article&utm_campaign=var)*

## AJAX-启用 Vue 应用

AJAX 可以在任何 JavaScript 应用中通过使用本地 web APIs 实现，包括`XMLHttpRequest`或更新的`Fetch` API。

然而，直接使用这些 API 将需要冗长的样板文件，并且在 Fetch 的情况下，需要为旧的浏览器提供多种填充。所以大多数 web 开发人员推荐的方法是使用 HTTP 客户端库，比如 Axios T1。

向 Vue 应用添加 HTTP 客户端的最简单方法是使用 Vue 插件。最著名的是 [Vue Axios](https://www.npmjs.com/package/vue-axios) ，它简单地包装了 Axios 库和 [Vue 资源](https://github.com/pagekit/vue-resource)。

我是 Vue Axios 的粉丝，所以让我们看看如何安装它。首先，从命令行安装 Axios 和 Vue Axios】

```
$ npm i axios vue-axios --save 
```

现在，导入 Axios 和 Vue Axios，并将其安装在 Vue 实例上:

*app . js*T2】

```
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios) 
```

完成后，Axios 将可以从 Vue 应用中的任何位置通过实例属性`$http`进行访问:

*somecomponent . view*

```
export default {
  ...
  methods: {
    myMethod () {
      this.$http.post(
        '/api/items', 
        { name: "my item" }
      );
    }
  }
} 
```

这里我们使用 Axios 的`post`方法来发布数据。如果你想看到 Axios 所有可用的方法，看看这里的文档[。](https://github.com/axios/axios)

## 处理异步代码

根据定义，AJAX 调用是异步的，所以我们必须使用异步 JavaScript 代码来处理请求。让自己熟悉`Promise` API 和`async/await`语法是一个好主意，在 2020 年，这被普遍认为是编写异步 JS 最简单的方法。

大多数 HTTP 客户端和 Fetch API 将从 AJAX 请求中返回一个承诺。这里我们可以看到 Axios 如何返回一个承诺，我们可以在异步方法中等待结果。

*somecomponent . view*

```
export default {
  ...
  methods: {
    async myMethod () {
      const { data } = await this.$http.patch(
        '/api/items/1', 
        { name: "something" }
      );
      console.log(data);
      // example response: { id: 1, name: "something" }
    }
  }
} 
```

## 处理错误

有时候事情会出错。也许用户的连接中断了，或者某个笨蛋在没有告诉你的情况下改变了 API 响应格式。

您应该使用`try/catch`来确保您的应用程序能够处理这种情况:

*somecomponent . view*

```
export default {
  ...
  methods: {
    async myMethod () {
      try {
        const { data } = await this.$http.patch(
          '/api/items/1', 
          { name: "something" }
        );
        // do stuff
      } catch (err) {
        // uh oh, didn't work, time for plan B
      }
    }
  }
} 
```

## UX 的注意事项

当通过互联网进行 AJAX 调用时，在发出请求和解析请求之间会有延迟，延迟的长度取决于互联网连接速度和 web 服务器的延迟。

通过在界面中反映 AJAX 的状态来让用户知道发生了什么是很好的 UX。一种方法是创建一个布尔标志`isLoading`，在 AJAX 调用开始前设置为`true`，然后在调用完成后设置为`false`。

由于 Vue 的反应性，这个标志可以用在模板中，有条件地显示一个“正在加载”的消息或者一个微调器。

在这个例子中，我使用了两个标志- `isLoading`和`isError`来覆盖所有的基础。

*somecomponent . view*

```
export default {
  data: () => ({
    ...
    isLoading: false,
    isError: false
  }),
  methods: {
    async myMethod () {
      try {
        this.isLoading = true;
        const { data } = await this.$http.patch(
          '/api/items/1', 
          { name: "something" }
        );
      } catch (err) {
        this.isError = true;
      } finally {
        this.isLoading = false;
      }
    }
  }
} 
```

我们现在可以让模板反映加载/错误/正常状态，为用户提供有价值的反馈:

*SomeComponent*

```
<template>
  <div class="wrapper">
    <div v-if="isError">...</div>
    <div v-else-if="isLoading">...</div>
    <div v-else>...</div>
  </div>
</template> 
```

## 建筑图案

好了，现在你知道如何让你的 Vue 应用支持 AJAX 了。您应该从哪里开始在应用程序中调用 AJAX？

在本文的其余部分，我将介绍您可能想要使用的最常见的模式。

## 花样#1。从根实例

使用这种模式，您可以从根实例发出所有 AJAX 请求，并在那里存储所有状态。如果任何子组件需要数据，它就会作为道具下来。如果子组件需要刷新数据，将使用一个定制事件来提示根实例请求它。

示例:

*app . vista*

```
<template>
  <some-component :message="message" @refresh-message="refreshMessage" />
</template>
<script>
import SomeComponent from "@/components/SomeComponent";
export default {
  data: {
    message: ''
  },
  methods: {
    async refreshMessage(resource) {
      const response = await this.$http.get('/message');
      this.message = response.data.message;
    }
  },
  components: {
    SomeComponent
  }
};
</script> 
```

*somecomponent . view*

```
<template>
  <div>{{ message }}</div>
</template>
<script>
export default {
  props: [ 'message' ]
  methods: {
    refreshMessage() {
      this.$emit('refresh-message');
    }
  }
};
</script> 
```

**优点**

*   将所有 AJAX 逻辑和数据放在一个地方。
*   让你的组件保持“沉默”,这样它们就可以专注于表现。

**缺点**

*   随着应用程序的扩展，需要大量的道具和自定义事件。

## 模式二。从组件

使用这种架构，组件负责独立管理自己的 AJAX 请求和状态。实际上，您可能想要创建几个“容器”组件，为它们的本地“表示”组件组管理数据。

例如，`filter-list`可能是一个包装了`filter-input`和`filter-reset`的容器组件，作为表示组件。`filter-list`将包含 AJAX 逻辑，并将管理该组中所有组件的数据，通过道具和事件进行通信。

> 参见 Dan Abramov 的[presentation and Container Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)以获得对该模式的更好描述。

为了使这个架构的实现更容易，您可以将任何 AJAX 逻辑抽象到一个 mixin 中，然后在一个组件中使用该 mixin 使其支持 AJAX。

```
let mixin = {
  methods: {
    refreshMessage() {
      ...
    }
  }
}

Vue.component('container-comp', {
  // No meaningful template, I just manage data for my children
  template: '<div><presentation-comp :mydata="mydata"></presentation-comp></div>', 
  mixins: [ myMixin ],
  data() {
    return { ... }
  },

})

Vue.component('presentation-comp', {
  template: '<div>I just show stuff like {{ mydata }}</div>',
  props: [ 'mydata' ]
}) 
```

**优点**

*   保持组件的解耦和可重用。
*   随时随地获取所需的数据。

**缺点**

*   不容易与其他组件或组件组进行数据通信。
*   组件最终会承担太多的责任和重复的功能。

## 花样三。来自 Vuex 操作

使用这种架构，您可以在 Vuex 存储中管理 AJAX 逻辑。请注意，您需要在您的商店文件中导入 Axios，而不是使用 Vue Axios 插件，因为 Vuex 不能访问 Vue 实例。

*store . js*T2】

```
import axios from "axios";

store = new Vuex.Store({
  state: {
    message: ''
  },
  mutations: {
    updateMessage(state, payload) {
      state.message = payload
    }
  },
  actions: {
    async refreshMessage(context) {
      const response = await axios.get('...');
      context.commit('updateMessage', response.data.message);
    }
  }
});

export default store; 
```

现在，组件可以通过分派动作来请求新数据。

*支原体。视图*

```
<template>
  <div>{{ message }}</div>
</template>
<script>
export default {
  template: '',
  methods: {
    refreshMessage() {
      this.$store.dispatch('refeshMessage');
    }
  },
  computed: {
    message: { return this.$store.state.message; }
  }
}
</script> 
```

**优点**

*   将状态和表示逻辑解耦
*   根组件架构的所有优点，不需要道具和自定义事件。

**缺点**

*   增加了 Vuex 的开销。

## 模式四。来自路线导航警卫

使用这种架构，您的应用程序被分成多个页面，并且当路线改变时，获取页面及其子组件所需的所有数据。

这种方法的主要优点是简化了用户界面。如果组件独立地获取数据，那么当组件数据以任意顺序填充时，页面将会不可预测地重新呈现。

实现这一点的一个简单方法是在您的服务器上为每个页面创建端点，例如`/about`、`/contact`等，它们与您的应用程序中的路线名称相匹配。然后，您可以实现一个通用的`beforeRouteEnter`钩子，将所有数据属性合并到页面组件的数据中:

```
import axios from 'axios';

router.beforeRouteEnter(async (to, from, next) => {
  const { data } = await axios.get(`/api${to.path}`);
  next(vm => Object.assign(vm.$data, data));
}); 
```

**优点**

*   使用户界面更可预测。

**缺点**

*   整体速度较慢，因为在所有数据准备就绪之前，页面无法呈现。
*   如果不使用路线，帮助不大。

## 花样五。从服务模块

“关注点分离”是指类/模块/文件应该只有一个任务。这个原则确保你的代码易于阅读和维护。

为了遵守这个原则，我们应该尽量将 AJAX 逻辑放在组件之外(用于表示)和 Vuex 之外(用于表示状态)。

实现这一点的一个好方法是将 AJAX 抽象成一个单独的模块。在这种情况下，我们可能不再需要 vue-axios 插件，而是可以直接使用 axios。

*services/http.js*

```
import "axios" from "axios";

export default {
  async getPost(id) {
    const { data } = await axios.get(`/posts/${id}`);
    return data;
  }
  ...
} 
```

现在你可以在 Vue 应用程序的任何地方调用它——components，Vuex，或者任何让你的船漂浮的东西。

*邮政. vista*

```
import http from "@/services/http";
export default {
  props: {
    id: String
  },
  data: () => ({
    post: null
  }),
  async created () {
    this.post = await http.getPost(this.id);
  }
} 
```

> 提示:你甚至可以将你的 HTTP 服务添加到 Vue 实例中，这样就可以从应用程序中的任何地方访问它，例如`this.$http.getPost();`

## 模式六。服务器呈现初始页面状态，而不是使用 AJAX

假设您的第一个页面加载包含服务器数据作为状态的一部分，例如`<p>Hello {{ name }}!</p>`。

不建议在初始页面加载时使用 AJAX 检索应用程序状态，因为它需要额外的服务器往返，这会延迟应用程序的呈现。

相反，将初始应用程序状态注入到 HTML 页面头部的内联脚本中，这样一旦需要，它就可以作为全局变量供应用程序使用。

``html
<html>
...
<head>
...
<script type="text/javascript">
window.__INITIAL_STATE__ = '{ "data": [ ... ] }';
</script>
</head>
<body>
<div id="app"></div>
</body>
</html>`` 

 ``然后 AJAX 可以用于后续的数据获取。

> 如果你有兴趣了解更多关于这种架构的知识，请阅读我的文章[在全栈 Vue/Laravel 应用中避免这种常见的反模式](https://vuejsdevelopers.com/2017/08/06/vue-js-laravel-full-stack-ajax/)。

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *``