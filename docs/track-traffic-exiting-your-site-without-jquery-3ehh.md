# 不使用 jQuery，跟踪离开站点的流量

> 原文：<https://dev.to/marcroberts/track-traffic-exiting-your-site-without-jquery-3ehh>

几年前(嗯，五年多前！)[我发表了一篇文章](https://www.theparticlelab.com/how-to-track-traffic-exiting-your-site/)，向你展示了如何使用 Google Analytics 通过一点点 jQuery 来跟踪你的网站的流量。虽然 jQuery 很棒，但对于较小的网站来说，它通常是多余的，尤其是如果只包含这一个用途的话。最近，在其中一个小网站上，我想到了一种不同的方法，不用 jQuery。

那么，没有 jQuery，我们如何做到这一点呢？jQuery 的许多特性在大多数现代浏览器中都以某种形式存在。例如，动画可以通过 CSS3 的过渡和/或动画特性轻松实现。最重要的是，在浏览器中广泛支持 [querySelectorAll](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) 。

> 你知道吗，jQuery 的很大一部分是一个名为 [Sizzle](http://sizzlejs.com/) 的库，它只是用来用 CSS 选择器选择元素。这正是 querySelectorAll 所做的。

有了这些知识，让我们在没有 jQuery 的情况下重新开始。首先，我们将在标记中放置一些东西来指示哪些链接应该被跟踪。上一次，我使用了一个定制的 jQuery 过滤器来查找所有外部链接，但是通过在标记中添加一些东西，我们可以更清楚地知道跟踪的是什么，也可以跟踪非链接元素(例如按钮和表单)。为此，我将使用一个 [HTML5 定制数据属性](http://html5doctor.com/html5-custom-data-attributes/)。

```
<a href="http://google.com" data-ga-track>Let me google that for you</a> 
```

我们只需要那个小小的数据轨道。现在这里是 javascript，我一会儿就看完。

```
;(function(){
  if (!document.querySelectorAll) return;
  if (!Array.prototype.forEach) return;

  function track() {
    var href = this.getAttribute('data-ga-track') ||
               this.getAttribute('href');

    ga('send','pageview', '/out/'+href);
  };

  var trackables = document.querySelectorAll('[data-ga-track]');

  Array.prototype.forEach.call(trackables, function(el) {
    el.addEventListener('mousedown', track);
  });

})(); 
```

我使用了一个[立即调用的函数表达式(life)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)来保持全局作用域的干净，并且在函数内部，如果浏览器不支持几个关键特性，就快速退出。显然，我们需要 querySelectorAll 和超过 97%的浏览器支持这一点，这很好，但 IE8 的支持有点问题。我还检查了超过 95%的浏览器支持的 ES5 数组函数。稍后我将使用它作为一种简单的方法来遍历匹配的元素，但更重要的是，IE8 不支持这一点，因此即使它(部分)支持 querySelectorAll，也不会运行其余的代码。

接下来是实际记录到 Google Analytics 的函数，它将查找 data-ga-track 属性的值，或者如果没有值，则查找 href 属性。然后，这个值作为页面视图发送到带有/out/前缀的 Google Analytics。上次我们向 Google Analytics 发送事件，而不是页面视图，但这只是我目前的偏好，你可以很容易地改变这一点，改为发送事件。

然后，我使用 querySelectorAll 找到所有具有属性 data-ga-track 的元素，并将它们保存在一个局部变量中(IIFE 将这个变量保存在函数的局部范围内，不在全局范围内)。最后，我们使用 forEach 函数遍历每个元素，并为 mousedown 事件添加一个事件处理程序来调用前面定义的 track 函数。我在这里使用 mousedown 来确保跟踪发生在浏览器开始离开之前。

> mousedown 事件也在元素上发生右键单击时触发，其中一些单击可能是有人试图“在新标签中打开”,但另一些不会。这将导致一些误报。

仅此而已。真的没有太多。你认为它能变得更短、更简单、更好吗？或者你还有其他简洁的独立 javascript 代码片段吗？让我们知道！