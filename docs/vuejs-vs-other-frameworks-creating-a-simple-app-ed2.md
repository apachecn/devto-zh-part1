# Vue.js vs 其他框架:创建一个简单的应用程序。

> 原文：<https://dev.to/jsmegatools/vuejs-vs-other-frameworks-creating-a-simple-app-ed2>

Vue.js 是一个框架，它将其他流行框架的许多功能组合成一个独特的组合，并添加了一些自己的酷功能。在这篇文章中，我将向你展示如何用 vue.js 创建一个简单的回顾应用程序，并将这个过程与我有经验的其他框架进行比较。

第一步是创建一个初始结构。javascript 开发人员目前有很多可用的库和配置，为了节省时间，我更喜欢使用 framework 附带的某种项目脚手架。Vue 有一个命令行界面。让我们通过运行:
来安装 CLI

```
npm install —global vue-cli 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
yarn global add vue-cli 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，我们可以创建一个项目

```
vue init webpack tv_shows_reviews 
```

Enter fullscreen mode Exit fullscreen mode

它会问我们一些问题。我们需要确保对“安装 vue 路由器？”回答“是”问题。剩下的就不言自明了。

然后，按照 CLI 的指示，我们有一个工作项目在本地主机上运行。它的根目录下有以下文件和文件夹:

```
README.md
build
config
index.html
node_modules
package-lock.json
package.json
src
static 
```

Enter fullscreen mode Exit fullscreen mode

启动应用程序的代码位于 src 文件夹中，其结构如下:

```
├── App.vue
├── assets
│   └── logo.png
├── components
│   ├── TVShow.vue
│   └── TVShowsList.vue
├── main.js
├── mock_data.js
└── router
    └── index.js 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候开发我们自己的功能了。

让我们从添加代表我们将要拥有的路线的组件开始。我们首先为它们创建一些基本结构。我们将它们添加到 src/components 目录中。第一个组件将位于 TVShowList.vue 文件中(view 将模板和样式应用到组件并正确导出时需要此扩展名):

```
<template>
  <div class="tv_show_list">TV show list</div>
</template>

<script>
export default {
  name: 'TVShowList'
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style> 
```

Enter fullscreen mode Exit fullscreen mode

Vue 采用的理念是组件的 HTML、CSS 和 JavaScript 都应该在一个文件中。这类似于 JSX 如何驻留在 React.js 的渲染函数中，只是 React 中的样式通常放在一个单独的文件中。

第二个将是相同的，除了它将在一个文件 TVShow.vue 中，有一个名称 TVShow 和一个类 tv_show，以后它将包含一个电视节目的描述和评论。

下一步将添加路由定义。它们将位于 Vue CLI 为我们创建的 routes/index.js 文件中。让我们编辑文件，使其包含以下内容:

```
import Vue from 'vue'
import Router from 'vue-router'
import TVShow from '@/components/TVShow'
import TVShowsList from '@/components/TVShowsList'

Vue.use(Router)

export default new Router({
  routes: [
    { path: '/show/:id', component: TVShow },
    { path: '/', component: TVShowsList }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的是将刚刚创建的组件导入到路由定义模块中，我们还导入了路由工作所需的 vue 和 vue 路由器模块。然后，我们使用 Vue.use 方法来安装路由器插件。然后我们定义了路线。我们的索引路由(“/”)将服务于 TVShowsList 组件，而“/show/:id”路由将服务于 TVShow 组件。id 符号用于匹配类似/show/1 的 URL，其中 1 是电视节目的 id。

然后我们需要编辑我们的 App.vue 文件来删除徽标，它看起来应该是这样的:

```
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

组件将包含我们的路线视图。

接下来，让我们添加一些真正的内容到登录页面。我们现在将使用一些模拟数据(稍后您可以集成来自 MongoDB 或任何其他您喜欢使用的数据库的数据)。让我们用一些虚构的电视节目创建一个 mock_data.js 文件。

```
export default [
  {
    id: '1',
    name: 'Physical Worker',
    image: 'vintage-1133810_640.jpg',
    reviews: [
    …
    ]
  },
  {
    id: '2',
    name: 'Hard Face',
    image: 'white-male-1847732_640.jpg',
    reviews: [
    …
    ]
  },
  {
    id: '3',
    name: 'Changing Catalog',
    image: 'white-male-1871447_640.jpg',
    reviews: [
    …
    ]
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将这些数据导入到组件中。通常这将通过 ajax 调用以 json 的形式接收，或者如果您使用一些状态管理解决方案，如 redux 或 vuex，通过这些库。但是因为我们编写了一个简单的演示应用程序，所以现在让我们使用 import。我们在项目的/src 目录中创建一个 mock_data.json。让我们也添加一个数据属性到我们的组件。标签内的内容应该是这样的。

```
import TVShows from '../mock_data.js'

export default {
  name: 'TVShowList',
  data: function () {
    return {
      TVShows // Using ES6 here, this line is equivalent to TVShows: TVShows in ES5
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当你在 Vue 中使用组件语法时，你必须对数据属性使用函数，但是你仍然可以从中返回一个对象，这正是我们要做的。

接下来的事情将是组件
的模板工作

```
<ul class="tv_show_list">
  <li class="show_item" v-for="show in TVShows" v-bind:key="show.id">
    <router-link v-bind:to=“`/show/${show.id}`">
      <img v-bind:src="`static/${show.image}`" />
    </router-link>
    {{ show.name }}
  </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 Vue 的 v-for 指令迭代包含在 TVShows 组件的数据属性中的 TVShows 数组。它将下一个显示项放入一个显示变量中，并为数组的每个元素呈现

*   Item. The display variable will have an effect on*   All child nodes of are available. The v-bind:key instruction tells Vue to use the data contained in the show variable as the key of the current HTML element. If you want elements not to be replaced during re-rendering, then this is basically necessary (if you are familiar with React, the v-bind:key instruction has the same goal as the key attribute of React, that is, to keep elements during coordination).

迭代总体上看起来类似于 AngularJS，其中使用指令(ng-repeat)完成，而不是像 React 的 JSX 那样使用数组函数。

在每个 li 元素中，我们都有一个<router-link>组件来包装图像，并从图像中创建一个到相应电视节目的详细页面的链接。我们在 router-link 的 v-bind:to 属性中使用 es6 模板字符串，这允许我们编写更干净的代码。</router-link>

最后，我们渲染一个节目的图像。我们需要将图像放入/static 文件夹，因为 webpack 配置为通过 Vue 模板从该文件夹提供静态资产。但是如果您对 webpack 配置感到满意，您可以将 webpack 配置中的 publicPath 属性更改为您需要的路径。

我们还需要通过在

```
.tv_show_list {
  padding: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.show_item {
  display: flex;
  flex-direction: column;
}
.show_item img {
  height: 200px;
  width: 200px;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们访问我们的应用程序的登录页面时，我们将能够看到一个节目列表，并通过单击节目图像转到一个节目页面(目前每个节目都是相同的，但这是我们要更改的下一个内容)。

同样，我们将在这里导入模拟数据:

```
import TVShows from '../mock_data.js' 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们向 TVShow 组件添加以下属性:

```
created: function () {
  setTimeout(() => {
    this.show = TVShows.find(show => show.id === this.$route.params.id)
  })
},
data: function () {
  return {
    show: {}
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不必在这里使用`created` lifecycle hook，但是我将使用它向您展示如何在一个具有真正后端的应用程序中处理延迟数据。Vue 像 react 一样有生命周期挂钩。我将在这里使用`created`生命周期钩子。它是在创建组件并且数据观察可用时触发的，您可以参考文档()了解详细信息。为了进行 ajax 调用，它对应于 React 中的 componentWillMount。这里我使用 setTimeout 模拟一个 ajax 调用。然后我们找到我们需要的节目。当我们之前宣布电视节目的路线是:

```
{ path: '/show/:id', component: TVShow } 
```

Enter fullscreen mode Exit fullscreen mode

我们使 id 变量在组件内部可用，如下所示。$[route . params . id](http://route.params.id)($ route . params 被添加到一个组件中以引用路由变量)所以现在我们使用它来获得我们需要的节目。

而 data 属性是需要为`show`属性设置默认值的，我们在一个模板中使用，所以 Vue 在使用的时候如果没有定义就会抛出错误。

然后在模板中我们有:

```
<div class="tv_show">
  <div>{{ show.name }}</div>
  <router-link v-bind:to="`/`">
    Home
  </router-link>
  <img v-bind:src="`static/${show.image}`" />
  <h1>Reviews</h1>
  <ul class="show_reviews">
    <li class="show_review" v-for="review in show.reviews” :key=“review.id”>
      <span>{{ review.reviewer }}</span>
      <p>{{ review.text }}</p>
    </li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

向我们展示了这部剧的回顾。我们在这里添加了一个路由器链接组件，这样用户可以在查看完一个特定的节目后返回到节目列表(主页)。此外，在这段代码中需要注意的一点是，我们使用:key 而不是 v-bind:key，这是 Vue 为 v-bind 指令提供的一种语法糖。

最后我们添加样式:

```
.show_reviews {
  list-style: none;
  text-align: left;
}
.show_review {
  border: 1px solid #ddd;
  border-radius: 5px;
  margin-bottom: 5px;
  padding: 5px;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们用 Vue 制作的 2 路简单入门应用已经准备好了。你可以在 Github 上查看应用的代码:[https://Github . com/jsmegatools/creating-a-simple-app-with-vue . js](https://github.com/jsmegatools/creating-a-simple-app-with-vue.js)