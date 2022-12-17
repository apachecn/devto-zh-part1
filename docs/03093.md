# 通过 vuex 模块和 Nuxt 使用“vuex-router-sync”

> 原文：<https://dev.to/saul/using-vuex-router-sync-with-vuex-modules-and-nuxt-3obn>

这个场景相对简单:我们有一个位于`~/store/projects.js`的 Vuex 模块，我们想要定义一个名为`currentProject`的 getter，它是基于当前路由参数的`.find()`项目。

我们将从安装 [`vuex-router-sync`](https://github.com/vuejs/vuex-router-sync) 模块开始:

```
> yarn add vuex-router-sync 
```

Enter fullscreen mode Exit fullscreen mode

然后我们定义一个 Nuxt.js [插件](https://nuxtjs.org/guide/plugins)来`sync()`商店和路由器:

```
// ~/plugins/vuex-router-sync.js
import { sync } from "vuex-router-sync";

export default ({ app: { store, router } }) => {
  sync(store, router);
}; 
```

Enter fullscreen mode Exit fullscreen mode

(来源于此 [GitHub 问题](https://github.com/nuxt/nuxt.js/issues/213)。)

为了在初始化期间运行，我们需要在我们的 Nuxt.js 配置中声明它:

```
// nuxt.config.js
module.exports = {
  plugins: ["~/plugins/vuex-router-sync"]
}; 
```

Enter fullscreen mode Exit fullscreen mode

模块“route”现在在商店中可用，并与`vue-router`实例保持同步。

回到我们的“项目”Vuex 模块，我们现在能够基于当前路由参数`id` :
定义一个项目的 getter

```
// ~/store/projects.js
export const state = () => ({ projects: [] });

export const getters = {
  currentProject: (state, getters, rootState) =>
    state.projects.find(project => project.id === rootState.route.params.id)
}; 
```

Enter fullscreen mode Exit fullscreen mode