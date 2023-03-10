# 你最近调试过的最难的问题是什么，你从中学到了什么？

> 原文：<https://dev.to/_bigblind/whats-the-hardest-problem-youve-had-to-debug-recently-and-what-did-you-learn-from-it>

我的是一个 React 应用程序的问题。我使用 XHR 加载了一个 SVG 图标表，并动态地将其添加到`<body>`标签的内容中。这是在第一次呈现图标时缓慢完成的。

我试图将一个动作网格(每个动作都有一个图标)放到一个 Accordion 组件中。accordion 可以处理任何其他内容，但是当我添加图标网格时，它没有响应。我首先开始寻找原因，直接在 accordion 中插入一个 SVG，没有使用`<use>` SVG 标签，这很有效。然后我加载了我的 sprite 工作表，并插入了一个`<use ... />`标签，它停止了工作。我急得要命，直到在吃午饭的时候，原因突然出现在我的脑海里。下面是我如何获取和插入我的精灵表:

```
fetch(location.origin + "/api/icons", {/* ... options ...*/}).then((resp) => {
    return resp.text();
}).then((svg) => {
    document.body.innerHTML = svg + document.body.innerHTML;
}); 
```

Enter fullscreen mode Exit fullscreen mode

问题是这样做会删除页面中的所有事件侦听器，因为您基本上是从一个字符串中重新创建整个 DOM。

下面是我的代码现在的样子:

```
fetch(location.origin + "/api/icons", {/* ... */}).then((resp) => {
    return resp.text()
}).then((txt) => {
    let div = document.createElement("div");
    div.innerHTML = txt;
    document.body.insertBefore(div.firstChild, document.body.firstChild)
}); 
```

Enter fullscreen mode Exit fullscreen mode

因此，我将从服务器获取的 SVG 作为文本插入到一个新创建的 div 元素中。然后，我将该 div 的内容插入到主体的第一个标记之前。重要的是，我实际上将 SVG 元素作为 DOM 节点插入，而不是作为文本。这样，DOM 保持不变，事件处理程序仍然工作。万岁！ðŸŽ‰

现在是时候从调试战场讲讲你们的故事了。