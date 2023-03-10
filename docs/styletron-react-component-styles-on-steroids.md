# Styletron:在类固醇上反应组件样式

> 原文：<https://dev.to/kayis/styletron-react-component-styles-on-steroids>

*TL；DR 这里是一个[库](https://github.com/kay-is/react-styletron-example)和一个工作实例。:)*

如果你了解最新的 React 趋势，那么你可能听说过[风格组件](https://styled-components.com/)。顾名思义，这是一个非常有助于创建样式组件的库。

Styletron 现在试图将这种方法推向下一个阶段。它基本上是针对 CSSOM 而不是 DOM 的 React。它试图创建最少量的 CSS 来完成工作，与其他简单地为每种样式创建一个类的方法相比，它提供了性能优势。

## 如何

首先，您需要在应用程序的根目录下有一个`StyletronProvider`，它将处理您的样式组件。它以一个`Styletron`对象作为道具。这个对象可以使用一个`style`元素作为它的呈现目标，就像 React 使用一个 DOM 元素来呈现它的组件一样。

```
const styleSheet = document.createElement("style");
document.head.appendChild(styleSheet);
const styletron = new Styletron([styleSheet]);

const vdom = (
  <StyletronProvider styletron={styletron}>
    <Application />
  </StyletronProvider>
);

...

ReactDom.render(vdom, renderTarget); 
```

Enter fullscreen mode Exit fullscreen mode

稍后，您可以使用`styled()`函数来创建您选择的元素或组件的样式版本。这个想法是你的组件像以前一样工作。

这里有一个`div`:

```
const Square = styled("div", {
  background: "grey",
  width: "100px",
  height: "100px"
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是它也可以使用对组件类的引用:

```
const BigIcon = styled(Icon, {
  color: "red",
  font: "3em"
}); 
```

Enter fullscreen mode Exit fullscreen mode

也可以传递一个函数，该函数将获取道具作为参数。这允许基于道具内容返回样式对象。

```
const Indicator = styled("h1", props => ({
  background: props.active ? "green" : "grey",
  color: props.active ? "white" : "black",
  textAlign: "center"
})); 
```

Enter fullscreen mode Exit fullscreen mode

Styletron 将在后台处理繁重的工作，计算创建这种样式所需的最小类数量，您的组件将像以前一样工作。