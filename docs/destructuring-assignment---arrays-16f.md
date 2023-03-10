# ES6 数组中的析构赋值

> 原文：<https://dev.to/sarah_chima/destructuring-assignment---arrays-16f>

析构赋值是 ES6 附带的一个很酷的特性。析构是一个 JavaScript 表达式，它可以将数组中的值或对象中的属性解包到不同的变量中。也就是说，我们可以从数组和对象中提取数据，并将它们赋给变量。为什么这是必要的？

想象一下，如果我们想从一个数组中提取数据。以前，这将如何进行？

```
 var introduction = ["Hello", "I" , "am", "Sarah"];
    var greeting = introduction[0];
    var name = introduction[3];

    console.log(greeting);//"Hello"
    console.log(name);//"Sarah" 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，当我们想从一个数组中提取数据时，我们不得不一遍又一遍地做同样的事情。ES6 去结构赋值使得提取这些数据更加容易。这是怎么回事？本文讨论数组的析构赋值。我的下一篇文章将讨论对象。让我们开始吧。

**基本解构**

如果我们想使用数组提取数据，使用析构赋值是非常简单的。让我们参考第一个数组的例子。我们将这样做，而不是重复这个过程。

```
 var introduction = ["Hello", "I" , "am", "Sarah"];
    var [greeting, pronoun] = introduction;

    console.log(greeting);//"Hello"
    console.log(pronoun);//"I" 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以用同样的结果来做这件事。

```
 var [greeting, pronoun] = ["Hello", "I" , "am", "Sarah"];

    console.log(greeting);//"Hello"
    console.log(pronoun);//"I" 
```

Enter fullscreen mode Exit fullscreen mode

**赋值前声明变量**
变量可以这样在赋值前声明。

```
 var greeting, pronoun;
    [greeting, pronoun] = ["Hello", "I" , "am", "Sarah"];

    console.log(greeting);//"Hello"
    console.log(pronoun);//"I" 
```

Enter fullscreen mode Exit fullscreen mode

请注意，变量是从左到右设置的。所以第一个变量得到数组中的第一项，第二个变量得到数组中的第二个变量，依此类推。

**跳过数组中的项目**

如果我们想得到数组中的第一个和最后一个项，而不是第一个和第二个项，并且我们只想给两个变量赋值，那该怎么办呢？这也可以做到。看看下面的例子。

```
 var [greeting,,,name] = ["Hello", "I" , "am", "Sarah"];

    console.log(greeting);//"Hello"
    console.log(name);//"Sarah" 
```

Enter fullscreen mode Exit fullscreen mode

刚刚发生了什么？请看变量赋值左边的数组。注意，不是只有一个逗号，而是有三个。逗号分隔符用于跳过数组中的值。所以如果你想跳过一个数组中的一项，只需使用逗号。

我们再做一个。我觉得挺好玩的。让我们跳过单子上的第一项和第三项。我们将如何做到这一点？

```
 var [,pronoun,,name] = ["Hello", "I" , "am", "Sarah"];

    console.log(pronoun);//"I"
    console.log(name);//"Sarah" 
```

Enter fullscreen mode Exit fullscreen mode

所以逗号分隔符发挥了神奇的作用。因此，如果我们想跳过所有项目，我们只需这样做。

```
 var [,,,,] = ["Hello", "I" , "am", "Sarah"]; 
```

Enter fullscreen mode Exit fullscreen mode

**分配数组的剩余部分**

如果我们想将数组的一部分赋给变量，而将数组中的其余项赋给一个特定的变量呢？我们会做到这一点。

```
 var [greeting,...intro] = ["Hello", "I" , "am", "Sarah"];

    console.log(greeting);//"Hello"
    console.log(intro);//["I", "am", "Sarah"] 
```

Enter fullscreen mode Exit fullscreen mode

使用这种模式，您可以将数组的剩余部分解包并赋给一个变量。

**用函数析构赋值**
我们也可以从函数返回的数组中提取数据。假设我们有一个函数返回一个数组，如下例所示。

```
 function getArray() {
        return ["Hello", "I" , "am", "Sarah"];
    } 
    var[greeting,pronoun] = getArray();

    console.log(greeting);//"Hello"
    console.log(pronoun);//"I" 
```

Enter fullscreen mode Exit fullscreen mode

我们得到同样的结果。

**使用默认值**
默认值可以分配给变量，以防从数组中提取的值是`undefined`。

```
 var[greeting = "hi",name = "Sarah"] = ["hello"];

    console.log(greeting);//"Hello"
    console.log(name);//"Sarah" 
```

Enter fullscreen mode Exit fullscreen mode

所以`name`回退到“Sarah ”,因为它没有在数组中定义。

**使用析构赋值交换值**
还有一件事。我们可以使用析构赋值来交换变量的值。

```
 var a = 3;
    var b = 6;

    [a,b] = [b,a];

    console.log(a);//6
    console.log(b);//3 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我将写对象析构。

有什么问题或补充吗？请留言评论。

感谢您的阅读:)