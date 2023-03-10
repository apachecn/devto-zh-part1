# Python 中的继承

> 原文：<https://dev.to/arsho/inheritance-in-python-8dn>

继承是面向对象编程的核心话题之一。当设计或开发一个复杂的软件时，它在很多场景中都很方便。Python 支持使用最少数量的语法和语义进行继承。

继承是继承现有类的属性和方法的机制。继承的类可以添加更多自己的方法和/或属性。用作继承基础的类被称为`base class`或`parent class`或`super class`。另一方面，从父类继承的类被称为`child class`或`subclass`或`derived class`。

我们来看一个继承的例子:

```
class Animal:
    def eat(self):
        print("It eats.")
    def sleep(self):
        print("It sleeps.")

class Bird(Animal):
    def fly(self):
        print("It flies in the sky.")

    def sing(self):
        print("It sings.")

print(issubclass(Bird, Animal))

duck = Bird()
print(isinstance(duck, Bird))

duck.eat()
duck.sleep()
duck.fly()
duck.sing() 
```

Enter fullscreen mode Exit fullscreen mode

这里的`Bird`类继承了`Animal`类。所以，`Animal`是父类(也称为超类或基类)，而`Bird`是子类(也称为子类或派生类)。`issubclass`方法确保`Bird`是`Animal`类的子类。

然后我们创建了一个名为`duck`的 Bird 类实例。在这种情况下，`isinstance`方法确保`duck`是`Bird`类的一个实例。我们调用了`Animal`类的方法来检查它们是否在`Bird`类中被继承。每种动物都吃饭睡觉。鸟也是动物。所以，他们继承了动物的特性。相反，只有鸟会飞，会唱歌。这两个特征使它们区别于其他动物。

现在，让我们帮助鸟儿唱歌。安装一个名为`pyttsx3`的 Python 包，赋予鸟生命。

您可以使用`pip install pyttsx3`简单地安装软件包。

安装包之后，我们将创建一个名为`speaker.py`的新文件，其中包含一个名为`Speaker`的类。这个类将把任何文本转换成声音。`speaker.py`包含:

```
import pyttsx3

class Speaker:
    engine = pyttsx3.init()
    def speak(self, text = "No sound is found."):
        print(text)
        self.engine.say(text)
        self.engine.runAndWait() 
```

Enter fullscreen mode Exit fullscreen mode

然后，在我们的案例研究示例中，添加另一个名为`SingingBird`的类，它继承了`Bird`类和`Speaker`类。它叫做*多重遗传*。将之前的代码更新如下:

```
from speaker import Speaker
class Animal:
    def eat(self):
        print("It eats.")
    def sleep(self):
        print("It sleeps.")

class Bird(Animal):
    def fly(self):
        print("It flies in the sky.")
    def sing(self):
        print("It sings.")
    def speak(self, text = "No sound is found."):
        print("It can not speak but can write it.")
        print(text)

class SingingBird(Speaker, Bird):
    def __init__(self, birdName = "Bird Name"):
        self.birdName = birdName
    def printBirdName(self):
        print("This is a {birdName}!".format(birdName = self.birdName))

print(SingingBird.__bases__)

duck = SingingBird("Duck")
duck.printBirdName()
duck.eat()
duck.sleep()
duck.fly()
duck.sing()
duck.speak("Quack quack, quack quack, quack quack") 
```

Enter fullscreen mode Exit fullscreen mode

输出如下所示:

```
(<class '__main__.Bird'>, <class 'speaker.Speaker'>)
This is a Duck!
It eats.
It sleeps.
It flies in the sky.
It sings.
Quack quack, quack quack, quack quack 
```

Enter fullscreen mode Exit fullscreen mode

`SingingBird`的`__bases__`属性显示了`(<class '__main__.Bird'>, <class '__main__.Speaker'>)`，这确保了该类的多重继承。所以，我们看到 Python 像魔咒一样支持`Multiple Inheritance`。

我们看到`speak`方法同时存在于`Bird`和`Speaker`类中。由于`SingingBird`继承了`Bird`类和`Speaker`类，为什么`Speaker`类的`speak`方法被调用？答案是`SingingBird`调用其基类的第一个`speak`方法。如果一个方法由两个或更多的超类不同地实现，那么应该保持类方法的顺序。

早期类中的方法会覆盖后期类中的方法。因此，如果我们像创建`class SingingBird(Bird, Speaker)`一样创建`SingingBird`，它将覆盖(使鸟静音！)的`Speaker`类的`speak`方法。像这样颠倒它们的顺序:`class SingingBird(Speaker, Bird)`将使`Speaker`类的`speak`方法可访问。

所有代码都可以在[这个要点](https://gist.github.com/arsho/59a2cb4f08cffa175738f38f1c85d912)中找到。

感谢[大卫·格里芬](https://dev.to/habilain)给了改进这篇文章的重要建议。

[![Inheritance](img/1f254acb89e79659854947adda435631.png "Inheritance")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Vqi8GHkH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j99r5frux1gn4ey6muc1.png)