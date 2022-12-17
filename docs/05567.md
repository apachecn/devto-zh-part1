# 用 React 和 SVG 构建动画图表

> 原文：<https://dev.to/azure/react-svg-chart-animation-5dfh>

在本系列的上一篇文章中，我谈到了 SVG 对象的动画制作以及如何将它与 React 结合起来。当我谈到它的催化剂时，我们正在研究如何制作图表。

当然，在我最初的实验之后，我想实际看看如何做一个图表。

## 创建基本图表

为此，我从 CSS Tricks 的 [SVG 图表开始，我将使用](https://css-tricks.com/how-to-make-charts-with-svg/)[折线图](https://css-tricks.com/how-to-make-charts-with-svg/#article-header-id-5)示例(但使用随机生成的数据)。

现在我们知道了基本的 React 组件是什么样子的:

```
const Line = ({ data }) => (
    <polyline
        fill="none"
        stroke="#0074d9"
        strokeWidth="2"
        points={data}
        />
); 
```

Enter fullscreen mode Exit fullscreen mode

但这不是我们来这里要看的，向 DOM 渲染元素是非常基本的，让我们开始考虑动画。

## 制作折线图动画

我想做的动画是让线条从 x 轴增长到 y 轴的终点。

此外，我将尝试用一个对象来表示一些更真实的东西，而不只是用一个数组来存放我们的输入数据。我的数据会是这样的:

```
const data = [{ x: 0, y: 120 }, { x: 20, y: 60 }]; 
```

Enter fullscreen mode Exit fullscreen mode

像我的上一篇文章一样，我将使用一个高阶组件来包装处理动画的逻辑。让我们从`constructor`和`render`开始:

```
const animateLine = (WrappedComponent) => {
    class Wrapper extends React.Component {
        constructor(props) {
            super(props);

            const { xSelector, ySelector, data } = props;

            let mappedData = data.map((d) => [xSelector(d), ySelector(d)]).reduce((arr, curr) => arr.concat(curr), []);
            let max = data.map((d) => ySelector(d)).sort((a, b) => a - b).reverse()[0];
            let liveData = mappedData.map((x, i) => i % 2 ? max : x);

            this.mappedData = mappedData;
            this.max = max;
            this.state = {
                data: liveData,
                count: 0
            };
        }

        render() {
            return <WrappedComponent data={this.state.data} />;
        }
    };

    Wrapper.displayName = `AnimationWrapper(${WrappedComponent.displayName | WrappedComponent.name | 'Component'})`;

    return Wrapper;
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们期望组件上有 3 个`props`:

*   一组数据
*   从数据项中获取`x`值的函数
*   从数据项中获取`y`值的函数

然后，我们创建一个新的数组来展平数据，所以它看起来像:

```
[0, 120, 20, 60] 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们需要为我们的动画做准备，为了达到这个目的，我们需要把我们第一次画的线拉平，然后我们再走回去。为了做到这一点，我们需要找到最大的`y`值，我将这个值放入一个名为`max`的变量中。

最后，我需要创建展平的数据集，这是通过获取点的数组并将所有的`y`点转换为`max`值来完成的(因为它是我们开始的图形的底部，这是 SVG 的近似高度)。现在我们呈现给用户界面的数据是这样的:

```
[0, 0, 20, 0] 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们得到了一个隐藏的线图，它实际上并不代表我们的数据...不是很有用。

是时候开始制作动画了。像上一篇文章一样，我们使用`componentDidMount`来启动动画，如果需要的话，使用`componentWillUnmount`来停止动画。这里是`componentDidMount` :

```
componentWillMount() {
    const animator = () => {
        if (this.state.count >= this.max) {
            cancelAnimationFrame(this.rafId);
            return;
        }

        const newData = this.state.data.map((data, index) => {
            if (index % 2) {
                if (data > this.mappedData[index]) {
                    return data - 1;
                }
            }
            return data;
        });

        this.setState({ data: newData, count: this.state.count + 1 });
        this.rafId = requestAnimationFrame(animator);
    }

    this.rafId = requestAnimationFrame(animator);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分解它，或者更准确地说，分解`animator`函数，这才是动画真正为我们做的。

第一步，我们在组件上有`max`的原因是为了让我们知道什么时候停止尝试动画一个点。这就是这个逻辑的用途:

```
if (this.state.count >= this.max) {
    cancelAnimationFrame(this.rafId);
    return;
} 
```

Enter fullscreen mode Exit fullscreen mode

第二步，开始让我们的临时数据更接近真实数据:

```
const newData = this.state.data.map((data, index) => {
    if (index % 2) {
        if (data > this.mappedData[index]) {
            return data - 1;
        }
    }
    return data;
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们将绘制数据图，并:

*   如果当前索引是偶数，一个 x 轴值，只需返回它，我们不移动它
*   如果当前索引是奇数
    *   如果小于目标值，则加 1
    *   否则只返回当前值

第三步是将新数组置于状态(并导致重新渲染)以及增加循环计数，然后再次启动`requestAnimationFrame`。

仅此而已，我们有一个可爱的动画线车。

## 结论

我们再次看到，少量的代码和 React 组件可以使一个非常容易阅读的动画 SVG 没有任何外部依赖性。

我创建了另一个例子[，你可以在这里看到](https://www.aaron-powell.com/demos/react-svg/react-svg-line-chart/)的运行，数据是随机生成的，所以每次重新加载页面都会得到一个新的图表😊。