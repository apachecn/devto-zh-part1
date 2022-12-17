# 向组件注入动态元素

> 原文：<https://dev.to/ganderzz/injecting-dynamic-elements-to-components-56gd>

原贴:[dylanpaulus.com](https://www.dylanpaulus.com/react/2017/07/26/injecting-react-tag-types/)

# React:给组件注入动态元素

### 简介

假设我们正在使用 React JS 开发一个 UI 组件库。我们做了一个超级好看的按钮，甚至可能是世界上最好的按钮。但是突然，我们的计算机没有保存我们的组件就关闭了！像顽强的 D 一样，我们也向按钮致敬，看起来像:

```
class AwesomeButton extends Component {
    render() {
        const { children, ...other } = this.props;

        return (
           <button {...other}>{children}</button> 
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

一切都很好。我们到处创建按钮`<AwesomeButton onClick={doStuff}>Click ME</AwesomeButton>`。

第二天，老板过来，“这个按钮太神奇了，我们做一个按钮链接到谷歌吧！”很简单，我们创建了一个新的组件，它使用了一个锚标记。

```
class AwesomeButtonLink extends Component {
    render() {
        const { children, ...other } = this.props;

        return (
           <a {...other}>{children}</a> 
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

几周后，另一个程序员走过来，“嘿，我们正在转换使用 React 路由器。我们能得到一个可以使用链接组件的按钮吗？”低声喃喃自语，我们创造了另一个可怕的组件。

```
class AwesomeButtonReactRouterLink extends Component {
    render() {
        const { children, ...other } = this.props;

        return (
           <Link {...other}>{children}</Link> 
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有自己的问题。每次有新的请求时，我们必须创建非常相似的新组件，只是使用稍微不同的渲染元素。如果公司决定重塑品牌。我们现在不是蓝色的，而是红色的公司。这些 AwesomeButton 组件的视觉效果需要单独更新。想干嘛！有更好的方法吗？敬请关注。

### 动态注入元素

如果组件的消费者可以定义它的基本元素会怎么样？我们来看下面的例子:

```
class AwesomeButton extends React.Component {
    render() {
        const { children, tag = "button", ...other } = this.props;
        const Tag = tag;

        return (
           <Tag {...other}>{children}</Tag> 
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们有了这个新的属性叫做 tag。标签将是我们将元素/组件传入 AwesomeButton 的方式。如果 es6 析构看起来不一样，我来解释一下。我们将从`this.props`中拔出`tag`支柱。如果没有为 tag 定义值，我们将把它的默认值设置为`"button"`(一个按钮 HTML 元素)。下一行，`const Tag = tag;`是为了实现 React 的 JSX 认为的组件。所有组件都必须大写，html 元素需要小写。由于我们使用了 then 变量`tag`，JSX 将总是把传入的值视为一个组件。所以，我们必须大写我们的变量。我们现在渲染道具`Tag`作为我们的元素。无论`tag`等于什么，那都是我们的元素！

几个例子:

```
<AwesomeButton onClick={doSomething}>Click Me!<AwesomeButton> 
```

Enter fullscreen mode Exit fullscreen mode

将呈现默认按钮。`<button onClick={doSomething}>Click Me!</button>`

```
<AwesomeButton tag="a" href={`/myPage`}>Click Me!<AwesomeButton> 
```

Enter fullscreen mode Exit fullscreen mode

将使用锚标记`<a href={'/myPage'}>Click Me!</a>`
进行渲染

```
<AwesomeButton tag={Link} to={`/myPage`}>Click Me!<AwesomeButton> 
```

Enter fullscreen mode Exit fullscreen mode

将使用 React 路由器链接组件进行渲染`<Link to={'/myPage'}>Click Me!</Link>`

整洁！但是为什么会这样呢？

### JSX 变换

在 render 函数中，我们的 JSX 被转换成浏览器可以使用的普通 JavaScript 函数。考虑到这一点，我们的 AwesomeButton 的 JSX 被转换成一个`React.createElement()`函数，元素名称作为第一个参数。在我们的好朋友 Babel 的帮助下，让我们看看不同的组件编译成什么！

首先，让我们看一个简单的组件，它只呈现一个带有文本“test”的 div。

```
class AwesomeButton extends React.Component {
    render() {
        return (
           <div>test</div> 
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[编译示例——点击我！](https://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=false&presets=es2015%2Creact%2Cstage-2&targets=&browsers=&builtIns=false&debug=false&code_lz=MYGwhgzhAECCDuBTCB7AtogQgVwC65QDtpEAPXRQgExgCVExhcA6AYXQAcjLdoBvALAAoaKOgAnSlUTiAFAEp-wsSomJc2ccVnLVqgDxUAlgDcAfBQi59AemPnouvfIDcT6AF9hHoA)

如果我们查看 render 方法，我们可以看到我们的 JSX 实际上编译成了什么。

[![div Compiled Code](img/857306115384a2a6b443ddadc7c118fb.png "div Compiled Code")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VrDxDV50--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/data:image/png%3Bbase64%2CiVBORw0KGgoAAAANSUhEUgAAASgAAACbCAYAAADRLgZ2AAAAAXNSR0IArs4c6QAAAARnQU1BAACxjwv8YQUAAAAJcEhZcwAADsMAAA7DAcdvqGQAAAwGSURBVHhe7d1Pa9vIH8fxPJM%2Blz3Xl976CPbSSyA57q3QFvZSFpJQWNpL2UDhRyBh2a4p7Kl7WH4NDiz0GcTpff9eZjV/JM%2BMRtI4lu2vpPcLBqzojxXF%2Bvg7ijU%2BUB0WJw/V7GThpuz08eXSTSXcXanj2ZG6unPTlYU6mz1UZ9dusnR9qmaz02LuSvCcZnvh/Obn2Mzypwv14ptfVf23%2B12dPz5X5zduUruZqxeP526/lmr%2Bzbk6%2B8lb88uv6uzxhZp/cdPB8k0Sz5OQ3M/k9v3tZexjp8/q5eH/1JP33go3v6ivDn9Rn/Tju9/Uk/JxyfzsR3Vh/lZf1MWzaP1gvlKf3kTz3XO%2BdMfk9v2P6qs3n%2B2EY3727Dd166b1NvzpeBv6WFwddbyOKw2v21Z2ndnRVeK1hHV0BlQYEPrAx8Hg/hhB6zGgzPx4%2B7oNK6Cat%2B27f0Clt%2B/vV38BtTrRIyasinCotdyASsyPntOET237RYsDKgqx0LYDykm9uWIt3QHl/pjmD6TDwqumynnBz3ZRQW3JNgNqTBVUY0ClKqjA5gGVqqBi3QEVvcZapd6Uc20QbjAyAqrggkn/UcODbf8Aq3ciF1jrBFQUaMvLI1shVS%2BeRAgmlc99/zBrDqjo5DYn9vl6AeXC4sX3v7vpFLud9mWa9tNu39%2BHxffFtqrldhBQLmCaw6EroOLumn0%2BXSFVz2mWb9uHvIBKvTEm5S6XREBtKi%2Bg3IFO9qnNH1AHg21n13rZROBEzS%2BvzbtZOa8IIrNOEEhl%2BHitti/bDKiCqVB0KLlgMif3OgGluZDyWq1aqsLPtSqs6uva5lVN8brB77KLgNJcSLlgCbtf3QEVr/yxk4Hz%2BlCyn8Of5tZAZUZHvmVVgoBtanMgALGx755tryhbVQ9afZNk4C6PwIKE9Z2%2BaCf6mezCgwEFCZOh1T9mqmurvL%2By9fFXR6hq3cvBBQAsQgoAGIRUADEIqAAiEVAARCrMaDm87l7dD%2Bbrg8AVFAAxKKCAlBIfx5s36ighEjds8gH%2B/Ykca%2Bfbqv7AVc3MesWjr4wRG2fqN%2Bv6VRQa98Yu1smoPwboM19YHt4R2scLmdY8m4YblC7gblJ4uZnYXKOQ%2B21J8h0KqihBdS%2B7oQnoCYWULJHXBhEBaXHNdLDhJjhUNxQIsGwIQUz9lE11EjLECReq7aRGOzNbM8bl6l9H%2BxQJuc3bjwnN79r4DlfHFC2yxfdSW/CY9UFrN8r5kr1aplE0ETbqJ6j9vNVW/uetHhkTW%2B0y2rAOX%2BZ%2BASK1q8N7xJ3weLRNP15VQsH0guXaxumuE1bQIXDxvj7aH6/YvrC7cOT95%2BrZYPfte04uG3c%2Bsvc4ziYSl1o9aQNooKqwqcc3yiqhsx8b%2BwjGyJh4LRWUJkB1bwPq2AqQym5Dy1sIPnBkA6n1Tudfefzw2N5eRoEkrmT3n/x1baRYJbZoIIyJ0zzyW0CKjhRojGm4vVdGK1OzuLEf%2BOd7G79OCTaKgczzzuZ7T55J24cgKalfqemgHLh5D1/8JwuVPR65fHQv1%2Bwz13HoQym6vdY/zho0kdbGEwFFQ6%2B5g9lqx/HwePPd/oIqMZ92HwwuKCCSoxDZOZHL6S46qqJtpP1YtwooNoqCqsWBtE6%2BoRa9yRLzW9eR5/IcdhEIblpBWXWb6nKTLjY%2BeZ4uJAxj90%2Bdx4Hbxul9Y6DZivufkZt2I7hVFBNw%2BCaILDVS9x6D6imfeg7oNwLx690TLhU1ZXXEhVSuEwZUJkvxo0CKjrRE/wTss5VHroyiJt3kpVVR9N8rfHETFZHtvUWUGV1U2u5AZVxHKYeUOIqqMZwSFVQCYMKqEJ0bSBVQYXqXT6xFVRjQNkTqm19e2KG4bHeiZmqoCLbqKB8nQGVexw2DajM18QeDb%2BCKpj5QfcrxXbJghApRWFSXQjfZ0C5wKmqKFcdNV4/qs236wddRRNYHdegUkEXMfuqt514YZuTrOXk7gqoVAD57Pa9E9MsXz8Ja8t5zEnbtg%2BbBlRZATUFQ0ZAdR2H3IBqOw5G4nKCJCOooCyzjNe9S16gNpXSahk/UIL1i%2BcyIbXXgCqDwHvxJLpwfpBUwWFaUQVd6%2BWjF58LqVVLvDijZeKwagsozZ4UxclSNi8M/BOyURk6Xlt1G6PuT7GtT/6JXYmWS53M1bxofkM3sHaBOm7BPsTPX7Ty984JKK3tOGQGVNdxKN%2BQ2t%2B09mcQFRSA7am9EQoyiAoKwDa5z88JvBZFBQWgoENqg8%2B/bQkVFACxqKAAiEVAARCLgAIgFgEFQCzRF8m5UA9MGxUUALGooACIRQWVa/FUHX57oF7rD9v6jxssPzxSh6/eBvfWaYt3B8W6T%2B91W4G/7ibbAYaCCiqXCaVHaq4Tx3/cYGsB5bbpPwbGigoq1/Ktel6Gkv%2B4QVNAbcLf5ja2D0hDBdWbWzV/pasjr3kBYgKlmhdXUB/V6%2BLnzz%2BEg5DYdejGYbqooHrhwundRzfdUuGY7mE9dOrL223GoQVMCRVUH0yXLwyddQOq1m1MbBOYGiqoPiRCZ%2B2AKugL32XFZNb3KjJgiqig%2BtBTQJl5Zh3dvWu/CA9MARVUL%2BxF7vJzUSacoovklbaAKoNpUXTvUuvmKMctj8Y3B4aICqovJnjcf%2Bl0uFTVkJb4D59p9SqpDLe2D4G2c8O3Ch4IH8hFBTVCdhB8ecO3Auuighod9314dPEwAlRQI9L1fXXA0FBBARCLCgqAWAQUALEIKABiEVAAxOIiOQCxqKAAiEUFBUAsKqgd%2Bffnr9Ufzx649p36x/28cnuh/qzmP1B/cR8dQAW1c9ffpQOq8n/1FwEFGFRQu0ZAAdmooLLo0Pha/X1rw8N2w/S0m50KlaYgIqCAbFRQWcpgWoXSPz8U0ycX6l8zRUAB20AFlaUrgAgoYBuooLIQUMA%2BUEFlIaCAfaCCytIVQF/U3ycP1J8/fzFTq8809R9Q5aB0x5eMl4nxo4LKklEhmenyP3zFz01IhQG2ml%2B21UX38IOcq1aFXqn81pbZacM3wwDjQQU1OO5bWxhzHBNABTU016d08TAZVFCDwffdYXqooACIRQUFQCwCCoBYBBQAsQgoAGJxkRyAWFRQAMSiggIgFhVUrsVTdfjtgXqtb4DzH0syhH0E1kAFlcuc8I/UXN9h4j/2LN4dqMN3H93UNnxUr9tCJ2MfgSGhgsq1fKuelye8/9iz94DK2EdgSKigemCCqQiOensaDIkSLhfOM1y3rGqv3roRC2wwBfPKttVABPaLCqpHbRWUmVcFTlHgfHhUBIwXUlkVT0cFBYwMFVSPmgNKB0scPlHYmIA6UM8/VN9llUBAYVqooHrUGFAufFJdtCBsouXqQURAYVqooHq0XgXVIflfOAIK00IF1aPadSVPfA2qU/Ka1K2av2oKQWB8qKB65QKk6qa1/Rcvmh/B69oyUop7i4SVhgxKigAYlFBARCLCgqAWFRQAMSiggIgFgEFQCwCCoBYBBQAsURfJAcwbVRQAMQaSAW1UGez0%2BQ9bgDGazAV1PLySM2OrvJvtgUweAO6BrVUV0cP1eyEOgqYimFdg7q7UsezI3V156YBjNqAKijNVlHHl6mOnquwuFYFjMawKqiCuRaV7OYRUMDYDKyCcgHFxXJgEoZZQRFQwCQMs4LiP3nAJAysgsq5SM5/%2BYCxGFYF1fExg8WJDqimAAMwNAOqoNqqJ%2Bf6lIACRmQwFVTOxXFbQfExA2AsBlJBddwsbLp%2BhBMwNsO6BgVgUgZ0DQrA1BBQAMQioACIRUABEIuAAiAWAQVArM6AMh%2BQ5DNGAPYgu4Iyn9JmFAEAO5TfxdP3uTEOE4AdIqAAiEVAARArP6D4yicAO5YfUIYbtZJKCsAOUEEBEItrUADEIqAAiEVAARCLgAIgVnZAcasLgF3rDChuFgawL/ldPADYMQIKgFgEFACxCCgAYhFQAMQioACIRUABEIuAAiAWAQVALAIKgFgEFACxbEDpkQpmD9XxJWMVAJCDgAIgFl08AGIRUADEIqAAiBVcg2LETACS2IAy33nHqJkAZHFdPPeNwQQUAEGCCoqPGQCQ5MB8WwvhBEAcpf4DpyEGsJh%2BD0cAAAAASUVORK5CYII%3D)

这很酷。所以我们的 div 元素被定义为`React.createElement`的第一个参数。我想知道如果我们用我们的元素注入一个很棒的按钮会发生什么？

```
class AwesomeButton extends React.Component {
    render() {
        const { children, tag = "button", ...other } = this.props;
        const Tag = tag;

        return (
           <Tag {...other}>{children}</Tag> 
        );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[<牛逼按钮>点击我！</awesome button>T1】](https://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=false&presets=es2015%2Creact%2Cstage-2&targets=&browsers=&builtIns=false&debug=false&code_lz=MYGwhgzhAECCDuBTCB7AtogQgVwC65QDtpEAPXRQgExgCVExhcA6AYXQAcjLdoBvALAAoaKOgAnSlUTiAFAEp-wsSujAiEXnzUALAJYgqkwgBpouMAHNoAXmgAiAEZ4Che2eaeUuHTOgBfW3N9CGYOcRQOCABuZVVRdUJNaAAVKyCLS1ihOPjJXGxxYllc-NEAHjTrPk9mb19xfwA-PmB9Q2N_coB6KqboUpV5bJV_YX8gA)

哇，我们的标签变量就放在元素所在的地方！这与 JSX 对待零部件的方式完全一样。他们直接陷入`React.createElement`的第一个论点。现在，当老板在`AwesomeButton`中要求一个新特性时，我们只需要改变组件的这一个实现。组件的所有消费者使用它的方式可能大不相同，但是我们能够保持代码的干爽和高度可重用性！