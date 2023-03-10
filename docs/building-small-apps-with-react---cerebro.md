# 使用 React - Cerebro 构建小型应用程序

> 原文：<https://dev.to/brainmaestro/building-small-apps-with-react---cerebro>

*原贴[此处](https://github.com/BrainMaestro/primer/blob/master/react/building-small-apps-with-react-cerebro.md)。*

React 是一项了不起的工程，它允许开发者实现易于操作和推理的声明式用户界面。第一次接触( [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html) )可能会令人望而生畏，但是 API 面很小，概念也很少，足以让开发人员快速提高工作效率。这个系列将主要是用 React 构建小的*有用的*应用程序的演练(你期望什么？)和各种各样的技术，同时还涉及一些有用的概念。系好安全带。

通常，教程以一些陈词滥调开始，如待办事项或计数器；这些对学习基本概念是有用的，但不足以满足我们的需求。我们需要更有挑战性的东西。进入[脑波强化器](https://github.com/KELiON/cerebro):一个跨平台*可扩展的*电子应用程序，它提供了完美的工具来构建一些小但有用的 React。它有许多[插件](https://github.com/lubien/awesome-cerebro)，有些有用，有些没用，但我们希望通过创建一个简单的天气插件来为前者做出贡献。

要继续，你需要基本了解 React(惊喜惊喜)、JavaScript、 [JSON](http://www.json.org/) 和[REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)是如何工作的。如果没有这些，你可能会有点挣扎，但不要担心，互联网是一个奇妙的地方，有[许多](https://facebook.github.io/react/tutorial/tutorial.html) - [的](https://axiacore.com/blog/beginners-introduction-reactjs/) - [有用的](https://www.codementor.io/reactjs/tutorial/the-reactjs-quick-start-guide) - [资源](https://github.com/enaqx/awesome-react#react-general-tutorials)带你跟上速度。

现在我们可以做一些设置:

1.  下载并安装最新版本的脑波强化器。
2.  按照[说明](https://github.com/KELiON/cerebro/blob/master/docs/plugins/boilerplate.md)设置新插件。
3.  通读[插件结构](https://github.com/KELiON/cerebro/blob/master/docs/plugins/plugin-structure.md)以很好地了解插件 API 允许什么。

我们的插件，**脑波气象**，将允许用户搜索一个城市，然后显示该城市天气状况的简明预览，如果它存在的话。这些数据将由来自[气象](https://www.metaweather.com/api/)的优秀 API 提供支持。首先将以下内容添加到您的`index.js`文件中:

```
export const fn = ({ term, display }) => {
  display({
    title: `Weather in ${term}`,
    subtitle: 'Cloudy with a chance of meatballs',
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这使用了 ES2015 的一项功能——对象析构，来从脑波强化器传递到插件的范围对象中检索`display`函数和`term`。这比总是键入`scope.display`或`scope.term`更容易。如果一切设置正确，当您在脑波强化器中输入“伦敦”时，您应该会看到:

[![Cloudy](img/2774f48be4fd9733e22375bae9d65394.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VgEjImmn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/BrainMaestro/primer/master/.assets/react/build-cerebro/cloudy.png)

[T2`fd738bd: Up and running`](https://github.com/BrainMaestro/cerebro-weather/commit/fd738bdeb7c0029ae392cc57a549fe501fa9a2c8)

现在，我们可以添加一个预览，这是我们的插件的用户界面将被显示。创建一个`preview.js`文件，并向其中添加以下内容:

```
import React, { Component, PropTypes } from 'react'

export default class Preview extends Component {
  render() {
    const { city } = this.props

    return (
      <div>
        {city == 'london' ? 'Endless Rain' : 'Look Outside'}
      </div>
    )
  }
}

Preview.propTypes = {
  city: PropTypes.string.isRequired,
} 
```

Enter fullscreen mode Exit fullscreen mode

这是基本的东西。在这个代码片段中，我们简单地创建了一个 react 组件，它定义了一个`city`属性，即用户从索引文件中输入的`term`。它呈现一个带有文本的简单 div。更新`index.js`显示新的预览:

```
+import React from 'react'
+import Preview from './preview' 
export const fn = ({ term, display }) => {
  display({
    title: `Weather in ${term}`,
    subtitle: 'Cloudy with a chance of meatballs',
+    getPreview: () => <Preview city={term} />
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这应该给我们这样的东西:

[![Rain](img/b7fa3cd4e151bdb21f90043e8346dbd4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pPsVtz9O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/BrainMaestro/primer/master/.assets/react/build-cerebro/rain.png)

[T2`37dce96: Dark and gloomy too`](https://github.com/BrainMaestro/cerebro-weather/commit/37dce96f6746d313dd2dc6787138cfd3652f6129)

完成后，我们现在可以用 [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) API 从 MetaWeather 获取实际数据。用以下内容创建一个`api.js`文件:

```
const BASE_URL = 'https://www.metaweather.com/api/location';

export const getWeather = city => {
  return fetch(`${BASE_URL}/search/?query=${city}`)
    .then(response => response.json())
    .then(results => fetch(`${BASE_URL}/${results[0].woeid}`))
    .then(response => response.json())
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数向 API 发出一个初始请求，搜索一个给定的城市。如果城市存在，则返回位置数组。我们将这个响应转换为 JSON，并使用位置的`woeid`检索第一个位置的天气细节，这是它的惟一标识符，也是从 API 检索天气数据的唯一实际方法。这个最终响应然后被转换成 JSON，并显示在我们的`Preview`组件的列表中。

```
import React, { Component, PropTypes } from 'react'
import { getWeather } from './api'

const headers = {
  weather_state_name: 'State',
  applicable_date: 'Date',
  min_temp: 'Min Temp',
  max_temp: 'Max Temp',
  the_temp: 'Temp',
  humidity: 'Humidity',
}

export default class Preview extends Component {
  constructor(props) {
    super(props)
    this.state = {
      weather: null,
    }
  }

  componentDidMount() {
    getWeather(this.props.city).then(weather => this.setState({ weather }))
  }

  render() {
    if (! this.state.weather) return <div>Loading...</div>

    const weather = this.state.weather.consolidated_weather[0]

    return (
      <ul>
        {Object.keys(headers).map(metric => (
          <ul key={metric}>{`${headers[metric]}: ${weather[metric]}`}</ul>
        ))}
      </ul>
    )
  }
}

Preview.propTypes = {
  city: PropTypes.string.isRequired,
} 
```

Enter fullscreen mode Exit fullscreen mode

这将使用我们的 API 函数获取天气数据，并显示第一份综合天气报告。最终，我们将显示所有可用的天气报告。这是我们现在应该有的:

[![Ugly Duckling](img/18a14c15a540093b838abe8625ac93dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mIFBzQi6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/BrainMaestro/primer/master/.assets/react/build-cerebro/ugly-duckling.png)

[T2`d144c25: The Awwward is coming home`](https://github.com/BrainMaestro/cerebro-weather/commit/d144c2512b12e44b8cac8c02359e79aa653031f9)

从我们的 API 调用返回的数据包含了比我们需要的更多的信息，所以使用`headers` object，我们将只过滤掉我们需要的数据；在使用外部 REST APIs 时，这是一个反复出现的主题。

我们的应用程序迫切需要造型，所以让我们拉一个包来帮助这一点。运行`npm install bootstrap@4.0.0-alpha.6 --save`。用`import styles from 'bootstrap/dist/css/bootstrap.css'`导入 boostrap css，将渲染函数的返回部分改为:

```
return (
  <table className={styles.table}>
    <thead>
      <tr>
        {Object.keys(headers).map(header => <th key={header}>{headers[header]}</th>)}
      </tr>
    </thead>
    <tbody>
      <tr>
        {Object.keys(headers).map(metric => (
          <td key={metric}>{weather[metric]}</td>
        ))}
      </tr>
    </tbody>
  </table>
) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道`className={styles.table}`是什么，它是一个 [CSS 模块](https://github.com/css-modules/css-modules),是 React 中众多样式之一。好吧，绕道过去。现在我们有了一个更好看的应用程序。 [![Better looking](img/913b89551370997420f0471478c2bb95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bAEJBKW4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/BrainMaestro/primer/master/.assets/react/build-cerebro/better-looking.png)

[T2`4de443d: Too many zeroes`](https://github.com/BrainMaestro/cerebro-weather/commit/4de443d3ec85b29d3fa1ff2804a0e54235ec6540)

我们现在可以专注于呈现剩余的天气报告。为了简洁起见，我将只包括变化。

```
const weatherReports = this.state.weather.consolidated_weather 
```

Enter fullscreen mode Exit fullscreen mode

```
<tbody>
  {weatherReports.map((report, index) => (
    <tr key={index}>
      {Object.keys(headers).map(metric => {
        let value = report[metric]
        if (typeof value == 'number' && value % 1 != 0) {
          value = value.toFixed(2)
        }

        return <td key={metric}>{value}</td>
      })}
    </tr>
  ))}
</tbody> 
```

Enter fullscreen mode Exit fullscreen mode

在第一个代码片段中，我们停止挑选`consolidated_weather`数组的第一个元素，而是选择整个元素。除了显示额外的报告之外，该函数现在将十进制数字格式化为只有两位小数。这给了我们:

[![Progress](img/85064fb4b385a8c8a0002090d9120d95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hMWs0qkP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/BrainMaestro/primer/master/.assets/react/build-cerebro/progress.png)

[T2`b6419d6: The more the merrier`](https://github.com/BrainMaestro/cerebro-weather/commit/b6419d6c2a0af8dd4add89513dbc61b09f285a96)

你不得不承认这要好得多，实际上也很有用。此外，对温度标题做了一个小改动，增加了单位`(c)`。虽然这很好，但我们可以再做一点调整来改善总体外观和体验。在我们的 headers 对象`weather_state_abbr: '#'`的顶部添加一个新的标题。这将用于显示天气状况的小图像表示。将以下代码添加到您的渲染函数中，就在第一个`if`语句的下方，用于格式化小数。

```
if (metric == 'weather_state_abbr') {
  value = <img src={`https://www.metaweather.com/static/img/weather/${value}.svg`} />
} 
```

Enter fullscreen mode Exit fullscreen mode

它使用 MetaWeather 的 SVG 图像为我们提供了:

[![Finale](img/cf8ff0170495c45b5fa6713b43beea92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ur-fbAQt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/BrainMaestro/primer/master/.assets/react/build-cerebro/finale.png)

[T2`7cc836c: Exit stage left`](https://github.com/BrainMaestro/cerebro-weather/commit/7cc836ce6980b5ee80b6461e36b00be8ff75999b)

相当不错！我们已经做了很多，但和软件一样，它可以进一步改进。显示城市和国家将是一个开始，还有时区，但是我将把这些留给读者去实现。欢迎在 [github repo](https://github.com/BrainMaestro/cerebro-weather/) 上提出建议、反馈和 PRs。感谢阅读。