# 装饰者和代码重用

> 原文：<https://dev.to/karimalaa/decorators-and-code-reuse>

Decorators 是一种**设计模式**，有助于代码重用。它允许动态地将行为添加/扩展到现有对象。这个想法是装饰本身对于一个对象的基本功能并不重要。

### 什么是代码复用？

简而言之，就是通过在不同位置没有任何重复的逻辑来*干燥*(**D**on**R**EPE at**Y**your code。这是一条通向**针对相似目标的通用代码的道路**。好的做法是始终注意你的代码中相似的部分，并采取行动通过**重构**那部分到一个更可重用的代码。

**我们来举个例子:**

让我们举一个简单的游戏例子，你的角色是一个战士，可以左右移动，还可以跳跃。在这个例子中，我们将使用各种面向对象的概念。

让我们从简单地将我们的战士定义为对象开始。我们将第一个战士定义为`darkWarrior`

```
var darkWarrior = {}; 
```

我们将在我们的`darkWarrior`中添加一个属性`location`来存储角色的位置。

```
var darkWarrior = {
  location: 1,
  weapon: "Sword"
}; 
```

现在假设在某个时间点你的角色需要移动。我们需要增加我们的`darkWarrior`中的`location`属性来反映这一点。

```
var darkWarrior = {
  location: 1,
  weapon: "Sword"
};

darkWarrior.location+=1 
```

现在让我们添加另一个`lightWarrior`战士

```
var lightWarrior = {
  location: 7,
  weapon: "Spear"
};

lightWarrior.location+=1 
```

我们已经开始在这个新战士身上重演。虽然这是一个非常简单的代码，但是您已经看到了以某种方式*干燥*代码的需要。首先要问的问题是:**代码的哪一部分需要重构？**

在我们的例子中，我们可以看到我们在两个不同的地方增加了`location`属性。

重构的一种方法是将增量行为放入一个单独的函数中，如下所示:

```
var moveRight= function(warrior){
  warrior.location+=1
}

var darkWarrior = {
  location: 1,
  weapon: "Sword"
};

moveRight(darkWarrior);

var lightWarrior = {
  location: 7,
  weapon: "Spear"
};

moveRight(lightWarrior); 
```

这种重构有助于只编写一次逻辑，避免不必要的重复。此外，如果需要的话，通过在一个地方更新您的更改，这使得您的代码更易于维护。例如，如果您要将`location`计数改为递增`10`而不是`1`，在以前的情况下，我们必须在两个地方更改它，但是在重构之后，我们只需要更新`moveRight`函数。

让我们也通过`moveRight`函数成为我们的勇士对象中的一个方法。

```
var moveRight= function(warrior){
  warrior.location+=1
}

var darkWarrior = {
  location: 1,
  weapon: "Sword",
  moveRight: moveRight
};

var lightWarrior = {
  location: 7,
  weapon: "Spear",
  moveRight: moveRight
}; 
```

在这种情况下，我们不必向 moveRight 函数传递对象。我们实际上可以使用`this`来引用调用该方法的对象。

```
var moveRight= function(){
  this.location+=1
}

var darkWarrior = {
  location: 1,
  weapon: "Sword",
  moveRight: moveRight
};

var lightWarrior = {
  location: 7,
  weapon: "Spear",
  moveRight: moveRight
}; 
```

这种方法的一个问题是`this`关键字是基于调用上下文的。例如，如果我们希望战士每 1 秒钟向右移动一次，我们需要将`moveRight`函数传递给一个间隔超时函数，如下所示:

```
setInterval(lightWarrior.moveRight, 1000); 
```

### 装修工

在上面的例子中，`this`将指向全局窗口对象，而不是调用对象。我们能做的是创建一个包装器函数，它接受我们的对象并用`moveRight`函数扩展它

```
 var darkWarrior = {
  location: 1,
  weapon: "Sword",
};

var lightWarrior = {
  location: 7,
  weapon: "Spear",
};

var moveAdder=function(warrior){
  warrior.moveRigt=function(){
    warrior.location+=1
  }
  return warrior
}

darkWarrior  = moveAdder(darkWarrior)
lightWarrior = moveAdder(lightWarrior) 
```

我们称这种方法为**装饰**模式，其中我们使用的函数只有一个目的，那就是给我们的战士添加一个新的行为。我们还使用了闭包概念，用传递给装饰函数的对象的实际引用替换了`this`

我们还可以扩展这个概念，不仅增加功能，而且改变现有的功能。让我们扩展前面的例子，我们的`darkWarrior`比另一个更快。

```
var moveFaster=function(warrior){
  warrior.moveRigt=function(){
    warrior.location+=8
  }
  return warrior
}

darkWarrior.moveRight() //darkWarrior.location = 2

darkWarrior = moveFaster(darkWarrior)

darkWarrior.moveRight() //darkWarrior.location = 10 
```

装修工就这样。