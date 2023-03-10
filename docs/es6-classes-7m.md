# ES6 classes

> 原文：<https://dev.to/sarah_chima/es6-classes-7m>

在 ES6 之前，Javascript 中一直使用类。ES6 只是提供了一个干净、好看的语法来定义类。值得注意的是，JavaScript 的类只是创建构造函数的一种更方便的方式，与传统的类有很大不同。在本文中，我们将重点关注:

1.  如何定义一个类
2.  如何定义一个类的实例
3.  类中的方法
4.  创建一个类的子类。

### 在 ES6 中定义类

如上所述，ES6 类只是一种更方便的方式来做一些已经存在的事情。例如，假设我们想为`Person`定义一个类，并为该类定义一个方法`sayName`。在 ES5 中这将如何实现？

```
 //ES5
    function Person (name, height) {
        this.name = name;
        this.height = height;
    }

    Person.prototype.sayName = function() {
        this.console.log(this.name);
    } 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
 //ES5
    function Person (name, height) {
        this.name = name;
        this.height = height;
        this.sayName = function() {
           console.log(this.name);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

首先注意，我们必须使用一个函数来定义这个类。在上面的第一个例子中，方法是通过将方法添加到`prototype`中来定义的。第二个例子是在内部添加方法的另一种方式。

ES6 提供了一种使用`class`关键字定义类的新的、漂亮的方法。正如我们将在本文中讨论的那样，这种方法还有其他很酷的特性。首先，让我们看看如何在 ES6 中编写我们的`Person`类。

```
 //ES6
    class Person {
        constructor() {
            this.name = "Person";
            this.height = 150;
        }

        sayName() {
            console.log(this.name);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

更优雅对吗？当然，但是等一下，这是怎么回事？那个`constructor`在那里做什么？
首先，类的主体是指`{}`之间的部分。这是定义方法和属性的地方。`constructor`方法是一个特殊的方法，用来定义和初始化一个用类创建的对象。在上面的例子中，我们只是初始化了`this.name`和`this.height`的值。因此，即使在没有设置这些值的情况下创建了类的实例，也可以使用默认值。如果一个`constructor`方法没有被添加到一个类中，也没关系。一个空的`constructor`方法将被添加。

**注意**:一个类中只能有一个名为“constructor”的方法。如果类包含不止一个构造函数方法，将抛出一个`SyntaxError`。
上面的例子是作为类声明编写的。重要的是要注意，与它们的函数对应物不同，类声明是不悬挂的。因此，在定义类之前，不能使用它。

类也可以写成表达式。所以`Person`的例子可以写成:

```
 //unnamed
    var Person = class {
        constructor() {
            this.name = "Person";
            this.height = 150;
        }

        sayName() {
            console.log(this.name);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
 //named
    var Person = class Person{
        constructor() {
            this.name = "Person";
            this.height = 150;
        }

        sayName() {
            console.log(Person.name);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

命名的`class`的行为方式与命名的函数表达式相同。任何用命名的`class`创建的变量都将有`name`属性。这个`name`即使赋给变量也不会改变。`class`的名字总是指用来创造它的名字。可以阅读[这个](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function)进一步参考。类表达式也不吊。

### 创建类的实例

如何创建和使用一个类的实例？这是老一套。让我们创建上面刚刚创建的类的一个实例。

```
 var Sarah = new Person();
   Sarah.sayName();//"Person"

   Sarah.name = "Sarah";
   Sarah.sayName(); //"Sarah" 
```

Enter fullscreen mode Exit fullscreen mode

使用关键字`new`创建一个类的实例。因为一个类基本上是一个对象，我们调用它的方法就像我们调用对象的方法一样。你能看到`constructor`方法在起作用吗？如果没有设置`Person`的实例的`name`，则创建的实例的`name`被设置为`Person`。

当您在 JavaScript 中创建一个类的实例时，会创建一个新的对象，但是它仍然依赖于它的父类。而是创建一个链接到原型的对象。因此，对该原型所做的任何更改都会影响新对象，即使是在创建实例之后。

### 类中的方法

使用`class`可以创建两种类型的方法:原型方法和静态方法。

**原型方法**

在上面的例子中使用的方法`sayName`是一个原型方法。原型方法可以由类的实例调用。原型方法还包括 getters 和 setters。

```
 class Person {
        constructor(firstName, lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }

        get fullName() {
            return this.computeFullName; 
        }

        computeFullName() {
            return this.firstName +"  "+ this.lastName;
        }
    }

    var Sarah = new Person("Sarah", "Chima");
    console.log(Sarah.fullName());//Sarah Chima 
```

Enter fullscreen mode Exit fullscreen mode

**静态方法**

类的实例不能调用静态方法。它们仅适用于在没有创建实例的情况下被调用的类。如果从一个实例调用一个`static`方法，将会得到一个错误

```
 class Person {
        constructor() {
            this.name = "Person";
            this.height = 150;
        }

        static sayName(name) {
            console.log(name);
        }
    }

    Person.sayName("Sarah");//"Sarah"

    var Chima = new Person();
    Chima.sayName("Chima");//error : Chima.sayName is not a function 
```

Enter fullscreen mode Exit fullscreen mode

### 子类化类别

可以使用关键字`extends`创建`class`的子类。当您创建一个`class`的子类时，`class`就变成了一个基类。让我们创建一个`Person`的子类。

```
 //Base class
    class Person {
        constructor() {
            this.name = "Person";
            this.height = 150;
        }

        sayName() {
            console.log(this.name);
        }
    }

    //subclass
    class Developer extends Person {
        constructor(name, height) {
            super(name, height); 
            this.name = "Developer"    
        }

    }

    var Jessy = new Developer ();
    Jessy.sayName();//"Developer" 
```

Enter fullscreen mode Exit fullscreen mode

`Developer`是`Person`的子类。注意，在子类的`constructor`中有一个对`super()`的调用。它用于进行超级构造函数调用，并允许访问父方法，即构造函数使用`super`关键字来调用父类的构造函数。这真的很重要，因为在派生类中，必须在使用`this`之前调用 super()。忽略这一点会导致参考误差。

```
 //Base class
    class Person {
        constructor() {
            this.name = "Person";
            this.height = 150;
        }

        sayName() {
            console.log(this.name);
        }
    }

    class Developer extends Person {
        constructor(name, height) {
            this.name = "Developer"    
        }

    }

    var Jessy = new Developer ();
    Jessy.sayName();//Must call super constructor in derived class before accessing 'this' or returning from derived constructor 
```

Enter fullscreen mode Exit fullscreen mode

最后一件事。在子类中，我们可以创建自己的方法。我们甚至可以创建一个与基类的方法同名的方法。子类的方法将覆盖基类的方法。那么让我们给子类`Developer`添加更多的方法。

```
 //Base class
    class Person {
        constructor(name, height) {
            this.name = "Person";
            this.height = 150;
        }

        sayName() {
            console.log(this.name);
        }
    }

    class Developer extends Person {
        constructor(name, height) {
            super(name, height);         
        }

        sayName(name) {
            console.log("I am an awesome Developer and my name is " +name)
        }

        sayHeight () {
            console.log("This is my height: " +this.height+ "cm");
        }
        sayTools(tool1, tool2) {
            console.log("I love " +tool1 + " and " +tool2 );

        }
    }

    var Sarah = new Developer ();
    Sarah.sayName("Sarah");//"I am an awesome Developer and my name is Sarah"

    Sarah.sayHeight();//"This is my height: 150cm"

    Sarah.sayTools("JavaScript", "CSS");//"I love JavaScript and CSS" 
```

Enter fullscreen mode Exit fullscreen mode

就这样结束了。

**免责声明**:我的身高不是 150cm。哈哈！！:)

有什么问题或补充吗？请留言评论。

感谢您的阅读:)