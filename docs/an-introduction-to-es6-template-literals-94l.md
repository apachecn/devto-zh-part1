# ES6 模板文字简介

> 原文：<https://dev.to/sarah_chima/an-introduction-to-es6-template-literals-94l>

还记得当您必须在一个字符串中插入几个变量时，JavaScript 中的编码有多糟糕吗？哦，你不得不无数次地写引号`''`和`+`符号。然后，您运行代码时才意识到，您没有正确地将变量与主文本隔开，或者您忘记了在变量的末尾添加一个句号，以使它看起来像一个正确的句子。你需要见见我的朋友，模板文字。它会让你忘记这一切。好了，别闹了..lol。现在让我们严肃起来。

模板文字是允许嵌入表达式的字符串文字。如果这听起来令人困惑，不要担心，你很快就会明白的。我们在使用字符串时会遇到几个问题。我们可能想要插入一个字符串，或者包含嵌入式表达式，或者使用多行字符串。所有这些都可以使用 ES6 之前的 JavaScript 版本来完成。然而，模板文字使这一点变得非常容易，而且是以一种简洁优雅的方式。我们将在本文中看到这是如何实现的。在我的下一篇文章中，我们将讨论带标签的模板文字。首先，我们应该知道一些关于模板字面量的事情。

1.  模板文字只是另一种编写字符串的方式，它利用反斜线代替单引号`''`或双引号`""`。在 ES6 之前，它被称为模板字符串。

2.  为了在模板文字中嵌入表达式(占位符)，我们用美元符号和花括号将它们括起来，就像这样`${expression}`。

3.  反勾号中的所有内容都被传递给一个默认函数，该函数连接成一个字符串，除非指定了函数。

我们现在都准备好继续了。

**字符串插值**
这就是文章开头提到的问题。让我们提醒自己痛苦。

```
 let name = "Sarah";
    let job = "Developer";
    let tools = "JavaScript and CSS";

    console.log("My name is " + name + " and I am a " + job + ". I write " + tools + "." );//My name is Sarah and I am a Developer. I write JavaScript and CSS." 
```

Enter fullscreen mode Exit fullscreen mode

使用 ES6 的模板文字，我们将这样做。

```
 let name = "Sarah";
    let job = "Developer";
    let tools = "JavaScript and CSS";
    console.log(`My name is ${name} and I am a ${job} . I write ${tools}.`);//My name is Sarah and I am a Developer. I write JavaScript and CSS." 
```

Enter fullscreen mode Exit fullscreen mode

看看这有多简单。注意，使用了反勾号而不是引号，变量都出现在美元符号和花括号中。让我们用这个做更多的事情。

让我们使用模板文字嵌入一个数学表达式。

```
 console.log(`Jessy has ${5+3} cats and ${3+2} dogs.`)//"Jessy has 8 cats and 5 dogs." 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以嵌入一个函数。

```
 let sayHello = () => "Hello";

   console.log(`${sayHello()}, it's the code girl next door.`);//"Hello, it's the code girl next door." 
```

Enter fullscreen mode Exit fullscreen mode

为了好玩，我们再做一个。

```
 let sayName = () => "Template Literal";
   let num = 5;

   console.log(`I am a ${sayName()} and I am ${num *2}x more powerful.`);//"I am a Template Literal and I am 10x more powerful." 
```

Enter fullscreen mode Exit fullscreen mode

**多行字符串**

还记得字符串中的`\n`是干什么用的吗？如果你说移到新的一行...你是对的。如果你说了**只有**的方式才能移动到新的一行...嗯，你说的不对。模板文本允许您通过不使用任何字符直接移动到新行来移动到新行。所以不这样做:

```
 console.log("I am the first line \n, I am the second line \n And I am the third line."); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以简单地这样做:

```
 console.log(`I am the first line,
 I am the second line
 And I am the third line.`); 
```

Enter fullscreen mode Exit fullscreen mode

得到同样的结果。

```
 "I am the first line,
    I am the second line
    And I am the third line." 
```

Enter fullscreen mode Exit fullscreen mode

超级爽！！！

让我们用一个例子来看看所有这些在哪里有用。我们希望使用 Javascript 向页面添加 HTML 标记。模板文字使得以一种非常简洁的方式做到这一点成为可能。看看下面的例子。

```
 const animal = {
        kind: "Monkey",
        food: "bananas", 
        hobby: "jumping trees"
    };

   const display = `
        <div class = "animal">
            <h1>Hey, this is a ${animal.kind}</h1>
            <p>It eats ${animal.food} and loves ${animal.hobby}</p>
        </div>
    `;
    document.body.innerHTML = display; 
```

Enter fullscreen mode Exit fullscreen mode

看看使用模板文字可以做到多么简单和整洁。酷吧？我的下一篇文章将讨论带标签的模板文字。敬请关注。

有什么问题或补充吗？留下评论。
感谢您的阅读。:)