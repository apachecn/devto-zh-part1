# 带 HTML 5.2 <dialog>标签和 Chrome 的深色图案，用于娱乐和盈利</dialog>

> 原文：<https://dev.to/tanepiper/dark-patterns-with-the-html-52-dialog-tag-and-chrome-for-fun-and-profit-2lg8>

最近，我对新的`<dialog>` HTML 标签很感兴趣，我创建了一个 [CodePen](https://codepen.io/tanepiper/pen/MQwpeW) 和[博客帖子](https://medium.com/@tanepiper/dark-patterns-with-the-html-5-2-dialog-tag-and-chrome-for-fun-and-profit-249ce092d200)，里面有实验的细节。

使用该对话框存在一些明显的安全问题，希望这些问题能够得到解决。

我也为这个黑客感到非常自豪，我很惊讶我能做多少事情来让用户点击我想要的按钮。

编辑:作为跟进，我已经开始[编写一个 web 组件](https://codepen.io/tanepiper/pen/NyqBwX)来涵盖一些安全问题。在这里，它维护一个全局状态，如果已经有一个模式打开，它就发出一个事件。我还想尝试使用 observables 来剥夺页面上任何对话框的显示权利，而不用库来控制它。然而，这仍然是一个概念验证。