# 动态自定义谷歌地图标记

> 原文：<https://dev.to/derekbrimley/dynamic-custom-google-map-markers-a4>

我开发了一个带有谷歌地图组件的 React 应用程序(使用了非常棒的 React-谷歌地图包)。地图显示大量实时数据，标记图标是动态的这一点很重要。不幸的是，谷歌地图并没有让这变得容易。标记对象需要一个图像，而不是一个动态的 React 组件，所以我需要一个变通方法来让标记图标反映变化的数据。
地图标记接受图像作为参数，通常是光栅图像的路径。为了具有动态性，它需要是一个 SVG 图像，但这似乎不是一个选项。然而，我的同事告诉我去调查 URIs 数据。经过一些研究，我了解到我可以使用数据 URIs“欺骗”地图标记接受 SVG。
数据 URI 是以特定格式定义图像的字符串。格式就在字符串中指定。字符串大概是这样的:
`data:image/svg;base64,REQFUfju93$T…`
来自 [CSS 窍门](https://css-tricks.com/data-uris/)，字符串的格式应该是:
`data:[<mime type>][;charset=<charset>][;base64],<encoded data>`
可以使用任何类型的数据编码，或者不使用。一个简单的 SVG 数据 URI 可以定义:
`data:image/svg+xml;utf8,<rect stroke='black' fill='black' width='50' height='25'/>`
就这么简单！数据 URIs 令人敬畏有很多原因，其中之一是它们可以使显示图像的速度快得多，因为页面不必发出太多 HTTP 请求。相反，图像只是在代码中定义的。
出于我的目的，我需要图像以字符串的形式出现，所以这似乎是一个完美的解决方案。然而，我仍然需要一种将 React SVG 节点转换成字符串的方法。幸运的是，React 提供了一个简单的函数来将 React 组件转换为字符串。实际上，它们提供了两个:一个叫做`renderToString`，它返回 React 将从该组件创建的 HTML，另一个叫做`renderToStaticMarkup`，它做同样的事情，但是没有所有额外的 React 特有的属性。我不想要任何额外的数据属性，所以我使用 renderToStaticMarkup。
从这里开始，定义一个动态 SVG，通过`renderToStaticMarkup`函数运行它，并创建数据 URI 字符串就相当简单了。如果您愿意，您也可以对该字符串进行编码，但是不要忘记指定您在 URI 中使用的编码。
标记组件最终看起来像这样:

`<Marker icon={"data:image/svg+xml;utf8,<rect stroke='black' fill='black' width='50' height='25'/>"} />`

我遇到的另一个问题是忘记给 SVG 添加`xmlns=http://www.w3.org/2000/svg`。通常这个属性对于在 HTML5 文档中显示 SVG 是不必要的，但是当图像作为数据 URI 时，它仍然是必要的。别忘了！
我仍然是 SVGs 的新手，所以如果我做错了什么，请告诉我！我喜欢反馈。我花了一点时间来适应它们，但我从理解它们中获得了很多乐趣。
如果你感兴趣的话，[这是我正在制作的](https://codepen.io/derekbrimley/pen/PJxwqP?editors=1000)的 SVG 图标的代码。这是一篇关于 URIs 数据的文章。这里有一篇关于 SVG 数据 URIs 的文章。
感谢阅读！