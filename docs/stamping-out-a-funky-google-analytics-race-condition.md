# 消灭一个时髦的谷歌分析竞赛条件

> 原文：<https://dev.to/ben/stamping-out-a-funky-google-analytics-race-condition>

我们在[开发到](https://dev.to)
的一些页面上看到了这个错误

```
 Uncaught TypeError: ga is not a function 
```

`ga`是谷歌分析功能。通常情况下，如果你试图从一个尚未加载的库中调用代码，你会看到这种错误，但这只发生在*的一些*页面上，脚本在所有页面上以相同的方式加载，谷歌分析的脚本标签以相同的方式包含在所有页面中，如下:

```
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

ga('create', 'UA-XXXXX-Y', 'auto');
ga('send', 'pageview'); 
```

更重要的是，我意识到错误不是发生在“一些”页面上，而不是其他页面上，它只发生在一个页面上，即问题[的评论页面。您使用哪个编辑器/IDE，为什么？](https://dev.to/ben/which-editoride-do-you-use-and-why/comments)。我认为该页面上一定加载了一些自定义的 JavaScript，以某种方式干扰了脚本，但是在深入研究之后，我找不到任何证据。

这个页面还有什么独特之处？

这一页是迄今为止网站上最大的一页。由于有 202 个注释和计数，而且没有分页，所以加载了大量的 Dom 节点。因为这个站点[消除了所有的渲染阻塞延迟](https://dev.to/ben/faster-rendering-on-the-web)，我怀疑可能会有一个竞争条件，其中一个异步脚本可能会以某种顺序执行，从而搞乱事情。我真的不知道发生了什么，但页面的大小确实意味着它是成块呈现的，并且异步脚本能够在谷歌分析世界中“应该”之前启动。

我真的不知道真正的原因是什么，但因为它真的很糟糕，放弃对访问者数据的报告，我抢着让它工作。

我决定添加一个不断重试的`setInterval()`调用，直到`ga`被定义。

```
var waitingOnGA = setInterval(function(){
    (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
    (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
    m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
    })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');
    if(typeof ga === "function"){
        ga('create', 'UA-XXXXX-Y', 'auto');
        ga('send', 'pageview');
        clearInterval(waitingOnGA);
    }
},40) 
```

每隔 40 毫秒(任意)，我们询问`typeof ga === "function"`是否触发任何`ga`事件。如果`ga`存在并且是一个函数，我们清除这个区间。黑客成功了！这是我能做的最快恢复报道的事情。我将在未来进一步调查，看看这是否是问题所在。如果任何人对这个问题有任何见解，或者对如何在此期间做一些不那么 hacky 的事情有任何建议，我很乐意听到这些，请回复！