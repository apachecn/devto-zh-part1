# 函数参数应该是可重赋值的还是可变的？

> 原文：<https://dev.to/mortoray/should-function-arguments-be-reassignable-or-mutable-4np6>

在处理 [Leaf](http://leaflang.org) 中的一个缺陷时，我有一个问题:函数中的函数参数应该重新赋值吗？它们就像局部变量一样，还是应该被特殊对待？它可以解决我在 Leaf 编译器中的问题，但是我不喜欢为了技术上的方便而做决定。正确答案是什么？

> 这是一个开放式问题，我希望听到您的反馈。这篇文章是细节和我的观点，但我没有得出结论。

## 命令式的方法

在植根于[命令式编程](https://mortoray.com/2017/05/10/what-is-imperative-programming/)的语言中，像 C、C++、Java、C#等，我们可以自由地使用函数参数作为局部变量。

```
int calc( int a, int b ) {
    a += b
    b += a
    return a
} 
```

Enter fullscreen mode Exit fullscreen mode

我承认我经常写这样的代码。重用现有变量比引入新变量更方便。我意识到它以前也给我惹过麻烦。当一段代码修改了参数，但是函数后面的代码没有预料到这一点。

```
int calc( float a, int b ) {
    float result = a;

    if (some_conditon_on(a)) {
        b /= 5;
        result += b;
    }

    if (some_condition_on(b)) {
        result = alt_calc(a,b);
    }

    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然是人为的，但它表明函数中的第二部分代码可能依赖于未修改的参数。这是一个微妙的缺陷，因为它需要两个条件都计算为真。再加上更多的分支，这些分支可能会也可能不会修改论点，问题就变得更严重了。

在 JavaScript 中，情况更糟。如果我修改一个命名参数，它也会修改`arguments`数组。

```
function hidden_arg( name ) {
    name = "weird"
    console.log(arguments[0])
}

hidden_arg("expected") 
```

Enter fullscreen mode Exit fullscreen mode

那写的是`weird`，不是`expected`。

## 功能方法

如果我们看看像 Haskell 这样的语言，我们会发现，通常情况下，重新分配变量是不被允许的(这可能吗？).虽然这不是函数式编程的基础，但是函数 a 是否重新分配参数并不影响函数的纯度。

然而，函数可以修改参数的值，这肯定会破坏不可变的需求。

这让我想到，也许这个要求应该更进一步:默认情况下，参数也应该是只读的。考虑下面的代码，其中的“值”名称是不可重新赋值的(C 和 C++是少数几种可以使用这种符号的语言):

```
//this prevents reassigning the "values" pointer...
float calc( vector<float> * const values ) {
    values[0] = 1; //...but we can still modify the values
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如果默认设置也是只读的呢？*(这是典型的 C++语法)*

```
float calc( vector<float> const & values ) {
    values[0] = 1; //error!
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数有一个更安全的签名。我可以调用它，而不用担心我的向量可能会在我身上被意外改变。

> 我想这个讨论不可避免地要深入到值的名称和值之间的差异。

## 默认，但不是硬性要求

我开始认为不可重分配和只读应该是默认的。如果我想要一个可变参数，我可以标记它。

```
float sort( vector<float> mutable & values ) 
```

Enter fullscreen mode Exit fullscreen mode

对于复杂的值类型，这很有意义。但是对于服务类型，比如文件或窗口句柄，这就不方便了。至少在 Leaf 中，我有一个独特的`service`类型，默认情况下它是可变的。我不喜欢前后矛盾，但有时候为了方便不得不牺牲。

另一个让我犹豫的情况是争论净化。例如:

```
float calc( float a, float b ) {
    if (b < 0) {
        a = -a;
        b = -b;
    }

    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们不希望函数的其余部分访问原始参数。它们被故意藏起来了。清理争论可能并不常见，但我经常这样做，所以我需要一个解决方案。也许通过同名的局部变量隐藏参数可能会有效。

## 你的想法？

我不知道正确的解决方法是什么。目前的语言和最佳实践似乎还没有给出明确的答案。这使得它成为语言设计中引人入胜的话题之一。

这是我写 Leaf 的冒险的一部分。我也很乐意听听你对这个话题的想法。