# 循环的类型

> 原文：<https://dev.to/tmr232/types-of-loops-1n55>

最近我一直在帮助和辅导一些真正的代码初学者。不是语言新手，而是编程新手。

我过去做过很多训练。初级和高级培训，编程和逆向工程。但是，尽管我以前的学生是新手，他们总是有一些先验知识，一些代码方面的经验。至少用一种编程语言。

通常，培训是关于教授语言特性、特殊技巧、最佳实践，以及让受训者熟悉新模式。培训生发出探针，寻找熟悉的东西，我只是在合适的时候补上。他们知道自己在寻找什么，或者很容易被引导到正确的方向。当人们完全绿色的时候，他们不会。

这对我来说是一种全新的体验，它让我思考了很多关于编程和我们处理代码的方式。我们试图寻找或形成的模式。作为经验丰富的程序员，我们甚至不用思考就能做的事情多得惊人。每一个，不管多么简单，都需要分解并向新来者解释。他们以前没有这方面的知识。

从我目前的经验来看，理解语法的含义和理解向前流动的程序是很容易的。条件句不是问题。第一个路障带有环路。尤其是写循环。我把退货单放在哪里？我在哪里定义变量？试图解释这些事情，并给出简单的规则，我得到了一些有用模式的有用实现，以及一些关于我们使用行话的痛苦事实。

让我们继续看模式。

## 寻找循环

```
public static int indexOf(String[] haystack, String needle) {
    for (int i = 0; i < haystack.length; ++i) {
        if (needle.equals(haystack[i])) {
            return i;
        }
    }

    return -1;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这些循环中，我们迭代数组，寻找满足我们条件的项。一旦我们找到它，我们立即返回该值。不需要声明任何变量。

## 计数循环

```
public static int countOf(String[] haystack, String needle) {
    int count = 0;
    for (int i = 0; i < haystack.length; ++i) {
        if (needle.equals(haystack[i])) {
            count++;
        }
    }

    return count;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这些循环中，我们迭代数组，寻找满足我们条件的项目。每当我们找到一个，我们就增加计数。一旦我们用完了迭代，我们就返回计数器。计数器和 return 语句在循环之外。

## 动作循环

```
public static void printAll(String[] haystack) {
    for (int i = 0; i < haystack.length; ++i) {
        System.out.println(haystack[i]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这些循环中，我们遍历数组，并对每一项执行操作。没有变量，也没有返回语句。

现在，那些简单的循环可以做很多事情，并且可以扩展和组合来做更多事情。我发现它们对初学者有帮助。但是你发现我的错误了吗？我用了“迭代”这个词。

## 词汇

虽然“iterate”的意思对于现有的程序员来说很清楚，并且通过查看循环，你可以很容易地判断出我们正在对`haystack`进行迭代或循环，但是对于初学者来说并不清楚。而且，这些词本身听起来就很怪异。这一点很关键，当你尝试以稍微高级一点的方式循环时，这一点变得更加明显

```
int i = 0;
for (Node current = myList.head; current != null; current = current.getNext(), ++i) {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们在`myList`上循环(或迭代),但是我们没有对它做任何改变，甚至没有直接访问它。我们确实改变了`i`(它不再是我们的计数器)和`current`(它是一个节点)。这使得代码和语言相当不协调。“我们在维护索引的同时迭代`myList`”是正确的说法，但不是代码的直接翻译。语言迫使以一种非常迂回的方式进行。许多语言都是如此。
但是现在，考虑稍微现代一点的语法:

```
for i, node in enumerate(myList):
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

```
for i, node := range myList {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
for (i, node) in myList.enumerate() {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在所有这些方面，情况都要清楚得多。我们可以看到我们正在循环遍历`myList`，很明显我们既有一个节点又有一个索引。对于有经验的程序员来说，这种差异可能很小，但对于新手来说，这是一个完全不同的世界。

学习编码不仅仅是学习一门编程语言。不仅仅是学习用特定的方式思考。它是重新学习你自己的语言。你懂英语吗？好了，现在你需要学习编程——英语。你懂希伯来语吗？学编程——希伯来语。我们不断改变现有词汇的含义，并期望人们能够理解和理解它们。这很难。我们至少可以尽量减少我们所读代码之间的差异(编程语言——Java、C、C++、Python、Go、Rust...)和我们说的代码(嗯，我猜英语也是一种编程语言？).