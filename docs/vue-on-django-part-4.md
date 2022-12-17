# 姜戈视图，第 4 部分

> 原文：<https://dev.to/rpalo/vue-on-django-part-4>

这是我制作一个由 Django REST 后端支持的 Vue 应用程序的教程的第 4 部分。终于！我们做到了。你已经做到了。但最重要的是，我做到了。😊在第一部分，我们只设置了 Vue 端。在第 2 部分中，我们建立了数据模型并获得了它，这样我们就可以在浏览器中创建和清除我们的 Todos。在第 3 部分中，我们设置了 Django 后端。第 4 部分将为我们的前端提供一种与后端对话的方式，这样我们的 todos 就可以一直持续下去。本节将进行一些 API 调用，所以对 HTTP 请求和 JavaScript 承诺有所了解会对您有所帮助。如果你对这些不熟悉，你至少应该习惯耸耸肩，跟随我的领导，稍后谷歌一下。

## 0.安装

我们应该只需要为这个部分安装另外一个东西:Vue 资源，它帮助我们发出 HTTP 请求。

显然他们已经贬低了 Vue 资源。参见[这篇关于可能替代方案的文章](https://medium.com/the-vue-point/retiring-vue-resource-871a82880af4)。谢谢@williezh！话虽如此，这个教程应该还是管用的。

```
$ npm install -s vue-resource 
```

就是这样！让我们开始改变吧，好吗？

## 1.客户端 API 实用程序

将名为`api.js`的文件添加到您的`src/store`文件夹中。我们开始吧:

```
// src/store/api.js

import Vue from 'vue'
import VueResource from 'vue-resource'

Vue.use(VueResource)

export default {
  get (url, request) {
    return Vue.http.get(url, request)
      .then((response) => Promise.resolve(response))
      .catch((error) => Promise.reject(error))
  },
  post (url, request) {
    return Vue.http.post(url, request)
      .then((response) => Promise.resolve(response))
      .catch((error) => Promise.reject(error))
  },
  patch (url, request) {
    return Vue.http.patch(url, request)
      .then((response) => Promise.resolve(response))
      .catch((error) => Promise.reject(error))
  },
  delete (url, request) {
    return Vue.http.delete(url, request)
      .then((response) => Promise.resolve(response))
      .catch((error) => Promise.reject(error))
  }
} 
```

这个文件只是用我们将要使用的 HTTP 方法导出一个对象。这样就可以通过`api.post(stuff)`调用了。你会看到一个例子。请记住，本节使用了很多我认为是 JavaScript 的承诺，到目前为止，我对这些承诺的内部工作方式还有点模糊。它在列表上，可以更深入地阅读。

我们还将更新我们的`store.js`文件，以便在我们的操作中使用这些新方法。记住，动作可以是异步的，但是突变必须是同步的。这就是为什么我们从动作内部进行 API 调用，这也是动作存在的重要原因！

```
// src/store/store.js

import Vue from 'vue'
import Vuex from 'vuex'
import api from './api.js'

Vue.use(Vuex)
const apiRoot = 'http://localhost:8000'  // This will change if you deploy later

const store = new Vuex.Store({
  state: {
    todos: []
  },
  mutations: {
    // Keep in mind that response is an HTTP response
    // returned by the Promise.
    // The mutations are in charge of updating the client state.
    'GET_TODOS': function (state, response) {
      state.todos = response.body
    },
    'ADD_TODO': function (state, response) {
      state.todos.push(response.body)
    },
    'CLEAR_TODOS': function (state) {
      const todos = state.todos
      todos.splice(0, todos.length)
    },
    // Note that we added one more for logging out errors.
    'API_FAIL': function (state, error) {
      console.error(error)
    }
  },
  actions: {
    // We added a getTodos action for the initial load from the server
    // These URLs come straight from the Django URL router we did in Part 3
    getTodos (store) {
      return api.get(apiRoot + '/todos/')
        .then((response) => store.commit('GET_TODOS', response))
        .catch((error) => store.commit('API_FAIL', error))
    },
    addTodo (store, todo) {
      return api.post(apiRoot + '/todos/', todo)
        .then((response) => store.commit('ADD_TODO', response))
        .catch((error) => store.commit('API_FAIL', error))
    },
    clearTodos (store) {
      return api.delete(apiRoot + '/todos/clear_todos/')
        .then((response) => store.commit('CLEAR_TODOS'))
        .catch((error) => store.commit('API_FAIL', error))
    }
  }
})

export default store 
```

这份文件有很多改动，但这确实是它的核心部分。从现在开始其他的事情都只是记账。请记住，由于我们设置一切的方式，我们能够将我们的应用程序连接到后端数据库，我们甚至不会触及组件！我认为这是最精彩的部分。

我们还将在底部向我们的`main.js`文件添加一行。当我们的应用程序加载时，在客户端看到它之前，我们希望它做的最后一件事是用保存的 todos 加载 todos 数组。

```
// src/main.js

import Vue from 'vue'
import App from './App'

import store from './store/store.js'

/* eslint-disable no-new */
const v = new Vue({
  el: 'body',
  store: store,
  components: { App }
})

// This should be the only new line ***
v.$store.dispatch('getTodos') 
```

为了让我们的生活更轻松，我们还应该做一件事。打开文件`format_index_html.py`。这里有一些来自 vue 项目模板的错别字，这将使我们的生活变得困难。下面是修复版。加了几个引号就差不多了。

```
import sys
import fileinput

file = 'templates/index.html'

with open(file, "r+") as f:
    s = f.read()
    f.seek(0)
    f.write("{% load staticfiles %}\n" + s)

for i, line in enumerate(fileinput.input(file, inplace=1)):
    sys.stdout.write(line.replace('href=//', "href=\"{% static '"))
for i, line in enumerate(fileinput.input(file, inplace=1)):
    sys.stdout.write(line.replace('css', "css' %}"))
for i, line in enumerate(fileinput.input(file, inplace=1)):
    sys.stdout.write(line.replace('src=//', "src=\"{% static '"))
for i, line in enumerate(fileinput.input(file, inplace=1)):
    sys.stdout.write(line.replace('js', "js' %}\"")) 
```

同样，这段代码附带了 vue 模板，它相当不错(可能不是我最喜欢的)。但它已经为我们写好了，所以我们要继续下去。

## 2.准备好

那应该差不多了。这就是我们开始整件事的方法。

1.  确保您的虚拟环境处于活动状态。

```
$ source .venv/bin/activate
# Windows: .venv\Scripts\activate 
```

1.  运行`./deploy.sh`。如果它抱怨权限问题，要么`chmod`权限，要么直接运行`bash deploy.sh`。在 windows 上，您应该能够手动运行部署脚本中的所有步骤。您唯一应该改变的是运行`python manage.py runserver 8000`，而不是分两步做。

2.  仔细观察所有的输出。如果有任何错误，您将在大量输出中看到它们。

3.  前往 localhost:8000 享受吧！

## 2.5 调试

如果不行，也不要慌。检查您的浏览器控制台。有错误吗？500 性质的错误最有可能是服务器端的。你会想要开发你的 django-rest 应用程序。如果错误在您的 javascript 端，终止任何正在运行的 dev 服务器并运行`npm run dev`来独立运行 vue 服务器。这将无法访问任何服务器功能，但会更容易找到真正的错误消息。

## 3.包裹

这是一个漫长的，希望我没有错过任何东西。因为我把帖子间隔开了(我很后悔)，我不得不玩一些游戏来提醒自己从一部分到另一部分发生了什么变化。所以，如果有什么东西不工作或者坏了，让我知道，我会看看我是否能找到哪里出错了。我在 GitHub 上添加了我的最终项目文件夹[，这样你就可以搜索差异来帮助调试。谢谢你坚持和我在一起！](https://github.com/rpalo/vue-django-example)