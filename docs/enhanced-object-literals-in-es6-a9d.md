# ES6 中增强的对象文字

> 原文：<https://dev.to/sarah_chima/enhanced-object-literals-in-es6-a9d>

对象文字使得快速创建带有花括号内属性的对象变得容易。要创建一个对象，我们只需标记一个由逗号分隔的键值对列表。ES6 使得对象文字的声明简洁，因此更容易。它有三种主要方式:

1.  它提供了从变量初始化属性的简写语法。
2.  它为定义函数方法提供了一种简化的语法。
3.  它支持在对象文字定义中包含计算属性名的能力。

让我们逐一检查一下。

**初始化属性的简写**
我们将用一个例子来说明这一点。让我们创建一个`getLaptop`函数。我们在这里使用了一个函数，使得传递变量更容易。我们基本上还在创造一个物体。在 ES6 之前，如果我们想用对象文字初始化一个对象的属性，我们将做以下事情:

```
 //ES5
    function getLaptop(make, model, year) {
        return {
            make: make,
            model: model,
            year: year
        }
    }

    getLaptop("Apple", "MacBook", "2015");// {make: "Apple", model: "MacBook", year: "2015"} 
```

Enter fullscreen mode Exit fullscreen mode

所以在上面的函数中，返回的对象是使用对象文字创建的。该对象的属性是通过将参数值传递给相应的键来创建的。你注意到那里的重复了吗？我也是。ES6 删除了所有的重复。下面是我们将如何编写我们的`getLaptop`函数；

```
 function getLaptop(make, model, year) {
        return {
            make,
            model,
            year
        }
    }

    getLaptop("Apple", "MacBook", "2015"); // {make: "Apple", model: "MacBook", year: "2015"} 
```

Enter fullscreen mode Exit fullscreen mode

更容易写和读。这里发生的事情是，它检查属性键是否有相应的变量名，并将该变量值赋给属性。注意，如果没有变量与定义的属性键同名，我们将得到一个错误。让我们进入下一个增强阶段。

**书写方法的速记**
在 ES6 之前，对象中书写方法的语法是这样的:

```
 //ES5
    function getLaptop(make, model, year) {
        return {
           sayModel : function() {
                return model;
            }
        }
    }

    getLaptop("Apple", "MacBook", "2015").sayModel(); //"MacBook" 
```

Enter fullscreen mode Exit fullscreen mode

有了 ES6，我们不必仅仅为了让一个方法工作而写很多代码。

```
 //ES5
    function getLaptop(make, model, year) {
        return{
            sayModel() {
                return model;
            }
        }
    }

    getLaptop("Apple", "MacBook", "2015").sayModel(); //"MacBook" 
```

Enter fullscreen mode Exit fullscreen mode

你注意到不同了吗？不再需要`:`和`function`来定义方法。所以 ES6 使创建方法的语法变得简洁。

**计算属性和对象字面量**
如果你读过我之前关于[对象析构](https://dev.to/sarah_chima/object-destructuring-in-es6-3fm)的文章，你可能会遇到这种情况。您可能已经知道，在访问对象属性时有两种方式来指定键:点符号和括号符号。括号符号允许我们使用表达式来访问属性。计算属性名允许我们编写一个用方括号括起来的表达式，而不是常规的属性名。表达式计算出的值将成为属性名。这意味着我们可以这样做:

```
 var name = "make";
    const laptop = {
        [name]: "Apple"
    }

    console.log(laptop.make);//"Apple" 
```

Enter fullscreen mode Exit fullscreen mode

将`name`的值计算为`make`，并将其用作属性的名称。这就是为什么我们可以使用`laptop.make`访问属性；

同样，这也行得通。

```
 var name = "make";
    var i = 0;
    const laptop = {
        [name + ++i]: "Apple",
        [name + ++i]: "Dell",
        [name + ++i]: "HP"
    }

    console.log(laptop.make1);//"Apple"
    console.log(laptop.make2);//"Dell"
    console.log(laptop.make3);//"HP" 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，计算并连接`name`和`i`的值，以获得属性的名称。我觉得这很酷，你觉得呢？

这就是增强对象文字的全部内容。

有什么问题或补充吗？留下评论。

感谢阅读。:)