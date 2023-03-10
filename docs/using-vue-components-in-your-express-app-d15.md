# 在您的 Express 应用中使用 Vue 组件

> 原文：<https://dev.to/hyra/using-vue-components-in-your-express-app-d15>

VueJS 很牛逼。创建自包含组件并在页面中将它们组合在一起非常有意义，因此我在最近的项目中广泛使用了它。但是，有时候你想(或者需要)创建一个传统的 app，又不能用热重装和的 SPA 工作流开发。vue 文件。

还是可以？ðŸ "

> TL；DR:我准备了一个带有完整示例代码的 repo，以防你想直接进入 Github

Laravel 已经获得了与 Vue 紧密集成的现成组件，你可以相对容易地在你的`blade`模板中注册和使用`.vue`组件。我选择武器通常是表达，因为我是一个 Javascript 爱好者ðÿ”。我开始尝试这是否可能，并花了两个晚上的时间尝试同样的工作。成功了！下面的步骤让您的下一个 Express 应用程序也能正常运行。

## 定义一些目标

在着手想出一个解决方案之前，我必须确定我心中的目标。我想到了以下几点:

*   **能够使用`.vue`方法**
    编写组件当然，在一个大文件中编写冗长的 javascript 并定义组件是可能的，但是我想使用。将`<template>`、`<script>`和`<style>`组合在一个文件中的 vue 方法。

*   **通过把`<custom-tag>`放入我的视图文件**
    来使用组件注册和编译是一回事，但是能够使用它们是另一回事。我不想使用 Vue 代码来显式呈现组件

*   **使用预处理器的能力**
    我喜欢用`scss`格式编写我的风格，所以这应该得到支持。

*   一个简单的编译工具
    当开发 SPA 项目时，编译工具变得相当冗长和复杂。对于我想使用的项目，构建工具中的组件应该很简单。

*   **额外功能:热重装**
    剧透提醒:我并没有把这个作为一个要求，但它确实有效，而且很棒。

## 设置舞台

所以让我们开始吧。显然，我们需要一个起点。启动新的 Express 应用程序最简单的方法是使用它的生成器。基本上，这将创建一个空项目，您可以从它开始构建。如果你还没有它，下面是你如何安装它:

```
npm install express-generator -g 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们运行生成器来创建我们的项目:

```
express --view=pug myapp 
```

Enter fullscreen mode Exit fullscreen mode

我使用`pug`是因为我喜欢干净的语法。虽然我知道很多人讨厌它，所以如果你是其中之一，就忽略参数ðÿ˜‰

生成器已经创建了一个文件夹`myapp`和一个已经准备好的应用程序，只需运行:

```
cd myapp
yarn install # or npm
yarn start 
```

Enter fullscreen mode Exit fullscreen mode

这将只是安装依赖项并启动 Express 服务器。如果一切顺利，浏览到 http://localhost:3000 应该会给你新应用的欢迎页面。

## 添加 Vue

随着基础的建立，是时候给项目添加一些 Vue 了。让我们先引入 Vue 依赖:

```
yarn add vue 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要创建我们的入口 Javascript 文件。我们最终会将所有引用的 javascript 代码捆绑到一个文件中，所以这将是我们的主文件。我在`public/javascripts/main.js`中创建了一个文件。在这里输入以下内容:

```
var Vue = require('vue/dist/vue.js')

const app = new Vue({
  el: '#app'
}) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们引入了`vue/dist/vue.js`，而不仅仅是`vue`。这是因为默认情况下，Vue 包括仅运行时版本，这意味着我们不能像我们希望的那样使用模板。vue 文件。

我们还把我们的 Vue 应用程序挂载到一个 id 为`app`的元素上，所以修改`views/layout.pug`来包含那个

```
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
+   #app
      block content 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个构建脚本

现在，要使用我们的 javascript，我们需要捆绑它并将其包含在我们的模板中。我通常的选择是 webpack，但是配置这个很麻烦，而且说实话，我不能让它工作。出于某种原因，它将包括默认的 vue 版本，这意味着我们不能使用。vue 文件，主要目标之一。

幸运的是，有一个很好的工具叫做 [Vueify](https://github.com/vuejs/vueify) ，我们可以用它来编译我们的 javascript。作为奖励，它支持预处理器，如 SASS，Styuls，甚至模板语言，如 Pug。赢了。这是 Browserify 的一个转变，所以我们两者都需要。

安装很简单:

```
yarn global add browserify # We want this global, to run is easily
yarn add vueify 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以开始编译了:

```
browserify -t vueify -e public/javascripts/main.js -o public/javascripts/bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，这会用编译后的代码创建一个新文件`bundle.js`。让我们不要忘记将它包含在我们的布局中:

```
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    #app
      block content
+   script(src="/javascripts/bundle.js") 
```

Enter fullscreen mode Exit fullscreen mode

## 写一个组件

接下来是我们期待已久的部分..一个真实的组件！让我们添加一个文件`public/javascripts/components/cruelWorld.vue`

```
<template>
    <div class="cruel">
        Cruel
        <span>{{ who }}</span>
    </div> </template> 
<script>
module.exports = { // This is important, I got errors when using `export default`
    data () {
        return {
            who: "World"
        }
    },
    mounted () {
        setTimeout(() => {
            console.log('We can use ES6 too!')
        }, 1000)
    }
}
</script> 
<style scoped>
.cruel {
    color: green;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这是你的基本 Vue 组件。我们有一个简单的模板和一些数据，它安装后，我们在 1 秒钟后注销一条消息，以显示箭头功能也可以工作。

让我们将它添加到我们的`main.js`中，这样我们就可以使用它:

```
var Vue = require('vue/dist/vue.js')

Vue.component('cruelWorld', require('./components/cruelWorld.vue'));

const app = new Vue({
  el: '#app'
}) 
```

Enter fullscreen mode Exit fullscreen mode

并通过再次运行命令来编译它:

```
browserify -t vueify -e public/javascripts/main.js -o public/javascripts/bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

## 包括组件

很好。所以我们得到了我们的设置，我们得到了我们的组件。现在让我们使用它。打开`views/index.pug`并添加组件:

```
extends layout 
block content
  h1= title
+ cruel-world
  p Welcome to #{title} 
```

Enter fullscreen mode Exit fullscreen mode

启动网络服务器:

```
yarn start 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，你应该会看到我们的组件充满活力！

现在，我们也可以使用预处理器。例如，我们可以将`lang="scss"`添加到我们的`<style>`标签中，以使用 SASS 语法:

```
<template>
    <div class="cruel">
        Cruel
        <span>{{ who }}</span>
    </div> </template> 
<script>
module.exports = {
    data () {
        return {
            who: "World"
        }
    },
    mounted () {
        setTimeout(() => {
            console.log('We can use ES6 too!')
        }, 1000)
    }
}
</script> 
<style scoped lang="scss"> <!-- add this attribute -->
.cruel {
    color: green;
    span { <!-- nested scss syntax! -->
        color: orange;
    }
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

在编译这个之前，我们需要快速添加`node-sass`作为依赖:

```
yarn add node-sass 
```

Enter fullscreen mode Exit fullscreen mode

现在，当你编译并运行时，你应该看到 SASS 被正确编译，你的组件变得更加丰富多彩。

这太棒了。我们可以写作。vue 组件，然后在我们的传统应用程序中使用它们。我们可以就此打住，但是让我们再添加一个特性..

## 加成:热重装

这只是锦上添花。能够更新我们的组件并在浏览器中直接看到结果。

打开您的`package.json`并将下面的**开发**脚本添加到`scripts`部分:

```
"scripts":  {  "start":  "node ./bin/www",  "dev":  "watchify -vd -p browserify-hmr -t vueify -e public/javascripts/main.js -o public/javascripts/bundle.js & node ./bin/www"  }, 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本看起来令人望而生畏，但是非常简单。基本上，它使用 **watchify** 来监视代码变化，并通过 browserify-hmr(热模块重载)将 **vueify** 的结果代理到我们的页面。太棒了。

不要忘记安装它所依赖的依赖项:

```
yarn add watchify
yarn add browserify-hmr 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您启动服务器时..

```
yarn dev 
```

Enter fullscreen mode Exit fullscreen mode

它将编译我们的 javascript 并观察变化。尝试一下，对`cruelWorld.vue`进行更改，并在浏览器中自动查看更改！

## 结论

我很高兴我能让这个工作。有时候用单页 app 全力以赴是没有意义的。能够在您的传统项目中仍然使用 Vue 组件就是黄金。

如果你想看上面的完整源代码，我已经在 [Github](https://github.com/Hyra/vue-components-in-express) 准备了一个存储库，你可以用它作为参考或起点。

希望以上有所帮助！

如果你有任何问题，你可以在[https://twitter.com/hyra](https://twitter.com/hyra)找到我