# 使用 Stencil.js 创建 Web 组件

> 原文：<https://dev.to/jorgerafaelsj/creating-web-components-with-stenciljs-5gpk>

如果你曾经在一个文件中继承了 3000、5000、8000 多行 CSS，然后在许多项目中看到同一文件的一些变体，请举手。具有不同悬停状态的随机提交按钮非常烦人。你想创建一个组件库，但是你必须在所有的应用程序中导入它，改变堆栈，改变 css 选择器。当你转移到一个不同的框架，重做一遍。解决方案:Web 组件。

“web 组件是一套不同的技术，允许您创建可重用的自定义用户界面组件，将它们的功能封装在代码的其余部分之外，并在您的 Web 应用程序中使用它们。” - [MDN](https://developer.mozilla.org/en-US/docs/Web/Web_Components)

一个朋友最近给我介绍了 [Stencil.js](https://stenciljs.com/) 。祝福他的灵魂。由 Ionic Framework 团队创建的 Stencil 是一个编译器，它可以转换您的 JSX 和 Sass，以创建捆绑到 NPM 包中的 Web 组件，该包可以导入到您的所有项目中。我们可以通过一个来源为您的所有应用提供一致的品牌和行为。框架不可知！

#### 创建一个组件

使用 JavaScript 类语法，您可以命名为“MyComponent”。用@Component decorator 指定 HTML 标记名和您的 Sass 文件。使用@Prop decorator 传递属性，并使用 JSX 语法来使用它们。

/组件
/组件名
/组件名. tsx
/组件名. scss

```
import { Component, Prop } from '@stencil/core';

@Component({
  tag: 'my-first-component',
  styleUrl: 'my-first-component.scss'
})
export class MyComponent {

  // Indicate that name should be a public property on the component
  @Prop() name: string;

  render() {
    return (
      <p>
        My name is {this.name}
      </p>
    );
  }
} 
```

#### 分布

他们的 [starter 模板](https://github.com/ionic-team/stencil-component-starter)是为超级容易地将你的组件发布为 NPM 包而设置的。然后，您可以安装 npm 或使用 unpkg CDN。将带有 src 的 index.html 中的脚本标签添加到 dist 文件中。瞧啊。您的组件现在已经注册/定义好了，可以使用了。[分发文件](https://stenciljs.com/docs/distribution)。

#### 用法

```
<my-first-component name="Max"></my-first-component> 
```

#### 天气好转

*   当组件出现在 DOM 中时，它延迟加载组件。如果我理解正确的话，HTML 标签已经注册了，但是一旦浏览器将要绘制每个组件，其余的定制内容就会加载。
*   [暗影 DOM](https://stenciljs.com/docs/shadow-dom) 。您的组件是有作用域的。风格不会冲突！
*   组件有一个类似 React 的生命周期。[componentWillLoad，componentDidLoad，componentWillUpdate，componentDidUpdate，componentDidUnload]
*   有一个@State 装饰器来处理组件状态。
*   更多...

我仍在学习更多关于这个工具的知识，并寻找其他可以考虑的技术。让我知道你的想法，如果你有任何建议。

查看[文档](https://stenciljs.com/docs/intro)进行测试。

### 启动视频

[https://www.youtube.com/embed/UfD-k7aHkQE](https://www.youtube.com/embed/UfD-k7aHkQE)