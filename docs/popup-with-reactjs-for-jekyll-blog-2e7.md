# Jekyll 博客的反应弹出窗口

> 原文：<https://dev.to/satansdeer/popup-with-reactjs-for-jekyll-blog-2e7>

*原贴于[maksimivanov.com](http://maksimivanov.com/posts/react-modal-window)T3】*

很久以前(昨天)，我认为我的博客缺少一个好的弹出窗口。你知道，就是那个非要加入邮件列表的人。因此，如果你有 jekyll 博客，你想知道如何使用 **ReactJS** 创建一个弹出窗口，这篇文章是完全相关的。

首先，让我们务实一点，看看我们会得到什么样的结果。

[![example button](img/db5f63c2c3319b0c97d16a09029b59ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yRVxmNCa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jdhz00j3sgqask4xcoz.png) 
*这里这个按钮只是一个图像，去[maksimivanov.com](http://maksimivanov.com/posts/react-modal-window)看例子*

在本文中，我假设您正在使用 **github pages** 来托管您的 jekyll 博客。

## 让我们把手弄脏吧

为了能够编译`.jsx`代码，首先我们需要设置`webpack`。

从在根目录中创建`package.json`开始。

```
➜ npm init 
```

Enter fullscreen mode Exit fullscreen mode

在回答了所有必要的问题并清除了不必要的东西后，你应该得到这样的结果:

```
{  "name":  "satansdeer.github.com",  "version":  "1.0.0",  "description":  "Maksim Ivanov jekyll blog",  "author":  "Maksim Ivanov",  "license":  "ISC",  "devDependencies":  {  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们安装所需的依赖项:

```
➜ npm install webpack babel-core babel-loader babel-preset-es2015 babel-preset-react react react-dom --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

创建带有预置配置的`.babelrc`:

```
➜ echo '{ "presets": ["react", "es2015"] }' > .babelrc 
```

Enter fullscreen mode Exit fullscreen mode

配置 webpack，将其放入您的`webpack.config.js` :

```
const path = require('path');

module.exports = {
  entry: "./front/entry.js",
  output: {
    path: path.join(__dirname, "/assets/javascripts"),
    filename: "bundle.js"
  },
  module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /(node_modules)/,
      loader: "babel-loader",
      query: {
        plugins: ['transform-class-properties']
      }
    }
    ]
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 试运行

制作`front`文件夹:

```
➜ mkdir front 
```

Enter fullscreen mode Exit fullscreen mode

并在那里创建文件`entry.js`,其中包含一些用于测试的控制台输出。

```
➜ echo 'console.log("It works!")' > front/entry.js 
```

Enter fullscreen mode Exit fullscreen mode

运行 webpack，它应该在`/assets/javascripts`文件夹中构建你的`bundle.js`。

```
➜ webpack 
```

Enter fullscreen mode Exit fullscreen mode

现在在你的布局里加上这条线。我不知道也许在你的页脚，或者只是在你的`_layouts/default.html` :
的底部

```
<script type="text/javascript" src="/assets/javascripts/bundle.js" charset="utf-8"></script> 
```

Enter fullscreen mode Exit fullscreen mode

打开你的网站。您应该在 javascript 控制台中看到`It works!`。如果不是——仔细检查 webpack 配置，确保`bundle.js`已构建。

## 添加 React

创建`components`文件夹:

```
➜ mkdir front/components 
```

Enter fullscreen mode Exit fullscreen mode

并在那里添加`App.js`，内容如下:

```
import React, { Component } from 'react';
import Popup from './Popup';
import SubscriptionForm from './SubscriptionForm';

class App extends Component {
  constructor(props) {
    super(props);

    this.state = { isOpen: false };
  }

  openPopup = () => {
    this.setState({
      isOpen: true
    });
  }

  closePopup = () => {
    this.setState({
      isOpen: false
    });
  }

  render() {
    return (
      <div className="App">
        <button onClick={this.openPopup}>
          Click Me!
        </button>

        <Popup show={this.state.isOpen}
          onClose={this.closePopup}>
          <SubscriptionForm></SubscriptionForm>
        </Popup>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，每次我们调用`openPopup`或`closePopup`时，我们都会触发`setState`函数，该函数会使用更新后的`isOpen`值进行重新渲染。

然后我们将`isOpen`值传递给`Popup`组件。用下面的代码创建【T2:】T3

```
import React from 'react';

class Popup extends React.Component {
  render() {
    if(!this.props.show) {
      return null;
    }

    return (
      <div className="popup-backdrop">
        <div className="popup">
          <button className="popup-close" onClick={this.props.onClose}>✖</button>
          {this.props.children}
        </div>
      </div>
    );
  }
}

export default Popup; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们传递 false，它返回`null`并且不呈现任何内容，或者如果我们传递 true，它呈现我们的弹出布局。

我们还传递了`closePopup`作为`onClosed`属性的值。现在我们将其触发为关闭按钮的`onClick`回调。

`{this.props.children}`部分允许我们传递嵌套组件`SubscriptionForm`，它基本上包含 Mailchimp 提供的选择加入表单。

我只是将`inputs`改为使用封闭标签，并将一些属性改为它们的 React 版本。比如`class` / `className`，`for` / `htmlFor`。

下面是我的`SubscriptionForm` :

```
import React from 'react';
import PropTypes from 'prop-types';

class SubscriptionForm extends React.Component {
  render() {
    return (
      <div id="mc_embed_signup">
        <form action="https://maksimivanov.us12.list-manage.com/subscribe/post?u=fdcb5a4b4a6cbb9721227a48f&amp;id=fa1a88a0d0" method="post" id="mc-embedded-subscribe-form" name="mc-embedded-subscribe-form" className="validate" target="_blank" noValidate>
          <div id="mc_embed_signup_scroll">
            <h2>Subscribe to my mailing list</h2>
            <div className="mc-field-group">
              <label htmlFor="mce-NAME">Name:
                <input type="text" name="NAME" className="required" id="mce-NAME"/>
              </label>
            </div>
            <div className="mc-field-group">
              <label htmlFor="mce-EMAIL">Email:
                <input type="email" name="EMAIL" className="required email" id="mce-EMAIL"/>
              </label>
            </div>
            <div id="mce-responses" className="clear">
              <div className="response" id="mce-error-response" style={{display: 'none'}}></div>
              <div className="response" id="mce-success-response" style={{display: 'none'}}></div>
            </div>
            <div style={{position: 'absolute', left: '-5000px'}} aria-hidden="true"><input type="text" name="b_fdcb5a4b4a6cbb9721227a48f_fa1a88a0d0" tabIndex="-1" value=""/></div>
            <div className="clear"><input type="submit" value="Subscribe" name="subscribe" id="mc-embedded-subscribe" className="button"/></div>
          </div>
        </form>
      </div>
    );
  }
}

export default SubscriptionForm; 
```

Enter fullscreen mode Exit fullscreen mode

这是暂时的解决方案，稍后我计划改用 [mail-for-good](https://github.com/freeCodeCamp/mail-for-good) 使用 AWS 发送批量电子邮件，并重写订阅表单。

## 总结

这就是了。我们已经使用了`webpack`来构建和捆绑我们的 javascript。我们添加了一个简单的弹出组件和一个选择加入表单。

在下一篇文章中，我们将添加测试，并了解一些用于测试 React 应用程序的工具。