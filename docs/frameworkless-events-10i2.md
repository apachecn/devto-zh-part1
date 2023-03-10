# 无框架事件

> 原文：<https://dev.to/warrend/frameworkless-events-10i2>

在自动驾驶汽车统治道路的五十年里，当有人需要手动驾驶时会发生什么？自动驾驶仪出现故障，我的曾孙韦斯顿必须回家观看骑士队在 NBA 总决赛第七场对阵勇士队的比赛，因为现代医学已经找到了一种方法，可以让勒布朗和库里在 80 多岁时保持最佳表现。

但韦斯顿一生中只手动驾驶过一两次汽车，第一次是因为一个朋友的挑战，导致他父母的车库部分受损。

尽管 React、Angular 和 Vue 等现代 JS 框架有着令人难以置信的能力，但在没有框架支持的情况下编写普通的 JS 时，我们可能都有点像 Weston。

我正在 React/Socket.io/Express 中开发一个[聊天应用，用户可以点击一条消息，进行回复，回复会直接出现在所选消息的下方。为了手动驾驶这辆车，我想在普通的旧 JS 中重现](https://github.com/warrend/inline-chat)[点击事件](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)。

我希望能够点击并选择一个列表项，一次只能选择一个项目。如果我单击同一个项目，它将在活动和非活动之间切换类。以下是我们将要构建的内容:

[https://codepen.io/danielwarren/embed/qVKzKr?height=600&default-tab=result&embed-version=2](https://codepen.io/danielwarren/embed/qVKzKr?height=600&default-tab=result&embed-version=2)

对于这个例子，我将使用一个前五名 NBA 3-pt 领袖的无序列表(您可以使用您喜欢的任何列表，无论是书籍还是电影列表)。

```
<div class="container"> 
  <ul id="list">
    <p class="title">Choose a 3-Pt Leader</p>
    <li class="inactive" data-id="0">Ray Allen | <span>2973</span></li>
    <li class="inactive" data-id="1">Reggie Miller | <span>2560</span></li>
    <li class="inactive" data-id="2">Jason Terry | <span>2242</span></li>
    <li class="inactive" data-id="3">Paul Pierce | <span>2143</span></li>
    <li class="inactive" data-id="4">Kyle Korver | <span>2097</span></li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我有一个包含五个项目的无序列表，其类别为 inactive。此外，每一项都有一个数据标识，我们稍后会用到。在我们的 CSS 中，我们有两个类，活动的和不活动的。CSS 的其余部分纯粹是装饰性的:

```
.active {
  background: #cc0000;
  color: #fff;
  transition: .15s;
}

.inactive {
  background: #efefef;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于 JS，我们首先要抓取无序列表:

```
const listOfThings = document.getElementById('list') 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们想在 ul 上添加一个事件监听器:

```
const listOfThings = document.getElementById('list')
listOfThings.addEventListener("click", function(event) {}) 
```

Enter fullscreen mode Exit fullscreen mode

在事件监听器内部，我们希望遍历所有列表项:

```
const listOfThings = document.getElementById('list')
listOfThings.addEventListener("click", function(event) {
  let list = document.querySelectorAll('li')
  for (let i = 0; i < list.length; i++) {
  // loop through all the list items…
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

首先，在循环中，对于所选择的项目，我们要在 active 和 inactive 之间切换类名。我们通过比较目标上的 data-id 和当前迭代上的 data-id 来做到这一点。

```
const listOfThings = document.getElementById('list')

listOfThings.addEventListener("click", function(event) {
  let list = document.querySelectorAll('li')
  for (let i = 0; i < list.length; i++) {
    if (event.target.dataset.id === list[i].dataset.id) {
      if (event.target.className === 'inactive') {
        event.target.className = 'active'
      } else {
        event.target.className = 'inactive'
      }
    } 
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们让它保持原样，我们将能够一次选择多个项目。为了防止这种情况，我们添加了一个 else 条件(如果它不是点击的目标),我们将其设置为 inactive。这确保了任何未选择的项目没有红色背景。

```
const listOfThings = document.getElementById('list')

listOfThings.addEventListener("click", function(event) {
  let list = document.querySelectorAll('li')
  for (let i = 0; i < list.length; i++) {
    if (event.target.dataset.id === list[i].dataset.id) {
      if (event.target.className === 'inactive') {
        event.target.className = 'active'
      } else {
        event.target.className = 'inactive'
      }
    } else {
      list[i].className = 'inactive'
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

逻辑变得相当混乱，有很多方法来写这个，但上面的工作完成了。最初，我有相反的逻辑，将两个三元运算符组合在一起，但这感觉太多了:

```
event.target.dataset.id !== list[i].dataset.id ? 
list[i].className = 'inactive' : 
(event.target.className === 'inactive' ? event.target.className = 'active' : event.target.className = 'inactive') 
```

Enter fullscreen mode Exit fullscreen mode

总之，一个很好的练习是将高级框架代码重写为简单的普通 JS。这不仅是很好的实践，也让我们对日常使用的框架有了一种欣赏。

[在这里查看最终产品](https://codepen.io/danielwarren/pen/qVKzKr)
[http://danielwarren.io/2017/11/26/frameworkless-events](http://danielwarren.io/2017/11/26/frameworkless-events)