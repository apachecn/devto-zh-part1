# 让我们用 Vue、Chart.js 和一个 API 来构建一个 Web 应用程序

> 原文：<https://dev.to/apertureless/lets-build-a-web-app-with-vue-chartjs-and-an-api>

数据是美好的。借助现代技术，可视化数据和创造美好体验变得异常容易。在这个快速操作指南中，我们将介绍如何与 NPMðÿ'˜API 进行交互，以获取软件包的下载统计数据，并使用 Chart.js 从这些数据中生成一个图表

# 灵魂快速启动

我们将建造[npm-stats.org](http://npm-stats.org/#/)，并将使用以下工具:

*   vista . js with vista 路由器
*   Chart.js
*   view-chartjs(视图-图表)
*   视图-cli
*   阿克斯

使用 Vue.js，我们将构建应用程序的基本接口，并使用`vue-router`进行路由。我们用`vue-cli`搭建我们的项目，它创建了我们的基本项目结构。对于图表生成，我们将使用 Chart.js 并将其作为 Vue 的包装器，`vue-chartjs`。因为我们需要与 API 交互，所以我们使用`axios`来发出 http 请求。然而，你可以随意将这个库与其他库交换。

# 【ðÿ】安装&设置

首先，我们需要安装`vue-cli`来支撑我们的项目。我希望您已经安装了 node 和 npm 的最新版本！ðÿ™更好，如果你有纱线安装！如果没有，你真的应该！如果你不想要，就用 npm 的等价物替换掉 yarn 命令。

```
$ npm install -g vue-cli 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以用 vue-cli 搭建我们的项目。如果你想可以启用单元和 e2e 测试，但是我们不会覆盖它们。ðÿ“但是你需要检查路由器！

```
$ vue init webpack npm-stats 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将 cd 放入我们的项目文件夹，并用`cd npm-stats && yarn install`安装依赖项。这样，我们的基本项目依赖项就安装好了。现在我们需要为我们的应用程序添加一个。

```
$ yarn add vue-chartjs chart.js axios 
```

Enter fullscreen mode Exit fullscreen mode

快速检查一下`yarn run dev`是否一切正常。现在我们应该看到 vue 的样板页面。

我们完事了。ðŸ'

# ðÿ'时间建造

这里只是一个小小的免责声明，我不会把重点放在造型上。我猜你可以通过自己的ðÿ'…让网站看起来更好，所以我们只讨论 javascript 相关的代码。
另一个声明，这是一个相当小的 MVP，然后现在超级干净的代码。我将在后面的阶段重构其中的一些。就像在现实世界里一样。

## 组件

让我们想想我们需要什么组件。正如我们在屏幕截图中看到的，我们看到了一个输入您正在寻找的包名的输入框和一个按钮。可能是页眉和页脚以及图表本身。

你完全可以把按钮和输入框做成一个组件，但是我们并没有构建一个复杂的应用程序，为什么要这么麻烦呢？简单点。让它工作！

所以我最后用了以下组件:

*   components/Footer.vue
*   组件/Header.vue
*   components/LineChart.vue
*   页面/主页视图

我将跳过页眉和页脚，因为它们只包含徽标和一些链接。这里没什么特别的。折线图和起始页是最重要的。

## [t1 线路图](#linechart)

LineChart 组件将是我们呈现图表的 chart.js 实例。我们需要导入 Line 组件并扩展它。我们现在创建两个道具。一个是数据，即下载次数，另一个是标签，例如日、周、年。

```
props: {
 chartData: {
   type: Array,
   required: false
 },
 chartLabels: {
   type: Array,
   required: true
 }
}, 
```

Enter fullscreen mode Exit fullscreen mode

因为我们希望所有的图表看起来都一样，所以我们在数据模型中定义了一些 Chart.js 样式选项，这些选项作为选项传递给 **renderChart()** 方法。

因为我们现在只有一个数据集，所以我们可以构建数据集数组并绑定标签和数据。

```
<script>
  import { Line } from 'vue-chartjs'
  export default Line.extend({
    props: {
      chartData: {
        type: Array | Object,
        required: false
      },
      chartLabels: {
        type: Array,
        required: true
      }
    },
    data () {
      return {
        options: {
          scales: {
            yAxes: [{
              ticks: {
                beginAtZero: true
              },
              gridLines: {
                display: true
              }
            }],
            xAxes: [ {
              gridLines: {
                display: false
              }
            }]
          },
          legend: {
            display: false
          },
          responsive: true,
          maintainAspectRatio: false
        }
      }
    },
    mounted () {
      this.renderChart({
        labels: this.chartLabels,
        datasets: [
          {
            label: 'downloads',
            borderColor: '#249EBF',
            pointBackgroundColor: 'white',
            borderWidth: 1,
            pointBorderColor: '#249EBF',
            backgroundColor: 'transparent',
            data: this.chartData
          }
        ]
      }, this.options)
    }
  })
</script> 
```

Enter fullscreen mode Exit fullscreen mode

# ðÿ《我们的起始页》

因为我们的折线图组件已经启动并开始工作。是时候构建其余部分了。我们需要一个输入字段和按钮来提交包名。然后请求数据并将数据传递给我们的图表组件。

所以，我们先想想我们需要什么数据，什么状态/数据模型。首先，我们需要一个`package`数据模型，我们将在输入字段中使用 v-model。我们还希望将包的名称显示为标题。所以`packageName`会很好。然后我们的两个数组用于请求的数据`downloads`和`labels`，当我们请求一个时间段时，我们需要设置`period`。但是，也许请求出错了，所以我们需要`errorMessage`和`showError`。最后但同样重要的是`loaded`，因为我们希望仅在请求发出后才显示图表。

## npm API

有各种端点可以下载一个包。一个是例如

```
GET https://api.npmjs.org/downloads/point/{period}[/{package}] 
```

Enter fullscreen mode Exit fullscreen mode

然而，这个只有一个点的价值。所以总下载量。但是要绘制我们的酷图，我们需要更多的数据。所以我们需要范围端点。

```
GET https://api.npmjs.org/downloads/range/{period}[/{package}] 
```

Enter fullscreen mode Exit fullscreen mode

周期可以定义为例如`last-day`或`last-month`或特定的日期范围`2017-01-01:2017-04-19`，但是为了简单起见，我们将默认值设置为`last-month`。稍后在**第二部分**我们可以添加一些日期输入字段，这样用户可以设置日期范围。

所以我们的数据模型看起来是这样的:

```
data () {
 return {
  package: null,
  packageName: â€˜’,
  period: â€˜last-month’,
  loaded: false,
  downloads: [],
  labels: [],
  showError: false,
  errorMessage: â€˜Please enter a package name’
 }
}, 
```

Enter fullscreen mode Exit fullscreen mode

# ðÿ'…模板

现在是时候构建模板了。我们需要 5 样东西:

*   输入栏
*   按钮来触发搜索
*   错误消息输出
*   带有包名的标题
*   我们的图表。

```
<input
 class=”Search__input”
 @keyup.enter=”requestData”
 placeholder=”npm package name”
 type=”search” name=”search”
 v-model=”package”
 >
<button class=”Search__button” @click=”requestData”>Find</button>
<div class="error-message" v-if="showError">
  {{ errorMessage }}
</div>
<h1 class="title" v-if="loaded">{{ packageName }}</h1>
<line-chart v-if="loaded" :chart-data="downloads" :chart-labels="labels"></line-chart> 
```

Enter fullscreen mode Exit fullscreen mode

暂时忽略 css 类。我们的输入字段在输入时有一个 keyup 事件。所以如果你按下回车键，你就触发了`requestData()`方法。我们将`v-model`绑定到包

对于潜在的错误，我们有一个条件，只有当`showError`为真时，我们才显示消息。可能出现两种类型的错误。一种是，有人试图在不输入任何名称的情况下搜索包裹，或者他输入了一个不存在的名称。

对于第一种情况，我们有默认的错误消息，对于第二种情况，我们将获取来自请求的错误消息。

因此，我们的完整模板将如下所示:

```
<template>
  <div class="content">
    <div class="container">
      <div class="Search__container">
        <input
          class="Search__input"
          @keyup.enter="requestData"
          placeholder="npm package name"
          type="search" name="search"
          v-model="package"
        >
        <button class="Search__button" @click="requestData">Find</button>
      </div>
      <div class="error-message" v-if="showError">
       {{ errorMessage }}
      </div>
      <hr>
      <h1 class="title" v-if="loaded">{{ packageName }}</h1>
      <div class="Chart__container" v-if="loaded">
        <div class="Chart__title">
          Downloads per Day <span>{{ period }}</span>
          <hr>
        </div>
        <div class="Chart__content">
          <line-chart v-if="loaded" :chart-data="downloads" :chart-labels="labels"></line-chart>
        </div>
      </div>
    </div>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

# ðÿ–JavaScript

现在是编码的时候了。首先，我们将执行 requestData()方法。这相当简单。我们需要向端点发出请求，然后映射传入的数据。在我们的 response.data 中，我们有一些关于包的信息:

[![response.data](img/faddf436d03f30cd94db89caeaa0bd85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VxNUUXK3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/r0WjLCi.png)

比如开始日期、结束日期、包名和下载数组。然而，下载数组的结构是这样的:

```
downloads: [
 {day: â€˜2017–03–20’, downloads: â€˜3’},
 {day: â€˜2017–03–21’, downloads: â€˜2’},
 {day: â€˜2017–03–22’, downloads: â€˜10’},
] 
```

Enter fullscreen mode Exit fullscreen mode

但是我们需要将下载和日期分开，因为对于 chart.js，我们只需要一个包含数据(下载)的数组和一个包含标签(日期)的数组。这是一个简单的地图工作。

```
requestData () {
 axios.get(`https://api.npmjs.org/downloads/range/${this.period}/${this.package}`)
 .then(response => {
   this.downloads = response.data.downloads.map(download => download.downloads)
   this.labels = response.data.downloads.map(download => download.day)
   this.packageName = response.data.package
   this.loaded = true
 })
 .catch(err => {
   this.errorMessage = err.response.data.error
   this.showError = true
 })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们输入一个包名，比如 vue 并按 enter 键，就会发出请求，映射数据并呈现图表！但是等等。你什么也没看见。因为我们需要告诉 vue-router 为我们的起始页设置索引。

在`router/index.js`下，我们导入或寻呼并告诉路由器使用它

```
import Vue from â€˜vue’
import Router from â€˜vue-router’
import StartPage from â€˜@/pages/Start’
Vue.use(Router)
export default new Router({
 routes: [
   {
     path: â€˜/’,
     name: â€˜Start’,
     component: StartPage
   },
 ]
}) 
```

Enter fullscreen mode Exit fullscreen mode

# ðÿ'ž波兰

但是，我们还没有完成。我们有些问题，对吧？首先，如果我们不输入任何名字，我们的应用程序就会崩溃。如果你输入一个新的包并按回车键，我们会有问题。出错后，消息不会消失。

嗯，是时候清理一下了。首先让我们创建一个新的方法来重置我们的状态。

```
resetState () {
 this.loaded = false
 this.showError = false
}, 
```

Enter fullscreen mode Exit fullscreen mode

我们在调用`axios` api 之前在我们的`requestData()`方法中调用它。我们需要检查一下包裹的名称。

```
if (this.package === null 
    || this.package === â€˜’ 
    || this.package === â€˜undefined’) {
  this.showError = true
  return
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们试图搜索一个空的包名，我们会得到一个默认的错误消息。

我知道，我们覆盖了很多，但让我们添加另一个很酷的小功能。我们有`vue-router`，但并没有真正使用它。在我们的根节点`/`处，我们看到了带有输入字段的起始页。搜索之后，我们停留在我们的根页面。但是如果我们可以分享我们与统计数据的链接，那会很酷，不是吗？

因此，在有效的搜索之后，我们将包名添加到我们的 url 中。

`npm-stats.org/#/vue-chartjs`

如果我们单击该链接，我们需要获取包名并使用它来请求我们的数据。
让我们创建一个新方法来设置我们的 url

```
setURL () {
 history.pushState({ info: `npm-stats ${this.package}`}, this.package, `/#/${this.package}`)
 } 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在我们的响应承诺中调用`this.setURL()`。现在，在发出请求后，我们将包名添加到我们的 URL 中。但是，如果我们打开一个新的浏览器标签并调用它，什么也不会发生。因为我们需要告诉`vue-router`我们的`/`之后的所有内容也将指向起始页，并将字符串定义为查询参数。这非常简单。

在我们的`router/index.js`中，我们只需要在 routes 数组中设置另一条路径。我们称之为 param 包。

```
{
  path: â€˜/:package’,
  component: StartPage
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你转到`localhost:8080/#/react-vr`，你会看到起始页。但是没有图表。因为我们需要获取参数并使用它执行我们的请求。

回到我们的`Start.vue`中，我们在挂载的钩子中抓取参数。

```
mounted () {
 if (this.$route.params.package) {
   this.package = this.$route.params.package
   this.requestData()
 }
}, 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！完整文件:

```
 import axios from 'axios'
  import LineChart from '@/components/LineChart'
  export default {
    components: {
      LineChart
    },
    props: {},
    data () {
      return {
        package: null,
        packageName: '',
        period: 'last-month',
        loaded: false,
        downloads: [],
        labels: [],
        showError: false,
        errorMessage: 'Please enter a package name'
      }
    },
    mounted () {
      if (this.$route.params.package) {
        this.package = this.$route.params.package
        this.requestData()
      }
    },
    methods: {
      resetState () {
        this.loaded = false
        this.showError = false
      },
      requestData () {
        if (this.package === null || this.package === '' || this.package === 'undefined') {
          this.showError = true
          return
        }
        this.resetState()
        axios.get(`https://api.npmjs.org/downloads/range/${this.period}/${this.package}`)
          .then(response => {
            console.log(response.data)
            this.downloads = response.data.downloads.map(download => download.downloads)
            this.labels = response.data.downloads.map(download => download.day)
            this.packageName = response.data.package
            this.setURL()
            this.loaded = true
          })
          .catch(err => {
            this.errorMessage = err.response.data.error
            this.showError = true
          })
      },
      setURL () {
        history.pushState({ info: `npm-stats ${this.package}` }, this.package, `/#/${this.package}`)
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 [GitHub](https://github.com/apertureless/npm-stats) 查看完整源代码，在[npm-stats.org](http://npm-stats.org/)ðÿ查看演示页面

# 改进

但是，嘿，仍然有改进的空间。我们可以添加更多的图表。像每月统计，每年统计和添加日期字段来设置期间和许多其他事情。我会在**第二部**中讲述其中的一些！敬请关注！