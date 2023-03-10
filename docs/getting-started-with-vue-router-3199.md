# Vue 路由器入门

> 原文：<https://dev.to/wilburpowery/getting-started-with-vue-router-3199>

Vue.js 是我构建动态 Javascript 用户界面的首选框架。我最喜欢 Vue.js 的一点是，它非常容易上手。根据你的需要，你只需要在你的页面上添加一个小的脚本标签，你就可以享受 Vue 的美妙了。

以防你不知道，Vue-Router 是一个 Vue.js 插件，用于为你的应用程序添加路由。在这种情况下，我将与您分享一些关于如何安装和设置 Vue-Router 的信息，以便您可以立即开始构建 SPA。

## 安装它

可以用 npm 或者 yarn 安装`vue-router`。只需在您的终端中运行以下任何命令，您就可以继续运行了。
`npm install vue-router`
或
`yarn add vue-router`

## 配置一下

使用模块系统时，需要告诉 Vue.js，它必须使用 VueRouter 插件。

```
import Vue from 'vue';
import VueRouter from 'vue-router';

Vue.use(VueRouter);

import router from './router';

const app = new Vue({
    el: '#app',
    router, // same as router: router
}); 
```

Enter fullscreen mode Exit fullscreen mode

在配置 vue 使用 Vue 路由器插件之后，您需要让父组件知道它应该使用那个路由器实例。

按照前面的步骤，现在已经安装并配置了`vue-router`,可以开始构建路由配置了。

## 构建您的应用

有许多方法可以构建您的应用程序，但我主要选择如下方式:

*   我在目录中有一个`router/`文件夹，用来保存我的脚本。
*   在`router/`文件夹中，我创建了两个文件，`index.js`和`routes.js`

### `index.js`

首先，我选择将这个文件命名为 index.js 是因为在使用模块系统时，我可以简单地使用下面的命令来导入它:`import router from './router'`。注意我不需要指定文件名，因为默认情况下模块系统会寻找一个`index.js`文件。

```
// router/index.js
import VueRouter from 'vue-router'; //Import vue-router
import routes from './routes'; // Import my routes

//Export a new VueRouter instance
export default new VueRouter({
    mode: 'history',
    base: '/home/',
    routes,
}); 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，有些事情你可能不太清楚。让我逐一解释。
默认情况下，`mode`属性被设置为*哈希模式*，这将为 url 添加一个`#`。在我的例子中，我将它设置为`history`模式来防止这种情况，并利用 history.pushState API 来实现 URL 导航，而无需重新加载页面。
`base`属性是设置一个基础 url 段，所有的路由都将从该段开始延伸。例如，如果我们设置了一条到`/dashboard`的路由，vue-router 实际上会寻找 url `/home/dashboard`。如果你不想要那种行为，你可以省略这两个属性。

```
// router/routes.js

// Here we import our Vue Components
import DashboardPage from '../pages/Dashboard.vue';
import Error404 from '../pages/errors/Error404.vue';

// the routes are simply an array of objects
const routes = [
    {
        path: '/',
        redirect: '/dashboard'
    },
    {
        path: '/dashboard',
        name: 'dashboard',
        component: DashboardPage
    },
    {
        path: '*',
        name: '404',
        component: Error404
    }
];

export default routes; 
```

Enter fullscreen mode Exit fullscreen mode

大多数 Route 对象需要至少有两个属性，它应该匹配的`path`和当路径匹配时它应该显示的`component`。
使用通配符`*`作为路径的数组中的最后一个对象，用于显示任何不匹配的路由的 404 页面。您不需要拥有它，它只是一个如何处理路由器 404 错误的例子。

## 好了，我已经设置好了，现在我该如何使用它呢？

使用 Vue Router 提供的两个开箱即用的特殊元素来导航您定义的路线非常简单，它们是`<router-link></router-link>`和`<router-view></router-view>`。

默认情况下，`router-link`组件呈现一个 html `a`标签。您可以使用`tag`属性指定想要呈现的元素。`to`属性用于指定点击元素后应该遵循的路线。

```
<ul>
    <router-link tag="li" to="/dashboard" class="list-group-item">
        Dashboard
    </router-link>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

在看到页面上呈现的每个组件之前，需要使用`<router-view>`元素。使用这个元素非常简单；只需将元素添加到页面中希望安装组件的任何位置。

```
<div class="row">
    <div class="col-md-12">
        <!--All components will be mounted here-->
        <router-view></router-view>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

Vue.js 是一个非常容易使用的 javascript 框架，它的路由器插件再次证明了这一点。只需几秒钟，您就可以轻松地在您的应用程序上安装、配置和运行 javascript 路由器。

这篇帖子里有我漏掉的吗？在 [twitter](https://twitter.com/wilburpowery) 上与我分享