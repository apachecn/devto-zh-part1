# 如何用 JavaScript 制作纸牌游戏，第 1 部分

> 原文：<https://dev.to/andrew565/how-to-make-a-card-game-in-javascript-part-1>

我玩这个纸牌游戏已经有一段时间了，现在叫做[地牢纸牌-四王之墓](https://matthewlowes.com/games/)，作者是马修·洛斯。该游戏模拟了深入到一个坟墓中寻找与四个国王一起埋在那里的宝藏。我一直喜欢这种耐心风格的游戏，因为它有简单易学的规则，使用标准的纸牌，所以它可以在任何地方玩，但却给原本“只是另一个纸牌游戏”带来了丰富的故事元素。

## 设置

我很早就决定用简单、纯粹、普通的 JavaScript 来创建这个游戏。我的职业是前端工程师，所以我认为用普通的 JS 制作这个游戏会给我最大的潜在雇主吸引力。我已经使用并喜欢 Ember 和 React，我甚至尝试过 Angular，但我总是觉得 MVC 框架对于大多数应用程序来说都是多余的。

这个游戏的第一次迭代将是基于文本的，很少或没有图形。概念验证。我做的第一件事就是为 DOM 操作创建一个助手:

```
function getById(_id) {
  return document.getElementById(_id)
} 
```

Enter fullscreen mode Exit fullscreen mode

我本来可以给`document.querySelectorAll()`起一个别名，但是我的一般偏好是对 JavaScript 钩子使用 ID，所以如果我知道我将对大多数动作使用唯一的 ID，那么就没有理由不使用`getElementById`。

我创建的第一个对象类型是一个卡片对象，因为这是一个卡片游戏。ES6/ES2015 之前的 JavaScript 没有真正的“类”系统，所以我的第一反应是创建一个普通的对象，尤其是因为 Card 对象将会非常简单。下面是它最初的样子:

```
var Card = function(data) {
  this.rank = data.rank;
  this.suit = data.suit;
  this.value = data.value;
  this.name = this.rank + " of " + this.suit;
}

Card.prototype.listItem = function() {
  var el = document.createElement('li');
  el.innerText = this.name;
  return el;
} 
```

Enter fullscreen mode Exit fullscreen mode

这当然很好，但是当我重构一些东西的时候，我把它重新配置成了一个类。现在看起来是这样的:

```
class Card {
  constructor(data) {
    this.rank = data.rank
    this.value = data.value
    this.suit = data.suit
    this.name = this.rank + " of " + this.suit
    this.graphic = '' // TODO
  }

  listItem() {
    var el = document.createElement('li')
    el.innerText = this.name
    return el
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，差别不大，但是类的语法确实使一些内容更容易阅读。此外，我删除了每一行末尾的分号；还是为了可读性。

我添加的第二个东西是一个`listItem()`方法。这给了我一个在 DOM 中显示这张卡片的快速简单的方法，类似于`Card.name`的便利属性。这里的类语法被浏览器解释为通过原型链添加函数，但是读起来更清晰一些。

这个帖子到此为止。下一次，我将开始查看游戏和关卡对象，并让您了解应用程序的 HTML 结构。