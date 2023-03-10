# Javascript 中的面向对象程序设计简介

> 原文：<https://dev.to/ndiecodes/introduction-to-oop-in-javascript-ao0>

这是什么东西？

面向对象编程(OOP)是一种编程语言模型，它是围绕对象而不是“动作”和数据而不是逻辑来组织的。

为了更好地理解，让我们看看作为一个对象的人，一个人可以有什么属性？腿、手、头等；这些是人的**属性**。好的，那么一个人能做什么，跑，走，爬，说话，坐，站等等；这些是 Person 对象的方法。注意，当我提到 Person 对象时，我一直用大写字母“P ”,这就是类名的写法。

面向对象程序设计的基本思想是我们用对象来模拟真实世界的事物，我们希望在我们的程序中表现这些事物-developer.mozilla.org

让我们看一些代码示例，好吗？

# 定义类别

我们使用`class`关键字和名称(“Person”)来定义类。属性是用构造函数方法编写的。关键字`this`给类分配属性，**这里的这个**指的是类的一个实例，如果`class`是一个名词，就把`this`想象成一个代词。

```
//class declaration

class Person {
     constructor() {
        this.name = "Isaac";
         this.age = 21;
     }
}

let person = new Person();

console.log(person.name); // logs - Isaac 
```

Enter fullscreen mode Exit fullscreen mode

这看起来不错，但是如果我们希望我们程序的用户输入他们的姓名和年龄，那么我们必须向我们的构造函数方法添加*参数*怎么办？参数是占位符，用于函数中接受参数(什么是参数？只是价值观 pal)。代码如下:

```
class Person {
     constructor(name, age) {
        this.name = name;
         this.age = age;
     }
}

let person = new Person("Isaac", 23);

let person1 = new Person("Bob", 28);

console.log(person.name); // logs - Isaac
console.log(person1.name);// logs - Bob 
```

Enter fullscreen mode Exit fullscreen mode

# 类方法

这很酷，现在让我们看看方法(getters、setters 等)，它们一点也不可怕，让我们看一个例子:

```
class Person {
    constructor(name, age) {
       this.name = name;
       this.age = age;
    }

   // setter
   setName(name) {
       this.name = name;
   }

   //getter
   bio() {
       return this.description();
   }

   //some method with a lil logic
   description(){
       return this.name + " is " + this.age + "years old.";
   }

}

let person = new Person("Isaac", 23);

person.setName("Joy");

console.log(person.bio()); // logs -  Joy is 23years old. 
```

Enter fullscreen mode Exit fullscreen mode

我告诉过你它们并不可怕，getters 只是得到属性值，而 setters 改变属性值
，我利用这个机会向你展示你可以用另一个方法返回另一个方法，注意我们可以简单地在我们的`bio()`方法中做`return this.name + " is " + this.age + "years old.";`。

# 继承

现在我们有一个很好的人类来描述一个人，但是随着我们程序的深入，我们可能会有其他的类，比如老板，父亲，母亲，工人等等。所有这个类将有相同的属性的人类和更多。如果可以使用继承，为什么要一遍又一遍地编写相同的代码。

在这里，父亲继承 Person 的属性/方法。

```
 //Parent class
class Person {
    constructor(name, age) {
       this.name = name;
       this.age = age;
    }
   setName(name) {
       this.name = name;
   }
   bio() {
       return this.description();
   }
   description(){
       return this.name + " is " + this.age + "years old.";
   }

}

//child class
class Father extends Person {

    bio(){
        return super.bio();
    }
}

var father = new Father("Isaac", 34);

console.log(father.name) //logs - Isaac
console.log(father.bio()); //logs - Isaac is 34years old. 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`extends`来允许父亲访问 Person 的属性/方法。
注意到了用来返回`bio()`的`super`？

我们使用`super`来访问父类(“Person”)的方法。

# 结论

我们讨论了类的定义、属性、方法和继承，如果你需要更多的信息，mozilla 总会帮助你；

我将在评论区等待你的精彩贡献。

谢谢你花时间阅读到最后，我很感激，再见。