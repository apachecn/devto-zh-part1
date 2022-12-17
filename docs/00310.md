# 一个简单的客户端模板语言

> 原文：<https://dev.to/paul_kinlan/a-simple-clientside-templating-langauge-3l3f>

在最近的一个项目中，我想要一种简单的方法将一些 JSON 数据绑定到一个 DOM 元素，而不需要导入任何库，我认为我提出了一个非常好的解决方案(在我看来),满足了我对这个项目的所有需求。

该解决方案将模板指令编码在名为`data-bind-*`的 DOM 数据属性中，这些属性可在 dataset 属性中的 DOM 元素上访问，并且恰好自动对属性进行大小写转换(即，要设置`innerText`，您需要一个属性`data-bind_inner-text`——注意连字符)。

下面是项目中的一个样本模板:

```
<template id="itemTemplate">
  <div class="item new" data-bind_id="guid" id="">
    <h3><span data-bind_inner-text="title"></span></h3>
    <p class="description" data-bind_inner-text="content:encoded|description"></p>
    <div>
      <a data-bind_href="link" data-bind_inner-text="pubDate" data-bind_title="title" href="" title=""></a>
      
        <path fill="none" d="M0 0h24v24H0z"></path><path d="M18 16c-.8 0-1.4.4-2 .8l-7-4v-1.5l7-4c.5.4 1.2.7 2 .7 1.7 0 3-1.3 3-3s-1.3-3-3-3-3 1.3-3 3v.7l-7 4C7.5 9.4 6.8 9 6 9c-1.7 0-3 1.3-3 3s1.3 3 3 3c.8 0 1.5-.3 2-.8l7.2 4.2v.6c0 1.6 1.2 3 2.8 3 1.6 0 3-1.4 3-3s-1.4-3-3-3z"></path>
      
    </div>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们使用了`<template>`元素来确保我们可以将 HTML 保存在 DOM 中并保持其惰性(这确实改善了创作体验)。注意，它不一定是模板元素，它可以接受 DOM 中的任何内容。

为了将上面的 DOM 映射到一个实际的元素中，并应用所有的活动数据，我使用了下面的基本算法:

1.  克隆要绑定数据的元素。
2.  遍历元素，对于每个元素:
    1.  检查它是否具有形式为`data-bind_`的属性
    2.  获取要在由“|”分隔的`data`上查找的键
    3.  将输入`data`中第一个找到的键值直接映射到由`data-bind_`定义的节点属性
3.  返回新节点。

这个代码非常简单，如果有点简洁的话。

```
const applyTemplate = (templateElement, data) => {
  const element = templateElement.content.cloneNode(true);    
  const treeWalker = document.createTreeWalker(element, NodeFilter.SHOW_ELEMENT, () => NodeFilter.FILTER_ACCEPT);

  while(treeWalker.nextNode()) {
    const node = treeWalker.currentNode;
    for(let bindAttr in node.dataset) {
      let isBindableAttr = (bindAttr.indexOf('bind_') == 0) ? true : false;
      if(isBindableAttr) {
        let dataKeyString = node.dataset[bindAttr];
        let dataKeys = dataKeyString.split("|");
        let bindKey = bindAttr.substr(5);
        for(let dataKey of dataKeys) {
          if(dataKey in data && data[dataKey] !== "") {
            node[bindKey] = data[dataKey];
            break;
          }
        }
      }
    }
  }

  return element;
} 
```

Enter fullscreen mode Exit fullscreen mode

我不期望任何人使用它，但是我想展示如何为简单的任务构建数据绑定工具，而不必求助于完整的库或框架。