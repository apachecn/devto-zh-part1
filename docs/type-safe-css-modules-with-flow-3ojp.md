# 带流的类型安全 CSS 模块

> 原文：<https://dev.to/kenneth_skovhus/type-safe-css-modules-with-flow-3ojp>

<figure>[![CSS Modules + Flow = type safety and editor autocompletion](img/600a38c5cc970136d40ee3f82a693011.png "CSS Modules + Flow = type safety and editor autocompletion")](///static/82781fd79c387e48bc41c57f9f86025a/75d76/css-modules-flow.png) 

<figcaption>CSS 模块+ Flow =类型安全和编辑器自动完成</figcaption>

</figure>

我一直梦想着使用 [CSS 模块](https://github.com/css-modules/css-modules)时的类型安全和编辑器自动完成。这个有几个打字工具(见[这个](https://medium.com/@sapegin/css-modules-with-typescript-and-webpack-6b221ebe5f10)和[这个](https://github.com/Quramy/typed-css-modules)，但是我没有找到[流](https://flow.org/)的扎实工具。

*TL；我做了一些[新工具](https://github.com/skovhus/css-modules-flow-types)，当在我工作的代码库上尝试它们时，Flow 发现**很多死代码(和潜在的 bug)***😬

## 出现了问题

在移除. css 文件中的类后，很容易拼错类名或忘记更新使用者。例如，类`foo`可能没有在`Button.css` :
中定义

```
/* @flow */
import styles from './Button.css';
const Button = () => <button className={styles.foo} />; 
```

Enter fullscreen mode Exit fullscreen mode

## 方案

为了教授关于 CSS 模块文件的流程，我们可以创建一个包含由`Button.css`公开的类名的定义文件`Button.css.flow`。通过这样做，我们可以:

*   显示不存在的类的用法的静态类型检查
*   CSS 类的编辑器自动完成(用于支持流的编辑器)

来产生这些。流文件我在想两个用例。一个使用简单的 CLI，另一个使用 webpack。

### 解决方案:CLI

css-modules-flow-types-cli 是一个可以快速生成。流文件。

让我们安装它:

```
npm install --save-dev css-modules-flow-types-cli

# or

yarn install -D css-modules-flow-types-cli 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的源目录上运行 CLI:

```
css-modules-flow-types src 
```

Enter fullscreen mode Exit fullscreen mode

我建议在您的 CI 系统(如 Travis 或 Circle)上使用 CLI 来确保所有。在运行流之前，流文件是最新的。这将在部署之前捕获潜在的样式错误。

另一个用例是开发和更改 CSS 模块文件时的快速反馈循环。CLI 为此提供了一个观察模式，但是我自己希望避免在开发时需要运行另一个工具。因为很多人已经运行了 webpack，所以我做了一个小的加载器来使用 style-loader 的令牌。

### 解决方案:webpack loader

[CSS-modules-flow-types-loader](https://www.npmjs.com/package/css-modules-flow-types-loader)是 webpack loader keeping。通过使用来自`style-loader`的令牌更新的流文件。我建议在作为 webpack-dev-server 设置的一部分进行开发时使用它。这将会稍微降低速度，因为加载程序可能需要写很多文件。

开始使用:

```
npm install --save-dev css-modules-flow-types-loader 
```

Enter fullscreen mode Exit fullscreen mode

然后更新您的 webpack 配置:

```
{
  test: /\.css$/, // or the file format you are using
  use: [
    'style-loader',
    'css-modules-flow-types-loader', // right after style-loader
    // Other loaders like css-loader after this:
    ...
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

然后坐下来享受 CSS 模块被流进行类型检查。🍺

请让我知道你的想法…并在 GitHub 上给一点⭐️。

*这篇博文是从[https://skovhus.github.io/type-safe-css-modules-with-flow/](https://skovhus.github.io/type-safe-css-modules-with-flow/)交叉发布的，最初发布在 [hackernoon](https://hackernoon.com/type-safe-css-modules-with-flow-dd95e761bbe5) 上。*