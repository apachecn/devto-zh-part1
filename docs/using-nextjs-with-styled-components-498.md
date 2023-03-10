# 对样式化组件使用 Next.js

> 原文：<https://dev.to/bilalbudhani/using-nextjs-with-styled-components-498>

在过去的几个月里，我一直在摆弄 [Next.js](https://github.com/zeit/next.js/) 库，到目前为止，它真的很有趣。

[Styled-Components](https://github.com/styled-components/styled-components) 是另一个很棒的库，我已经开始在我的生产应用程序中使用它，它改进了我的开发周期。

现在让我们谈生意，Next.js 是服务器渲染库，我们需要利用样式化组件[服务器端渲染](https://www.styled-components.com/docs/advanced#server-side-rendering)函数让它们一起工作。

在 Next.js 项目中添加样式化组件包:

```
yarn add styled-components 
```

现在在`pages`文件夹中创建一个名为`_document.js`的新文件，并将下面的代码片段粘贴到其中。

```
import Document, { Head, Main, NextScript } from 'next/document'
import { ServerStyleSheet } from 'styled-components'

export default class MyCustomDocument extends Document {
  static getInitialProps ({ renderPage }) {
    const sheet = new ServerStyleSheet()
    const page = renderPage(App => props => sheet.collectStyles(<App {...props} />))
    const styleTags = sheet.getStyleElement()
    return { ...page, styleTags } // return styles collected
  }

  render () {
    return (
      <html>
        <Head>
          My Page Title
          {this.props.styleTags}
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </html>
    )
  }
} 
```

上面的代码收集了应用程序中使用 styled-components 定义的所有样式，并在页面呈现之前返回它们以填充为`props`。现在在`render`函数中，`{this.props.styleTags}`将这些收集的样式注入页面的`head`部分。有道理？

此外，如果需要应用全局样式，则导入另一个名为`injectGlobal`的函数，并在定义定制文档组件之前提供您的样式。

```
import { ServerStyleSheet, injectGlobal } from "styled-components";

injectGlobal`
  html {
    font-size: 10px;
  }
  body {
    font-family: "Merriweather", serif;
    font-size: 1.6em;
    line-height: 1.6;
  }
`;

export default class MyCustomDocument extends Document {
//.... 
```

就是这样！我发现 Next.js +风格的组件组合非常高效和灵活。你可以在 twitter 上让我知道你对这个组合的想法。

*附:个人博客原帖:[https://bilalbudhani . com/using-next-js-with-styled-components/](https://bilalbudhani.com/using-next-js-with-styled-components/)T3】*