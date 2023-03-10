# 美食，酒吧，丑陋

> 原文：<https://dev.to/johannesvollmer/the-foo-the-bar-the-ugly-5c71>

*[图片来源:cinetropolis.net](http://cinetropolis.net/scene-is-believing-the-good-the-bad-and-the-ugly/)T3】*

当我学习我的第一门编程语言 Java 时，我只是把我的问题扔进了谷歌。web 提供了大量有用的指南，当然我们都知道 StackOverflow，但是其中很多都有一个小问题。

## 代码示例

不可否认，举例是最有效的学习方法之一。它们对于任何编程技巧、教程或指南都是必不可少的。格拉迪，大多数导游都有。

## 具体来说:不良代码示例

不幸的是，有人曾经认为使用没有任何意义的完全虚构的标识符来编写代码示例是一个好主意；好的、坏的、丑陋的:`Foo`、`Bar`和`Baz`。

> 它们被用来命名实体，如变量、函数和命令，它们的确切身份并不重要，只是用来演示一个概念。 *[维基百科](https://en.wikipedia.org/w/index.php?title=Foobar&oldid=804411222)*

## 这些词为什么不好？

让我们来看一个例子(使用这些关键字的例子)。OMG 好 meta！这个例子演示了 Java 中的**接口**是如何工作的:

```
interface Foo {
    void foo();
}

class Bar implements Foo {
    @Override 
    public void foo(){
        System.out.println("Bar");
    }
}

class Baz implements Foo {
    @Override 
    public void foo(){
        System.out.println("Baz");
    }    
}

// ... later
void baz(Foo foo){
    foo.foo();
} 
```

Enter fullscreen mode Exit fullscreen mode

承认吧:你没有*真的*读过这个例子。就是读起来不好玩。类、接口和方法之间的关系只能通过仔细阅读源代码来学习，甚至可能只有在已经对接口有一点了解的情况下。

示例的一个关键属性是它们可以与。因此，选择一个虚构的、无意义的词违背了“例子”的定义。通过使用`Foo`，你无法提供上下文。

## 我们该怎么办？

这个接口的例子不是更容易理解吗:

```
interface SomethingThatPurrs {
    void purr();
}

class Cat implements SomethingThatPurrs {
    @Override 
    public void purr(){
        System.out.println("*purring cat*");
    }
}

class Kitten implements SomethingThatPurrs {
    @Override 
    public void purr(){
        System.out.println("*purring kitten*");
    }    
}

// ... later
void makeThatThingPurrSomehow(SomethingThatPurrs purring){
    purring.purr();
} 
```

Enter fullscreen mode Exit fullscreen mode

猫和小猫创造了一个环境。突然间，很容易看出它们都会发出呼噜声，以及呼噜声和它们之间的联系。那是因为你知道猫和小猫都会发出呼噜声。

我见过初学者不知道`Foo`和`Bar`实际上是什么意思，直到有人告诉他们没有隐藏的意思。有些人甚至认为 Foo 和 Bar 很有趣，这反过来促使教师使用这些名称构建示例。

在我看来，当试图解释一个概念时，一个人应该尽一切可能来帮助学习者。通过使用特定的名称和众所周知的概念来提供上下文很有帮助。

## 结论

*   Foo 和 Bar 不提供上下文
*   例子应该提供尽可能多的背景
*   Foo 和 Bar 没有创意，也很无聊
*   小猫很可爱，也很鼓舞人心

*`Foo``Bar`据说起源于军事缩略语“FUBAR”，扩展为“搞得面目全非”。真巧。*