# 创建 Vuejs 插件

> 原文：<https://dev.to/sadick/creating-a-vuejs-plugin-3cga>

#### 帮助我们在 Vuejs 应用程序中使用 PouchDB 的插件

<figure>[![](img/27eb3ca14aee7d374f224c578c4a519e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K0PKXylc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGRM7VUZFA-y4cR4gFPB_lA.jpeg) 

<figcaption>牛逼的 vuejs 形象</figcaption>

</figure>

这篇文章假设你至少有一些关于 [Vuejs](https://vuejs.org/v2/guide/index.html) 和 [PouchDB](https://pouchdb.com/getting-started.html) 的工作知识。

别担心，在你开始之前，我不会告诉你安装 1000 个东西。你所需要的只是 [Vuejs](https://vuejs.org/js/vue.js) 和 [PouchDB](https://github.com/pouchdb/pouchdb/releases/download/6.0.7/pouchdb-6.0.7.js) 。

插件为您提供了一种向 Vue 添加全局级功能的方式。例如，Vue 本身没有内置的路由系统。Vue 路由器插件为 Vue 增加了路由功能。关于如何创建 vuejs 插件的资源很少，所以我希望这能有所帮助。

在深入构建细节之前，先看看你将如何使用我们即将制作的这个插件。