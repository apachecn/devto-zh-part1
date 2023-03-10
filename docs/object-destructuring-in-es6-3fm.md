# ES6 中的对象析构

> 原文：<https://dev.to/sarah_chima/object-destructuring-in-es6-3fm>

这是我上一篇关于数组析构的文章的后续文章。除非你有解构的想法，否则你应该读一读。

首先，让我们看看为什么需要对象析构。我们想从一个对象中提取数据并赋给新的变量。在 ES6 之前，这将如何实现？

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer"};

    var name = person.name;
    var country = person.country;
    var job = person.job;

    console.log(name);//"Sarah"
    console.log(country);//"Nigeria"
    console.log(job);//Developer" 
```

Enter fullscreen mode Exit fullscreen mode

看提取这样的数据有多繁琐。我们不得不反复做同样的事情。ES6 自带解构功能来拯救世界。让我们直接开始吧。

让我们用 ES6 重复上面的例子。我们可以使用左边的一个对象来提取数据，而不是一个接一个地分配。

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer"};

    var {name, country, job} = person;

    console.log(name);//"Sarah"
    console.log(country);//"Nigeria"
    console.log(job);//Developer" 
```

Enter fullscreen mode Exit fullscreen mode

你会得到同样的结果。将变量赋给未声明的对象也是有效的。

```
 var {name, country, job} = {name: "Sarah", country: "Nigeria", job: "Developer"};

    console.log(name);//"Sarah"
    console.log(country);//"Nigeria"
    console.log(job);//Developer" 
```

Enter fullscreen mode Exit fullscreen mode

**赋值前声明的变量**
对象中的变量可以在赋值前声明，并进行析构。让我们试试那个。

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer"}; 
    var name, country, job;

    {name, country, job} = person;

    console.log(name);// Error : "Unexpected token =" 
```

Enter fullscreen mode Exit fullscreen mode

等等！！刚刚发生了什么？哦，我们忘了在花括号前加`()`。
在没有声明的情况下使用对象文本析构赋值时，赋值语句周围的`( )`是必需的语法。这是因为左边的`{}`被认为是一个块，而不是一个对象文字。所以让我们现在就开始吧。

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer"};
    var name, country, job;

    ({name, country, job} = person);

    console.log(name);//"Sarah"
    console.log(job);//"Developer" 
```

Enter fullscreen mode Exit fullscreen mode

同样重要的是要注意，当使用这个语法时，`()`前面应该有一个分号。否则，它可能用于执行前一行中的函数。

注意，左侧对象中的变量应该与对象`person`中的属性键同名。如果名字不一样，我们就得到`undefined`。看看这个。

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer"};

    var {name, friends, job} = person;

    console.log(name);//"Sarah"
    console.log(friends);//undefined 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想使用一个新的变量名...我们可以。

**使用新的变量名**

如果我们想把一个对象的值赋给一个新的变量，而不是使用属性的名字，我们会这样做。

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer"};

    var {name: foo, job: bar} = person;

    console.log(foo);//"Sarah"
    console.log(bar);//"Developer" 
```

Enter fullscreen mode Exit fullscreen mode

因此提取的值被传递给新变量`foo`和`bar`。
**使用默认值**

默认值也可以在对象析构中使用，以防变量`undefined`在它想要从中提取数据的对象中。

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer"};

    var {name = "myName", friend = "Annie"} = person;

    console.log(name);//"Sarah"
    console.log(friend);//"Annie" 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果该值不是未定义的，变量存储从对象中提取的值，就像`name`的情况一样。否则，它会像对`friend`一样使用默认值。

当我们给一个新变量赋值时，我们也可以设置默认值。

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer"};

    var {name:foo = "myName", friend: bar = "Annie"} = person;

    console.log(foo);//"Sarah"
    console.log(bar);//"Annie" 
```

Enter fullscreen mode Exit fullscreen mode

所以`name`被从`person`中提取出来并赋给一个不同的变量。另一方面，`friend`是`person`中的`undefined`，所以新变量`bar`被赋予默认值。

**计算属性名称**

计算属性名是另一个对象文字特性，也适用于析构。您可以通过表达式指定属性的名称，如果您将它放在方括号中。

```
 var prop = "name";

    var {[prop] : foo} = {name: "Sarah", country: "Nigeria", job: "Developer"};

    console.log(foo);//"Sarah" 
```

Enter fullscreen mode Exit fullscreen mode

**组合数组和对象**

数组也可以在对象析构中与对象一起使用。下面给出一个例子。

```
 var person = {name: "Sarah", country: "Nigeria", friends: ["Annie", "Becky"]};

    var {name:foo, friends: bar} = person;

    console.log(foo);//"Sarah"
    console.log(bar);//["Annie", "Becky"] 
```

Enter fullscreen mode Exit fullscreen mode

**嵌套在对象析构中**

析构时对象也可以嵌套。

```
 var person = {
        name: "Sarah",
        place: {
            country: "Nigeria", 
            city: "Lagos" }, 
        friends : ["Annie", "Becky"]
        };

    var {name:foo,
         place: {
             country : bar,
             city : x}
          } = person;

    console.log(foo);//"Sarah"
    console.log(bar);//"Nigeria" 
```

Enter fullscreen mode Exit fullscreen mode

**停止在对象析构中**

rest 语法还可以用来挑选析构模式尚未挑选的属性键。这些键和它们的值被复制到一个新的对象上。看看下面的例子。

```
 var person = {name: "Sarah", country: "Nigeria", job: "Developer" friends: ["Annie", "Becky"]};

    var {name, friends, ...others} = person;

    console.log(name);//"Sarah"
    console.log(friends);//["Annie", "Becky"]
    console.log(others);// {country: "Nigeria", job: "Developer"} 
```

Enter fullscreen mode Exit fullscreen mode

这里，其余属性的关键字不属于列出的变量名，它们被分配给变量`others`。这里的 rest 语法是`...others`。`others`可以重命名为你想要的任何变量。

最后一件事，让我们看看如何在函数中使用对象析构。

**对象析构和功能**

对象析构可以用来给函数赋值参数。这里我们可以用一个例子。

```
 function person({name: x, job: y} = {}) {
        console.log(x);
    }

    person({name: "Michelle"});//"Michelle"
    person();//undefined
    person(friend);//Error : friend is not defined 
```

Enter fullscreen mode Exit fullscreen mode

注意参数对象右侧的`{}`。它使得我们可以调用一个函数而不传递参数。这就是我们得到`undefined`的原因。如果我们删除它，我们会得到一个错误信息。
我们也可以给参数分配默认值。

```
 function person({name: x = "Sarah", job: y = "Developer"} = {}) {
        console.log(x);
    }

    person({name});//"Sarah" 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在上面的例子中看到的，我们可以用对象析构做很多事情。

有什么问题或补充吗？留下评论。

感谢您的阅读。:)