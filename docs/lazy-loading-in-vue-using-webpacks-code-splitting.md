# 使用 Webpack 的代码分割在 Vue 中延迟加载

> 原文：<https://dev.to/alexjoverm/lazy-loading-in-vue-using-webpacks-code-splitting>

当一个 [Vue](https://vuejs.org/) 应用变大时，使用 [Webpack 的代码分割](https://webpack.js.org/guides/lazy-loading/)的懒惰加载组件、路由或 [Vuex](https://vuex.vuejs.org/en) 模块将通过仅在需要时加载代码片段来提升它。

我们可以在 Vue 应用中的 3 个不同级别应用延迟加载和代码分割:

*   组件，又称[异步组件](https://vuejs.org/v2/guide/components.html#Async-Components)
*   路由器
*   Vuex 模块

但是它们都有一个共同点:它们使用了[动态导入](https://github.com/tc39/proposal-dynamic-import)，Webpack 从版本 2 开始就理解这一点。

## Vue 组件中的惰性负载

这在 Egghead 上的[“当需要 Vue 异步组件时加载组件”](https://egghead.io/lessons/load-components-when-needed-with-vue-async-components)中有很好的解释。

就像注册组件时使用`import`函数一样简单:

```
Vue.component('AsyncCmp', () => import('./AsyncCmp')) 
```

Enter fullscreen mode Exit fullscreen mode

并使用本地注册:

```
new Vue({
  // ...
  components: {
    'AsyncCmp': () => import('./AsyncCmp')
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

通过将`import`函数包装成一个箭头函数，Vue 将只在它被请求时才执行它，在那个时刻加载模块。

如果组件导入使用的是名为 export 的[，您可以在返回的承诺上使用对象析构。例如，对于来自](http://2ality.com/2014/09/es6-modules-final.html#named-exports-several-per-module) [KeenUI](https://github.com/JosephusPaye/Keen-UI) :
的 UiAlert 组件

```
...
components: {
  UiAlert: () => import('keen-ui').then(({ UiAlert }) => UiAlert)
}
... 
```

Enter fullscreen mode Exit fullscreen mode

## Vue 路由器中的惰性加载

Vue 路由器内置了对[延迟加载](https://router.vuejs.org/en/advanced/lazy-loading.html)的支持。这就像用`import`函数导入组件一样简单。假设我们想在*/登录*路径:
中延迟加载一个登录组件

```
// Instead of: import Login from './login'
const Login = () => import('./login')

new VueRouter({
  routes: [
    { path: '/login', component: Login }
  ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 懒惰加载一个 Vuex 模块

Vuex 有一个`registerModule`方法，允许我们动态创建 Vuex 模块。如果我们考虑到`import`函数返回一个以 ES 模块为有效负载的承诺，我们可以用它来延迟加载一个模块:

```
const store = new Vuex.Store()

...

// Assume there is a "login" module we wanna load
import('./store/login').then(loginModule => {
  store.registerModule('login', loginModule)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

Vue 和 Webpack 使得延迟加载变得非常简单。使用你刚刚读到的内容，你可以开始从不同的方面将你的应用程序拆分成块，并在需要时加载它们，减轻应用程序的初始负载。

喜欢就去分享吧！你可以在这个博客或 twitter 上关注我，账号是 [@alexjoverm](https://twitter.com/alexjoverm) 。有什么问题吗？开枪！

* * *

*原载于[alexjoverm . github . io](https://alexjoverm.github.io/2017/07/16/Lazy-load-in-Vue-using-Webpack-s-code-splitting?utm_source=devto)2017 年 7 月 7 日。*