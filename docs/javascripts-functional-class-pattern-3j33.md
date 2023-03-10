# JavaScript 的函数类模式

> 原文：<https://dev.to/sophiedebenedetto/javascripts-functional-class-pattern-3j33>

这篇文章最初发表在几年前我的博客上。这个月，我在熨斗学校教授[JS 入门/速成训练营预备](https://flatironschool.com/programs/accelerated-bootcamp-prep/)课程(玩得很开心！).回答这么多不同的学生提出的问题总是让我惊讶，迫使我用新的眼光看待旧的概念，甚至学习新的东西。受到一个 Bootcamp 预备学生问我的问题的启发，我重温了我不久前写的一篇文章，当时我第一次试图理解 JavaScript 中的类的概念。我想我应该把它重新贴在这里给新手和更有经验的人。即使我们认为基本概念已经没什么可教我们的了，重新思考它们也无妨——以我的经验来看，它们几乎总是如此。

* * *

在 Ruby 中，我们认为类是能够产生只处理一种责任的对象的实体。例如，在一个博客应用程序中，你会有一个负责创建帖子的“Post”类。

在 Javascript 中，类的定义非常相似。

什么是 Javascript 类？

简而言之，Javascript 中的类是任何能够产生大量相似对象的函数——也就是说，具有相似属性的对象。

在 Ruby 中，创建一个类很简单，而且不像 Ruby 中的许多其他东西，只有一种方法可以做到:

```
class Cylon

 def initialize(number, attitude)
   @number = number #cylons don't have names, they 
   have numbers, it makes them much scarier, trust me. 
   @attitude = attitude
 end

 def human_disguise(human_name)
   @human_name = human_name
 end

end 
```

赛昂人负责制造新的超级智能和类人机器人，它制造的每个机器人都将与其兄弟姐妹共享属性。(没错，我们又回到了太空堡垒 Gallactica 的土地，抱歉伙计们)。

在 Javascript 中，有几种方法可以产生相似的对象。在深入研究函数类模式之前，让我们先来看几个例子。

**代表团**

为了编写可重用的代码并保持代码干燥，有必要创建共享属性的 Javascript 对象。

例如，如果你试图接管世界(或者拯救它？BSG 的结局总是让我很困惑)，你需要制造很多赛昂人。这些赛昂人至少需要分享他们的一些财产——比如，他们一定都想接管世界。

让我们来看看如何使用委托来实现这一目标。

下面的代码创建了一个变量`cylon`,并将它设置为一个具有我们需要的属性的对象。

```
var numberEleven = {species: "Cylon", attitude: "subdued"} 
```

现在，如果我们想创造更多的赛昂人，我们可以使用委托:

```
var numberEight = Object.create(numberEleven) 
```

存储在变量`numberEight`中的对象现在具有与存储在变量`numberEleven`中的对象相关联的所有属性。

这意味着当我们调用`numberEight.species`时，对该值的查找被委托给`numberEleven`。这种委托是持续的，并且是实时执行的，无论何时运行代码和读取特定的行。

您仍然可以像与任何其他对象交互一样与`numberEight`对象交互。也就是说，我们可以赋予`numberEight`它自己的属性。例如:

`numberEight.humanName = "Sharon"`

现在，`console.log(numberEight.humanName)`将输出“Sharon ”,而不必将该查找委托给`numberEleven`对象。

**对象装饰图案**

到目前为止，我们已经使用委托创建了共享属性的 Javascript 对象。如果我们需要具有相似功能或方法的相似对象，该怎么办？

例如，所有的赛昂人都需要分享撒谎的能力，谎称自己是赛昂人。

换句话说，`lie(numberEight)`应该输出“我不是赛昂人！我是人类！”存储在`numberEleven`中的对象也应该如此。

保持代码干燥，我们不想用相同的功能定义两个对象——那将导致我们每次创建一个新的赛昂人时都要重复自己。

让我们抽象出`lie`功能:

```
var lie = function(cylon){
      cylon.attitude = "aggressive";
      console.log("I'm not a Cylon! I'm a human!");
} 
```

到目前为止，我们已经有了一个非常基本的赛昂对象的例子，一个产生额外赛昂对象(委托)的方法，以及一个处理赛昂对象并向其添加内容的函数。

有几个原因让我们对目前的代码不太满意。

现在，为了调用`lie`函数，我们需要给它传递一个赛昂对象的参数。如果我们想呼叫赛昂人 ojbect 上的`.lie`呢？换句话说，如果我们希望每个赛昂人都有开箱即用的`lie`能力呢？现在用我们的代码这是不可能的。

此外，随着我们的程序变得越来越复杂，随着赛昂人越来越接近毁灭地球，我们可能想要创造具有更复杂功能和额外属性的赛昂物体。我们之前的代表团再也不会削减了。

让我们加快速度:

```
var cylonLike = function(obj, number, attitude)

    obj.number = number;
    obj.attitude = attitude;
    return obj;
} 
```

哒哒！我们有了第一位室内设计师。函数的工作是接收一个对象，并给它增加被认为是赛昂人所需的属性。

当一个函数接受一个对象并用额外的属性或功能扩充该对象时，这个函数被认为是一个**装饰器**。

让我们使用我们的装饰器创建一个新的赛昂对象，它具有赛昂人需要的所有属性:

`var numberSeven = cylonLike({}, 7, "subdued")`

我们调用`cylonLike`函数，向它传递一个空对象、一个数字和一个态度。然后，`cylonLike`以上面定义的方式处理该对象。

装饰者不限于向对象添加属性。他们还可以添加功能，即方法。

我们之前定义的`lie`函数可以添加到`cylonLike`装饰器中，这样所有通过我们的装饰器函数传递的赛昂对象都可以有能力调用 lie 函数并逃脱人类的检测来执行他们邪恶的计划。

```
var cylonLike = function(obj, number, attitude)

    obj.number = number;
    obj.attitude = attitude;
    obj.lie = lie;
    return obj;

} 
```

我们*几乎*有能力在一个赛昂物体上调用`.lie`。我们需要重构我们的`lie`函数，它不再需要接受一个对象，而是在一个对象上被调用:

```
var lie = function(){
      this.attitude = "aggressive";
      console.log("I'm not a Cylon! I'm a human!");
} 
```

这个例子很简单，我们发现自己将一个空对象传递给了`cylonLike`装饰函数。装饰函数通常用于向已经附加了属性的对象添加一些属性/功能。

**功能类模式**

之前，我们将类定义为任何能够产生一系列相似对象的函数。我们上面的装饰函数当然有这个能力。然而，我们仍然不认为这是一个通常意义上的类。一个类构建它要扩充的对象，而装饰器接受它要扩充的对象作为参数。

因为我们上面装饰的对象是空的，所以让我们首先让我们的`cylonLike`函数为我们构建赛昂人:

```
var Cylon = function(number, attitude){

  var obj = {number: number, attitude: attitude};
  obj.lie = lie;
  return obj;

} 
```

我们在那里上了第一堂课！产生我们相似对象的函数叫做*构造函数*。

当构造函数被调用时，它创建了我们的`Cylon`类的*实例*:

`var numberThirteen = Cylon(13, "subdued")`

但是，如果我想将方法添加到`Cylon`类的实例中，该怎么办呢？或者，如果我事实上*不*需要*我所有的*赛昂人都有说谎的能力呢？还记得莎伦甚至不知道她是一个赛昂人吗？那她怎么能撒谎呢？进入**原型制作。**

**原型制作**

我们已经知道，任何对象都可以将其失败的属性查找委托给另一个对象。然而，目前我们陷入了困境。如果我们让我们的构造函数保持在当前状态，我们就不是干巴巴的——我们允许在运行时读取和存储`lie`方法，每次我们创建一个新的 Cylon。我们只希望在实际调用`lie`方法时发生这种情况。但是，如果我们从构造函数中取出`lie`方法，任何其他方法，我们怎么能让我们的赛昂实例访问它呢？

我们可以使用**原型对象**来存储共享方法。如果我们稍微重构一下我们的构造函数代码，我们可以给 Cylon 类的所有实例将失败的查找委托给存储在该对象中的方法的能力。

首先，让我们创建我们的原型对象:

```
Cylon.prototype.lie = function(){
  this.attitude = "aggressive";
  console.log("I'm not a Cylon! I'm a human!");
} 
```

现在，让我们重构我们的赛昂构造函数:

```
var Cylon = function(number, attitude){

  var obj = Object.create(Cylon.prototype);
  obj.number = number;
  obj.attitude = attitude
  return obj;

} 
```

现在，`Cylon`类的每个实例都可以调用`.lie()`，但是编译器不会读取和存储那个函数，直到它被`Cylon`的实例调用。

暂时就这样吧！这是对 Javascript(和 Battlestar Gallactica)中面向对象的简要介绍。