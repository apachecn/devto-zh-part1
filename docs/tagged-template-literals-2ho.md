# ES6 中标记的模板文字

> 原文：<https://dev.to/sarah_chima/tagged-template-literals-2ho>

在我的上一篇文章中，讨论了[模板文字](https://dev.to/sarah_chima/an-introduction-to-es6-template-literals-94l)。如果您不知道什么是模板文字，您应该阅读它。我们看到，当使用模板文字时，字符串被传递给默认函数，该函数解释字符串并返回结果。嗯，事情不一定总是这样。我们可以控制使用带标签的模板文字从字符串中得到什么结果。

标记模板文字使我们能够标记字符串。这意味着我们可以通过自己定义的函数来传递字符串，而不是让浏览器使用默认函数来解释字符串。这让我们可以控制琴弦的制作方式。在这一点上，一个例子将是有帮助的。

```
 function doSomething(string) {

    }
    const name = "Kelvin";
    const food = "Rice";
    const sentence = doSomething`My name is ${name} and I love eating ${food}`; 
```

Enter fullscreen mode Exit fullscreen mode

从这个例子中，我们可以看到我们创建了一个函数，然后用它来标记字符串。也就是说，将函数放在字符串的前面。注意没有使用括号。这个函数定义了字符串的内容。在我们的例子中，`doSomething`决定了`sentence`将会是什么。因此，如果我们定义函数来做类似返回一个不同的字符串的事情，这就是`sentence`将会变成的样子。看看下面的例子。

```
 function doSomething() {
        return "Hello";
    }

    const name = "Kelvin";
    const food = "Rice";

    const sentence = doSomething`My name is ${name} and I love eating ${food}`;
    console.log(sentence);// "Hello" 
```

Enter fullscreen mode Exit fullscreen mode

让我们来理解当我们使用一个带标签的模板文字时会发生什么。当该函数运行时，关于该字符串的所有信息都被提供给该函数。这包括输入的所有字符串片段以及传入的表达式/变量。这些插值表达式被求值并传递给标签。字符串片段作为数组传递给函数，然后所有其他变量作为单独的参数传递给函数。例如，在上面的例子中，实际传递给函数的是:

```
 function doSomething(strings, name, food) {
        return "Hello";
    } 
```

Enter fullscreen mode Exit fullscreen mode

其中`strings`是模板中所有字符串的数组，`name`和`food`是模板中的变量。该函数确定如何处理这些信息以及它将产生的结果。
当我们向函数传递了这么多参数，而你想要检索它们时，会发生什么呢？我们可以使用 ES6 rest 操作符来检索它们。这会将所有其他参数放在一个数组中。它的语法就是你选择的任何变量的名字加上像这样的`...`。

```
 function doSomething(strings,...rest) {
        return "Hello";
    } 
```

Enter fullscreen mode Exit fullscreen mode

`rest`是一个数组，包含传递给标签的所有变量和表达式，即除主字符串之外的所有内容。在我们的例子中，它包含了`[name, food]`。

在上面的例子中，我们只是告诉函数返回`hello`,但这绝对不是我们想要对字符串做的。让我们做一些更实际的事情。我们希望突出显示传递给字符串的变量，以便它们在屏幕上以粗体显示。我们能做到的。

```
 function highlight(strings, ...rest) {
        let str = "";

        strings.forEach((string, i) => {
            if (rest[i] != undefined){
                str += `${string} <b>${rest[i]}</b>`;
            } else {
                str += `${string}`;
          }
        });

        return str;
    }

    const name = "Kelvin";
    const food = "Rice";
    const sentence = highlight`My name is ${name} and I love eating ${food}, cool right?`;

    document.body.innerHTML = sentence; 
```

Enter fullscreen mode Exit fullscreen mode

所以我们的函数`highlight`使用模板文字来连接字符串和变量。这里的`if else`语句是为了确保`undefined`没有被添加到字符串中，在最后一个字符串之后没有变量。

这只是一个简单的例子，展示了如何使用带标签的模板文字。带标签的模板文字确实是 ES6 的一个强大特性。它赋予你控制琴弦的力量。你认为用这个还能做什么？请留言评论。

有什么问题或补充吗？留下评论。

感谢您的阅读:)