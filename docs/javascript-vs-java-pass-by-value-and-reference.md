# JavaScript 与 Java 通过值和引用传递

> 原文：<https://dev.to/tiffany/javascript-vs-java-pass-by-value-and-reference>

## 不同的语言，不同的范式

当您同时使用不同的语言时，需要注意的是，虽然大多数语言都是基于 C 的，但是每种语言都有自己的一套规则和范例，这使得相似的约定变得不同。

Java 和 JavaScript 虽然有 *Java* 的共同点，但却是完全不同的语言 <sup id="fnref1">[1](#fn1)</sup> 。因此，它们处理原始值 <sup id="fnref2">[2](#fn2)</sup> 、函数和方法、自变量和参数的方式也不同。

## 在 Java 中处理对象和方法

Java 是一种*面向对象的编程语言*，它基本上意味着创建对象作为类的一部分，实例化对象并向对象添加方法，以实际上*用新创建的对象做*事情。然后，您可以通过添加和调用以某种方式操作代码的函数，使您的代码更加模块化。 <sup id="fnref3">[3](#fn3)</sup>

您可以创建一个对象，例如:

```
 public class Dog {
      int dogAge;
      public Dog(string name) {
      System.out.println("My dog's name is " + name " .");
   }

   public void setAge (int age) {
       dogAge = age;
    }

   public int getAge() {
       System.out.println("My dog is " + dogAge + " years old");
       return dogAge;
     }

     public static void main (String []args) {
     /* Create an object using the new keyword */
         Dog myDog = new Dog("Max");
         myDog.setAge(11); // setAge is a class method on the object Dog
         myDog.getAge();
         System.out.println(myDog.dogAge);
       }
    } 
```

这里我们有一个类和一个新的对象`Dog`，在对象`Dog`上有一些 getter 和 setter 方法。

这是标准的 Java OOP 内容。JavaScript 不是一种面向对象的语言，它是一种原型语言，它创建对象原型，以便通过原型继承来扩展对象，而不是像 Java 那样的经典继承。 <sup id="fnref4">[4](#fn4)</sup>

## 在 JavaScript 中处理对象和方法

JavaScript 中的对象是以与 Java 相同的方式**而不是**创建的。至少技术上不是，还有这个:

```
 function Car() {
     const car1 = new Car();
    } 
```

应该避免。 <sup id="fnref5">[5](#fn5)</sup>

我们可以创建一个对象文字，比如:

```
 const myObject {
        foo: 'bar',
        car: 'Mazda',
        age: 38
    } 
```

并在对象上添加方法，如:

```
 myObject.car = 'Honda'; 
```

并将它们传递给其他方法，如 console.log:

```
 console.log(myObject.car); 
```

## 在 JavaScript 中按值传递

JavaScript 中唯一可以通过值传递的是原始数据类型。

通过值传递意味着你在内存中有两个变量被分配给另一个。例如:

```
 var a;
    var b = 8;

    var a = b;

    console.log(b); // will return 8 
```

当我们通过值传递这些原语时，我们正在复制存储在内存中的变量`b`的值，比方说在地址 0x001，并将其分配给存储在 0x002 的`a`。`a`和`b`互为镜像，因为它们只是彼此的复制品。这就是我们在 JavaScript 中说的通过值传递的意思。

## Java 中的按值传递

取这段代码:

```
 int y = 10;
    changeMeToSix (y);
    System.out.println(y);
    // [...some code]

    void changeMeToSix (int x) {
        x = 6;
    } 
```

这不印 6。它打印 10 张。为什么？因为只有值 10 被传递到方法中，而不是变量本身。

再比如:

```
 Dog myDog; 
```

这实际上不是一只狗，而是指向内存中狗实例的指针。所以如果我们有:

```
 public void foo (Dog yourDog) {
        yourDog.setName("Sparky");
        yourDog = new Dog("Max");
        yourDog.setName("Jeffie");
    } 
```

假设参数`yourDog`在内存地址 0x001。

第一行跟随到它所指向的`Dog`，也就是 0x001 处的`Dog`对象。那只狗被要求把名字改成 Sparky，地址还是 0x001。

在第二行，一个新的`Dog`被创建在一个不同的地址，比如 0x006。我们将`yourDog`参数分配给存储器地址 0x006。

在最后一行，yourDog 被跟踪到它在 0x006 中指向的新的`Dog`。那个`Dog`把他的名字改成了`Jeffie`。然后它返回。

`myDog`没变。`myDog`是内存中指向原始地址 0x001 的指针，而不是实际的`Dog`。

您可以通过*值*传递对参数的引用，而不是实际的引用本身。

## 通过引用传递 JavaScript

JavaScript 中的引用传递只能发生在对象上。这包括函数，因为函数是 JavaScript 中的一级对象。 <sup id="fnref6">[6](#fn6)</sup>

通过引用传递包括让两个引用指向内存中的同一个对象。这意味着您可以通过将一个对象或函数赋给另一个对象或作为参数传递给一个函数来改变它。取此代码:

```
 // by reference (all objects (including functions))
   const greet = {
      greeting: 'hi' 
    };
   const greet1;

  greet1 = greet;
  greet.greeting = 'hello'; // mutates to hello

  console.log(greet);
  console.log(greet1);

  // by reference (even as parameters)
  function changeGreeting(obj) {
    obj.greeting = 'What's up?'; // mutates to What's Up  
  }

 changeGreeting(greet1);
console.log(greet);
console.log(greet1);

// equals operator sets up new memory address
greet = { 
    greeting: 'Yo' 
};
console.log(greet); // Mutates to Yo
console.log(greet1); 
```

...诸如此类。

理解按值传递和按引用传递之间的区别是理解 JavaScript 对象和原语如何工作的关键。

## 在 Java 中通过引用传递？

你**在 Java 中不能**通过引用传递。正如[乔恩·斯基特](https://twitter.com/jonskeet?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)所说:

> 按引用传递混合了代码的输入和输出。这是这项技术的根本问题。在 Java 中，程序员可以假设变量在作为参数传递给方法时不会改变它们的值。在具有按引用传递语义的语言中，不能做出这种基本假设。
> 
> 通过引用传递会混淆方法的接口。使用按引用传递语义编写的方法可能具有非常复杂的接口，客户端程序员很难学习和理解。

通过引用排除传递是一种设计选择。它满足了某些语言的需求，比如 JavaScript，但是在其他语言中可能会出现问题。

## 那很长

抱歉，太长了！如果你有什么要补充的，请在下面留言。如果我错了或者说错了，请告诉我。我不介意。

*更新:我假设你不能在 JS 的传值例子中用 let 重新声明一个变量。所以我去了控制台，做了一些控制台的修改，决定用`var`虽然它已经过时了。还在学 ES6。*

更新 2:我学到了在 JavaScript 中不能通过引用传递，尽管这正是我所学的，尽管是错误的。可以传递对另一个对象的引用，但不能在 JavaScript 中通过引用传递。

*这篇文章最初发表在我的博客上[只是另一个开发者博客](https://tiffanywhite.tech/)T3】*

* * *

1.  然而，这两种语言都是基于 C 的。 [↩](#fnref1)

2.  Java 和 JavaScript 都有基本类型。Java 有*强类型的*原始值。Java 数据类型见[本帖](https://tiffanywhite.tech/primitive-data-types-in-java/)。 [↩](#fnref2)

3.  从技术上讲，在 Java 中，你可以互换使用方法和函数，但我真的不喜欢这样做。见[这个回答](http://stackoverflow.com/questions/155609/difference-between-a-method-and-a-function)。 [↩](#fnref3)

4.  Eric Elliott [对这个](https://medium.com/javascript-scene/common-misconceptions-about-inheritance-in-javascript-d5d9bab29b0a#.szfiambhu)谈了很多。请查看这两种语言之间的差异继承。 [↩](#fnref4)

5.  这是使用 JavaScript 构造函数和`new`关键字创建一个对象，模仿 JavaScript 中的 OOP 继承。道格拉斯·克洛克福特建议你避开它。 [↩](#fnref5)

6.  更多在另一个帖子。 [↩](#fnref6)