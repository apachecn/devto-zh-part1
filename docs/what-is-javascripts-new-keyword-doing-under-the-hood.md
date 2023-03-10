# Javascript 的‘new’关键字在幕后做什么？

> 原文：<https://dev.to/vincecampanale/what-is-javascripts-new-keyword-doing-under-the-hood>

早上好，下午好，晚上好，晚安。关于 Javascript 中的`new`关键字，我有一些东西要和大家分享。重要的事情。

我将从一些关于构造函数和关键字`class`的上下文和背景开始。然后，我会解释一下*的`new`关键字到底在做什么。接下来，我将向*展示*如何用代码实现它。最后，我将解释*为什么*会做这些事情，并给出一些论据来避免在大多数*情况下使用这种方法来创建 Javascript 对象。这里呈现的信息来自[这些](https://www.youtube.com/watch?v=Y3zzCY62NYc) [资源](https://medium.com/javascript-scene/javascript-factory-functions-vs-constructor-functions-vs-classes-2f22ceddf33e)和其他几个，由我的大脑处理。**

### 构造函数ðÿ›

构造函数是构建并返回对象的新实例的函数。看起来是这样的:

```
/** Car: {
*    doors: number,
*    color: string,
*    drive: Function
*   }
*
* Car(doors: number, color: string) => Car
*/

function Car(doors=4, color='red') {
    this.doors = doors;
    this.color = color;
    this.drive = () => console.log('Vroom!');
} 
```

Enter fullscreen mode Exit fullscreen mode

构造函数名称开头的大写字母只是 Javascript 程序员采用的一种约定，用于将*构造函数*与常规函数分开。

构造函数在幕后工作的方式可能会成为一篇有趣的文章，但是我将把它留到以后再说。今天是关于`new`。

从本节中最重要的一点是，当用`new`关键字调用构造函数时，它将返回一个带有`doors`属性、`color`属性和`drive`方法的*对象*。

### **类**

在 ES2015 规范中，`class`关键字被引入 Javascript，通常被称为 ES6，很快被称为“只是 Javascript”。

`class`关键字并没有引入什么新东西(ha)——它只是为喜欢 Java 和语义关键字的人提供了一些语法上的好处。这没什么不对。

下面是你的使用方法:

```
class Car {
    constructor(doors=4, color='red') {
        this.doors = doors;
        this.color = color;
    }

    drive() { console.log('Vroom!'); }
    // or drive = () => console.log('Vroom!');
} 
```

Enter fullscreen mode Exit fullscreen mode

注意到什么熟悉的东西了吗？

给你个提示:

```
console.log(typeof Car) // Function 
```

Enter fullscreen mode Exit fullscreen mode

### 引擎盖下的ðÿš—

无论您是使用普通的构造函数还是特殊的关键字来实例化您的对象构造机制，您都将使用`new`来创建已定义对象的新实例。(在 Javascript 中还有另一种不那么秘密且强大的生成对象的方法，叫做[工厂](https://medium.com/javascript-scene/javascript-factory-functions-vs-constructor-functions-vs-classes-2f22ceddf33e)函数，这将在以后的文章中介绍)。

那么`new`关键字在引擎盖下(用人类的话说)做什么呢？

三封信，四个动作。当你说`var myCar = new Car()`时，它...

```
1) Creates a new (empty) object 
2) Gets the prototype of the constructor function (Car) and sets it as the empty object's prototype
3) Calls the constructor function with the new empty object as `this` 
4) Returns the new object 
```

Enter fullscreen mode Exit fullscreen mode

这个过程用计算机的话说是什么样子的？

*注意:*为了重新实现`new`，我们必须分别传入构造函数和它的参数。

首先，让我们在 ES5 中完成，因为你只活一次。

```
// new(constructor: Function, constructorArgs: Array<any>) => Object
function new2(constructor, constructorArgs) {

    // Step 1: Create an empty object
    var newObject = {};

    // Step 2a: Get the prototype of the constructor function
    var constructorPrototype = constructor.prototype;
    // Step 2b: Set the empty object's prototype 
    Object.setPrototypeOf(newObject, constructorPrototype);

    // Retro technique to turn arguments into an actual array 
    var argsArray = Array.prototype.slice.apply(arguments); 
    // Slice off first argument b/c that's the constructor function itself. 
    var realConstructorArgs = argsArray.slice(1);

    // Step 3: Invoke constructor with newObject as 'this'
    constructor.apply(newObject, realConstructorArgs);

    // Step 4: Return the new object :)
    return newObject;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个工作的实现，我们可以清理它并利用 ES6 的一些新工具。

```
// new(constructor: Function, constructorArgs: Array<any>) => Object
function new2(constructor, ...constructorArgs) {
    const newObject = {};
    Object.setPrototypeOf(newObject, constructor.prototype);    
    constructor.apply(newObject, constructorArgs);
    return newObject;
} 
```

Enter fullscreen mode Exit fullscreen mode

和...

```
const myCar = new2(Car, 4, 'blue');
console.log(myCar) // { doors: 4, color: 'blue', drive: [Function] }
myCar.drive() // Vroom! 
```

Enter fullscreen mode Exit fullscreen mode

*但是等等*，有一个边缘案例。如果构造函数本身返回一个新的对象，就像这样...

```
function Car(doors, color) {
    this.doors = doors;
    this.color = color;
    this.drive = () => console.log('Vroom!');
    return {
      doors,
      color
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该直接返回那个对象:

```
// new(constructor: Function, constructorArgs: Array<any>) => Object
function new2(constructor, ...constructorArgs) {
    const newObject = {};
    Object.setPrototypeOf(newObject, constructor.prototype);
    return constructor.apply(newObject, constructorArgs) || newObject;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们结束了。

希望这有所帮助！

如果有或没有，发微博给我反馈[@ _ Vince campanele](https://twitter.com/_vincecampanale)。

直到下次ðÿ'‹.