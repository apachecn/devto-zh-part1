# 带有 ID 的 DOM 元素是全局变量

> 原文：<https://dev.to/buntine/dom-elements-with-ids-are-global-variables>

所以，有件事很奇怪。

昨天我重构了一些代码，遇到了类似这样的事情:

```
const logo = document.getElementById("brand")

brand.style.top = 0 
```

Enter fullscreen mode Exit fullscreen mode

代码库中没有定义名为`brand`的变量。因此，任何理智的观察者都会预料到前面的代码片段会导致运行时错误——假设没有类似 linter 的东西可以首先发现它(事实上,`logo`正在被定义，但从未被使用)。

我开始经历一个我们都害怕的时刻:我在看一些不可能成功的代码。然而，它就在那里。工作。我跌坐在椅子上，不确定自己看到了什么。

事实上，我在整个应用程序中唯一能看到“brand”这个词的地方是在执行这个 Javascript 的 HTML 文档中(是的，一些网站仍然通过网络发送 HTML)。

所以我改了:

```
<div id="brand_foo"> ... </div> 
```

Enter fullscreen mode Exit fullscreen mode

然后，你瞧，代码立即开始导致运行时错误。等等。决不...浏览器肯定不会为每个带有 ID 属性的 DOM 元素预定义一个全局变量。对吗？

不对！确实如此。我在大多数主流浏览器和移动设备上查看了这段代码，没有一个人抱怨过。

不相信我？你现在可以自己试试。按 F12 键(或者右键单击-> Inspect)，找到一个具有 ID 的元素，然后在控制台 REPL 上计算该 ID。

像“brand-name”这样不符合 Javascript 语法的 id 怎么办？他们肯定被忽视了吗？没有:

```
window["brand-name"]  // --> <div id="brand-name"> 
```

Enter fullscreen mode Exit fullscreen mode

我不知道这有多出名。我当然不知道它，但是，不可否认的是，我已经很多年没有花太多时间在浏览器上了。我联系了我认识的所有前端开发人员，他们也不知道这件事。

事实证明，HTML 规范实际上[记录了这个功能](https://html.spec.whatwg.org/#named-access-on-the-window-object)。尽管看起来主要的浏览器并没有统一实现它。如果你用同一个 ID 定义了不止一个元素(你这个邪恶的人)，那么 Chrome 实际上会返回一个`Element`对象数组，而 Firefox 会返回它看到的第一个元素。这两种浏览器都没有基于像`img`和`a`这样的元素上的`name`属性定义全局变量。

## 我该利用这个吗？

不，请不要。想象一下，如果您在一个重要的代码库中处理许多 DOM 元素，而它们中没有一个是在程序员可见的任何地方定义的，这会多么令人困惑。或者想象一下，如果你全力以赴，然后有一天一个主要的浏览器决定这是疯狂和禁用它！

我唯一能看到这个有用的地方是在 REPL 调试的时候。但是，尽管如此，以一种从不做这种事情的方式来看，这还是很酷的。

*标题图片来源于[http://www.classichorrorcampaign.com](http://www.classichorrorcampaign.com)T3】*