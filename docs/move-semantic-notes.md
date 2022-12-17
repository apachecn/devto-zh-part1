# 移动语义注释

> 原文：<https://dev.to/bobfang1992/move-semantic-notes>

来自第 23 项:

*   `std::move`将一个值(不管是哪种值)强制转换为右值。一个近似的实现是:

```
//C++ 14
template<typename T>
decltype(auto) move(T&& param)
//C++ 11
template<typename T>
typename remove_reference<T>::type&&
{
    using ReturnType = remove_reference_t(T)&&;
    return static_cast<ReturnType>(param);
} 
```

Enter fullscreen mode Exit fullscreen mode

*   如果要移动什么东西，不要声明为`const`。想想字符串和向量是怎么移动的。

*   填补这里关于`std::forward`如何实际工作的空白，但是从概念上来说，它也只是一个强制转换，一个有条件的强制转换。

来自第 24 项:

*   `universal/forwarding references`的两个例子:

```
template<typename T>
void f(T&& param); // param is universal reference

auto&& var2 = var1; // var2 is universal reference 
```

Enter fullscreen mode Exit fullscreen mode

通用引用来自类型演绎(见上面的例子)。

为什么我们需要知道这些？->这时不确定。

来自第 25 项:

*在右值引用上使用 std::move，在通用引用上使用 STD::forward*

第一个让我感到惊讶的例子是:

```
class Widget {
    public:
    Widget(Widget&& rhs)
    : name (std::move(rhs.name)),
      p(std::move(rhs.p)
    { ... }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

那么为什么我们需要用`std::move`来代表`rhs.name`？这是因为`rhs`实际上是一个左值，而不是右值。见我的 [SO 问题](https://stackoverflow.com/questions/45520884/why-do-we-need-stdmove-in-constructors-initialisation-list)这里有更详细的解释。

这篇文章实际上解释了当你写代码时应用的一些规则。让我来为你解释一下:

1.  分别用`std::move`表示右值引用，用`std::forward`表示通用引用，如果在你的函数中引用有多种用法，你可能只想用`std::move`和`std::forward`表示最后一种用法

2.  如果你正在返回一个作为函数参数传递的右值引用，上面的规则仍然适用，你必须使用`std::move`或`std::forward`来达到最优

3.  但是如果你返回一个本地对象，不要使用`std::move`,因为那会使编译器的 RVO(返回值优化)失效。

从第 26 和 27 项
*避免通用引用上的重载*

这是因为当编译器试图解决重载函数时，通用引用上的重载可能会占据很高的优先级，从而导致混乱。对于构造函数来说尤其如此。但是有时我们无法避免这一点，尤其是对于构造函数，因为我们必须重载它们。在第 27 项中，Scott 向我们介绍了一些解决这个问题的技巧。我认为最重要的是使用标签调度。

那么什么是标签调度呢？

<.... revisit="" item="" and="" later="" ...=""/>

项目 28

这一条只陈述了两条规则:

1.  虽然程序员不能声明“引用对引用”，但允许编译器在一定的上下文中生成“引用对引用”。

2.  当我们对引用进行引用时，一个称为引用折叠的规则开始起作用，简而言之，它规定:

> 如果任一引用为左值引用，则结果为左值引用。否则，它是一个右值引用。