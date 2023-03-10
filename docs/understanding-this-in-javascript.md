# 理解 JavaScript 中的“this”

> 原文：<https://dev.to/yaphi1/understanding-this-in-javascript>

*这篇[文章](https://simplestepscode.com/javascript-this/)最初出现在[简单步骤代码](https://simplestepscode.com/)上，它尽力帮助人们[学习 JavaScript 并真正记住它](https://simplestepscode.com/learn-javascript/)。*

在 JavaScript 中，`this`关键字回答以下问题:
**我们的函数在被调用时属于哪个对象？**

如果这没有意义，没关系！在一些快速的背景信息和例子之后，它会更有意义。

## 一、什么是物体？

对象是存储在名称-值对(也称为键-值对)中的一组数据。

例如:

```
var car = {
    color: 'purple',
    topSpeed: 9001,
    model: 'magic school bus',
    driver: 'Waluigi'
}; 
```

Enter fullscreen mode Exit fullscreen mode

一个对象可以存储很多东西，包括字符串、数字、数组、函数，甚至其他对象。

## 接下来，什么是方法？

方法是属于对象的函数。

例如:

```
var car = {
    color: 'purple',

    makeNoise: function(){
        console.log('vroom');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在那个例子中，`makeNoise`是我们 car 对象的一个方法，我们可以这样调用它:

```
car.makeNoise(); 
```

Enter fullscreen mode Exit fullscreen mode

## 那么`this`在哪里呢？

在 JavaScript 中，`this`仅仅意味着我们的函数所属的对象。

在下面的例子中，`this`将是我们的汽车对象。

```
var car = {
    currentSpeed: 0,

    goFaster: function(){
        this.currentSpeed += 10;
    }
}

console.log('speed before: ' + car.currentSpeed);

car.goFaster();

console.log('speed after: ' + car.currentSpeed); 
```

Enter fullscreen mode Exit fullscreen mode

如果您尝试这个例子，您会看到`goFaster`函数将`currentSpeed`增加了 10。

## `this`关键词有什么有用的例子？

假设我们想让一些元素在被点击时变成红色并有一个红色的边框。

以下代码太重复:

```
<div id="first">first text</div>
<div id="second">second text</div>

<script>
var firstDiv = document.getElementById('first');
var secondDiv = document.getElementById('second');

firstDiv.onclick = function(){
    firstDiv.style.color = 'red';
    firstDiv.style.border = '1px solid red';
};

secondDiv.onclick = function(){
    secondDiv.style.color = 'red';
    secondDiv.style.border = '1px solid red';
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

多痛苦啊！

如果我们只想要一个可以说，"嘿。看到这个 div 了吗？改这个 div。”

这比为每个 div 重写函数要好得多。

我们的`this`关键词可以帮助我们:

```
<div id="first">first text</div>
<div id="second">second text</div>

<script>
var firstDiv = document.getElementById('first');
var secondDiv = document.getElementById('second');

function turnRed(){
    this.style.color = 'red';
    this.style.border = '1px solid red';
}

firstDiv.onclick = turnRed;
secondDiv.onclick = turnRed;
</script> 
```

Enter fullscreen mode Exit fullscreen mode

看到这有多整洁了吗？

但是等等...我以为`this`应该是指我们函数所属的对象。我们的`turnRed`函数好像不属于任何对象吧？

**这里是重点:**
**`this`关键字只在每次运行函数时设置，而不是在第一次声明函数时设置。**

所以在上面的例子中，我们的`onclick`方法被设置为`turnRed`。`onclick`方法属于它们对应的 div，所以在每种情况下，`this`都意味着“这个 div”。

如果单独调用`turnRed()`会怎么样？

如果一个函数在被调用时不属于另一个对象，那么默认情况下它属于`window`对象。

这意味着`this`将引用窗口，并且会发生错误，因为您不能将 CSS 样式直接放在窗口上。

## 快速回顾到目前为止

*   在 JavaScript 中`this`是我们的函数在被调用时属于哪个对象。

*   `this`关键字是在运行函数时设置的，而不是在第一次声明它时。

*   除非另有说明，`this`默认设置为“窗口”。

## 我怎么才能看出`this`是什么？

如果你被卡住了，你可以通过记录到控制台来检查`this`是什么。

很方便，`console.log()`不会干扰`this`的值，所以你不用担心`console.log(this);`会说“控制台”。

如果有疑问，就把它记录下来。

## `this`能变吗？

耶！

有时，`this`不方便。也许你希望它指的是一件事，但它指的是另一件事。

[绑定](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)，[调用](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)，[应用](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)， [ES6 箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)让你决定你想要`this`成为什么。

现在，你已经有足够的东西要记住了，但是如果你想知道更多，我已经链接了这些概念。

## 关键要点

*   是运行函数时函数所属的对象。

*   不要想多了。

*   如果你被卡住了，只要`console.log(this);`你就会知道`this`是什么。

我希望这有所帮助，并随时查看[简单步骤代码](https://simplestepscode.com/learn-javascript)以获得更多 JavaScript 技巧。

如果有什么问题，欢迎在评论里留下，我会尽力解答。