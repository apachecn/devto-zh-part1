# 在 React *中制作 SVG 动画，无需*第三方库！

> 原文：<https://dev.to/azure/react-svg-animations-5573>

我最近一直在做一个项目，我们使用 React 作为它的 UI 组件。在开始计划项目的下一阶段时，我们查看了一个关于制图的需求。我已经有一段时间没有用 JavaScript 制作图表了，更不用说用 React 制作图表了，所以我做了现在每个人都做的事情，在 Twitter 上大声喊出来获取输入。

玩笑归玩笑，有人建议，如果我使用 React，只做原始 SVG，如果需要的话，添加一点 [d3](https://d3js.org/) 来制作动画。

嗯，这是我从未想过的方法，但仔细想想，它很有意义。如果你看看图表库，他们在做什么？为您提供帮助方法来构建 SVG 元素并将它们添加到 DOM 中。React 是做什么的？创建一个虚拟 DOM，然后在真实的 DOM 中呈现给浏览器。所以使用一个外部库，你会发现你正在创建位于虚拟 DOM 之外的元素，结果会给 React 带来问题。

这都是几周前的事了，虽然这个想法听起来不错，但我并不需要进一步研究它，至少直到本周早些时候，当 charting + React 再次出现在对话中。所以我决定尝试一下，看看效果如何。

## 基本反应+ SVG

老实说，在 React 中绘制 SVG 与绘制任何其他类型的 DOM 元素并没有什么不同，就像这样简单:

```
const Svg = () => (
  
    <circle cx="50" cy="50" r="40" stroke="black" stroke-width="3" fill="red" />
  
);

ReactDOM.render(, document.getElementById('main')); 
```

Enter fullscreen mode Exit fullscreen mode

哒哒！

## 反应+ SVG +动画

好吧，所以这不是一个特别难的问题？那么，如果我们想添加动画呢？我从 [MSDN(例 2)](https://docs.microsoft.com/en-us/previous-versions/windows/internet-explorer/ie-developer/samples/gg193979(v=vs.85)?WT.mc_id=devto-blog-aapowell#example-2---basic-javascript-animation) 拿了一个例子作为我的演示。

我创建了一个演示[，可以在这里找到](https://www.aaron-powell.com/demos/react-svg/react-svg-example/)。与原始示例代码相比，它要干净得多，因为我们不再需要自己深入 DOM，通过使用`setState`设置`transform`属性非常容易。

现在我们使用`requestAnimationFrame`来制作动画(它又调用`setState`)，我们可以使用`componentDidMount`来开始动画，使用`componentWillUnmount`来停止动画。

## 添加 HOC

所以我们有一个缺点，我们将我们的状态与我们的应用程序代码结合在一起，那么如果我们想使用一个[高阶组件](https://facebook.github.io/react/docs/higher-order-components.html)来包装我们应用于 SVG 元素的特定转换，会怎么样呢？

让我们像这样创建一个特设:

```
const rotate = (Component, { angularLimit, thetaDelta }) => {
    class Rotation extends React.Component {
        constructor(props) {
            super(props);

            this.state = {
                currentTheta: 0
            };
        }

        componentDidMount() {
            const animate = () => {
                const nextTheta = this.state.currentTheta > angularLimit ? 0 : this.state.currentTheta + thetaDelta;

                this.setState({ currentTheta: nextTheta });
                this.rafId = requestAnimationFrame(animate);
            };

            this.rafId = requestAnimationFrame(animate);
        }

        componentWillUnmount() {
            cancelAnimationFrame(this.rafId);
        }
        render() {
            return (
                <g transform={`rotate(${this.state.currentTheta})`}>
                    <Component {...this.props} />
                </g>
            );
        }
    }

    Rotation.displayName = `RotatingComponent(${getDisplayName(Component)})`;

    return Rotation;
}; 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们把玩`requestAnimationFrame`的逻辑移到了它上面，使得旋转许多不同的 SVG 元素变得非常容易。此外，我们没有将`transform`应用于`rect`元素本身，而是将其应用于包装`<g>`元素。

我已经创建了第二个例子来展示这是如何工作的。

## 结论

最终，我认为这将比原来要困难得多！如果你花一点时间直接理解 SVG 是如何工作的，而不是依赖抽象层，我们可以快速制作一个使用内嵌 SVG +动画的 React 应用程序。

现在回到图表的原始主题？嗯，这实际上就是使用数组方法来遍历数据集，创建适当的 SVG 元素并对它们应用属性，所以我认为这不仅仅是举一个简单的例子并加以扩展。