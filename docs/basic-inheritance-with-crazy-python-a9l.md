# 疯狂 Python 的基本继承

> 原文：<https://dev.to/ogwurujohnson/basic-inheritance-with-crazy-python-a9l>

老实说，现在我甚至不知道如何写这篇文章，让任何人理解这种继承的东西是很难的。所以对于像 [@ben](https://dev.to/ben) 和 [@jess](https://dev.to/jess) (lol)这样拒绝私下教我们什么是继承的人，我会给你一本关于继承的书的定义，然后你就可以回去工作了，但是对于我们其他发现理解继承很难的人(像我一样)，只要冷静下来，跟着那些假定的简单教程和例子走就行了。

继承是一个术语，用来表示一个类将从父类获得其大部分或全部特性。在 python 中，要创建一个从另一个类继承的类，我们应该做这样的事情:

```
class [Name of new Class] ([Name of parent Class]):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我也很困惑，但是我明白，你现在可以走了。去忙你的事吧。但是对于所有像我的朋友一样拒绝理解这一点的人，请呆在原地，听到了吗？

回想一下我们一直在试验的例子，有一个雇员类的例子。假设我们需要创建一个新的类开发者。(当需要时，你会知道的)。公司中的开发人员是一名雇员，但却是一名与众不同但很酷的雇员。虽然大多数时候开发者喜欢给自己选择酷的名字，比如 [@ioedeveloper](https://dev.to/ioedeveloper) ，但是这就意味着这个家伙没有全名吗？....没有。既然一个开发人员和其他员工一样也有全名，那就意味着他和所有其他员工共享一个特殊的属性。现在试着用代码描绘一下。记住 OOP 的一个优点是代码的可重用性，按如下方式编写 employee 类会不会很酷；

```
class Developer:
    def __init__(self,first,last):
        self.first = first
        self.last = last 
```

Enter fullscreen mode Exit fullscreen mode

当我们已经有了自己的员工阶层时也是如此；

```
class Employee:
    def __init__(self,first,last):
        self.first = first
        self.last = last 
```

Enter fullscreen mode Exit fullscreen mode

当我们这样做时，我们绝不是在重用代码，这就是继承的概念发挥作用的地方。由于 employee 类有一些默认的属性和我们的 developers 类所具有的特征，我们只继承 Employee 类，本质上继承了 Employee 类的那些属性。所以现在声明这个类，我们会这样做；

```
class Developer(Employee):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

这样做非常有帮助，因为现在，我们可以创建子类并获得父类的所有功能，然后覆盖或添加全新的功能，而不会以任何方式影响父类。

我们在上一篇文章中创建了一些实例；

```
emp_1 = Employee('John','Son',50000)
emp_2 = Employee('David','Disu',7000) 
```

Enter fullscreen mode Exit fullscreen mode

但是现在我们已经声明了 **(cool)** developer 类，我们还可以创建一个 developer 实例；(更像是创造一个开发者)；

```
dev_1 = Developer('Ben','Halpern',50000)
dev_2 = Developer('David','Disu',2000) 
```

Enter fullscreen mode Exit fullscreen mode

尝试打印他们的电子邮件；

```
print(dev_1.email)
print(dev_2.email) 
```

Enter fullscreen mode Exit fullscreen mode

在运行程序时，您可以看到这两个开发人员被成功创建。如果你没有关注关于 python 面向对象的文章，你需要先写下面的代码，然后才能继续声明这个新的开发者类；

```
class Employee:
    def __init__(self,first,last,pay):
        self.first = first
        self.last = last
        self.pay = pay
        self.email = first + '.' + last + '@company.com'

    def fullname(self):
        return self.first + ' ' +self.last 
```

Enter fullscreen mode Exit fullscreen mode

编写完上述代码后，您可以继续声明 developer 类，实例化该类，并打印创建的 developers 邮件。当你运行程序时，实际发生了什么来确保程序工作；当我们创建 developers 类的实例时，程序首先在 developers 类中寻找我们的 **(init)** 方法，但是没有找到，它沿着继承链向上运行，直到找到它；这个继承链被称为**(“方法解析顺序”)**。要查看开发者类继承的**(方法解析顺序)**和属性，请这样做；

```
print(help(Developer)) 
```

Enter fullscreen mode Exit fullscreen mode

一个开发人员不仅仅有一个全名，他还有他喜欢的编程语言，他喜欢的 IDE。既然我们的 employee 类只接受(firstname，lastname，and pay)，那么我们如何在代码中处理这个额外的属性呢？为了解决这个问题，我们将为开发人员类提供自己的 init 方法。

```
class Developer(Employee):
    def __init__(self,first,last,pay,prog_lang) 
```

Enter fullscreen mode Exit fullscreen mode

现在，代替复制第一个、最后一个和支付参数，所有这些看起来奇怪的东西；

```
self.first = first
self.last = last
self.pay = pay 
```

Enter fullscreen mode Exit fullscreen mode

通过这样做，我们将允许 Employee 类处理第一个、最后一个 pay 属性；

```
class Developer(Employee):
    def__init__(self,first,last,pay,prog_lang):
        super().__init__(self,first,last,pay) 
```

Enter fullscreen mode Exit fullscreen mode

你可以用#super 来代替

```
Employee().__init__(self,first,last,pay) 
```

Enter fullscreen mode Exit fullscreen mode

所以作为一个作业，只需要谷歌一下两者的区别(超级或者父类名)。现在，为了添加编程语言参数，我们将在下添加以下代码行；

```
self.prog_lang = prog_lang 
```

Enter fullscreen mode Exit fullscreen mode

所以现在当我们实例化我们的开发者时；这将要求我们在编程语言中传递其他属性(名字、姓氏、工资)；

```
dev_1 = Developer('John','Son',5000000,'Python') 
```

Enter fullscreen mode Exit fullscreen mode

为了确保它的工作，打印开发人员的电子邮件和他的编程语言；

```
print(dev_1.email)
print(dev_1.prog_lang) 
```

Enter fullscreen mode Exit fullscreen mode

今天就到这里，伙计们。我不知道如何结束这篇文章，所以请大家帮我。