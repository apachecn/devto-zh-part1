# 那个“重载”技巧:在 C++17 中重载 Lambdas

> 原文：<https://dev.to/tmr232/that-overloaded-trick-overloading-lambdas-in-c17>

C++17 已经授予我们 [`std::variant`](http://en.cppreference.com/w/cpp/utility/variant) 。简单来说就是类型安全的`union`。要访问它存储的值，您可以请求一个特定的类型(使用 [`std::get`](http://en.cppreference.com/w/cpp/utility/variant/get) 或类似的东西)或“访问”变量，只自动处理实际存在的数据类型。
拜访是用 [`std::visit`](http://en.cppreference.com/w/cpp/utility/variant/visit) 完成的，相当直截了当。

[编译](https://godbolt.org/g/UhTBtD)，[执行](http://coliru.stacked-crooked.com/a/ce02024f6539db91)，

```
#include <variant> #include <cstdio> #include <vector>  
using var_t = std::variant<int, const char*>; // (1)

struct Print { // (2)
    void operator() (int i) {
        printf("%d\n", i);
    }

    void operator () (const char* str) {
        puts(str);
    }
};

int main() {
    std::vector<var_t> vars = {1, 2, "Hello, World!"}; // (3)

    for (auto& v : vars) {
        std::visit(Print{}, v); // (4)
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们定义了我们的变体类型。在(2)中，我们定义了一个重载`operator()`的类。这是调用`std::visit`所需要的。在(3)中，我们定义了一个变量向量。在(4)中，我们访问每个变体。我们传入了一个`Print`的实例，重载决策确保了对每种类型都会调用正确的重载。
但是这个例子迫使我们为重载的`operator()`编写并命名一个对象。我们可以做得更好。事实上， [cppreference](http://en.cppreference.com/w/cpp/utility/variant/visit) 上的`std::visit`的例子已经存在了。下面是由此衍生的一个例子:

[编译](https://godbolt.org/g/8gAj3y)，[执行](http://coliru.stacked-crooked.com/a/2edc87064e156115)，

```
#include <variant> #include <cstdio> #include <vector>  
template<class... Ts> struct overloaded : Ts... { using Ts::operator()...; }; // (1)
template<class... Ts> overloaded(Ts...) -> overloaded<Ts...>;  // (2)

using var_t = std::variant<int, const char*>;

int main() {
    std::vector<var_t> vars = {1, 2, "Hello, World!"};

    for (auto& v : vars) {
        std::visit(overloaded {  // (3)
             { printf("%d\n", i); },
             { puts(str); }
        }, v);
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这当然更紧凑，我们移除了`Print`结构。但是它是如何工作的呢？您可以看到一个 class-template (1)，lambdas 作为构造的参数传入(3)，还有一个带箭头的东西和一些模板魔术(2)。让我们一步一步来建造它。

首先，我们想将打印功能从`Print`中分离出来，稍后再组合它们。

[编译](https://godbolt.org/g/ZuyYrD)，[执行](http://coliru.stacked-crooked.com/a/4e90f3c2ae1a5113)，

```
struct PrintInt { //(1)
    void operator() (int i) {
        printf("%d\n", i);
    }
};

struct PrintCString { // (2)
    void operator () (const char* str) {
        puts(str);
    }
};

struct Print : PrintInt, PrintCString { // (3)
    using PrintInt::operator();
    using PrintCString::operator();
}; 
```

Enter fullscreen mode Exit fullscreen mode

在(1)和(2)中，我们像以前一样定义相同的操作符，但是在不同的结构中。在(3)中，我们从这两个结构中继承，然后显式地使用它们的`operator()`。这导致了与之前完全相同的结果。接下来，我们将`Print`转换成一个类模板。我将直接把它转换成变量模板。

[编译](https://godbolt.org/g/iXGT2p)，[执行](http://coliru.stacked-crooked.com/a/3e9902784fec5791)，

```
template <class... Ts> // (1)
struct Print : Ts... {
    using Ts::operator()...;
};

int main() {
    std::vector<var_t> vars = {1, 2, "Hello, World!"};

    for (auto& v : vars) {
        std::visit(Print<PrintCString, PrintInt>{}, v); // (2)
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们定义了模板。我们获取任意数量的类，从它们继承，并使用它们的`operator()`。在(2)中，我们用`PrintCString`和`PrintInt`实例化了`Print`类模板，以获得它们的功能。
接下来，我们想用 [lambdas](http://en.cppreference.com/w/cpp/language/lambda) 做同样的事情。这是可能的，因为 lambdas 不是函数；他们是实现`operator()`的对象。

[编译](https://godbolt.org/g/bFzYUP)，[执行](http://coliru.stacked-crooked.com/a/ed95f8db6c30562e)，

```
int main() {
    std::vector<var_t> vars = {1, 2, "Hello, World!"};
    auto PrintInt =  { printf("%d\n", i); }; // (1)
    auto PrintCString =  { puts(str); };

    for (auto& v : vars) {
        std::visit(
            Print<decltype(PrintCString), decltype(PrintInt)>{PrintCString, PrintInt}, // (2)
            v);
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们定义了我们需要的 lambdas。在(2)中，我们用 lambdas 实例化模板。这是丑陋的。由于 lambdas 有独特的类型，我们需要在使用它们作为模板参数之前定义它们(使用`decltype`推断它们的类型)。然后，我们需要传递 lambda 作为[聚合初始化](http://en.cppreference.com/w/cpp/language/aggregate_initialization)的参数，因为 lambda 有一个删除默认构造函数。我们很接近了，但还没到那一步。
`<decltype(PrintCString), decltype(PrintInt)>`部分确实很丑，导致重复。但是它是必需的，因为构造函数不能进行类型演绎。所以在适当的 C++风格中，我们将创建一个函数来绕过它。

[编译](https://godbolt.org/g/S3XGZr)，[执行](http://coliru.stacked-crooked.com/a/8f373b382ed73d3b)，

```
template <class... Ts> // (1)
auto MakePrint(Ts... ts) {
    return Print<Ts...>{ts...};
}

int main() {
    std::vector<var_t> vars = {1, 2, "Hello, World!"};

    for (auto& v : vars) {
        std::visit(
            MakePrint( // (2)
                 { puts(str); },
                 { printf("%d\n", i); }
                ),
            v);
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们定义了我们的助手函数，来执行类型推导并将其转发给 ctor。在(2)中，我们利用新发现的类型演绎来内联定义 lambdas。但这是 C++17，我们可以做得更好。

C++17 增加了[自定义推演指南](http://en.cppreference.com/w/cpp/language/class_template_argument_deduction)。这些允许我们指示编译器执行与我们的 helper 函数相同的操作，但是不需要添加另一个函数。使用合适的演绎指南，代码如下。

[编译](https://godbolt.org/g/ZZXM6L)，[执行](http://coliru.stacked-crooked.com/a/1bbf8c7cccedd3d8)，

```
#include <variant> #include <cstdio> #include <vector>  
using var_t = std::variant<int, const char*>;

template <class... Ts>
struct Print : Ts... {
    using Ts::operator()...;
};

template <class...Ts> Print(Ts...) -> Print<Ts...>; // (1)

int main() {
    std::vector<var_t> vars = {1, 2, "Hello, World!"};

    for (auto& v : vars) {
        std::visit(
            Print{ // (2)
                 { puts(str); },
                 { printf("%d\n", i); }
            },
            v);
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们定义了一个演绎指南，作为我们之前的帮助函数，在(2)中，我们使用构造函数而不是帮助函数。完成了。

现在我们已经完全重现了最初的例子。由于`Print`不再表示模板类的行为，`overloaded`可能是一个更好的名字。

```
template<class... Ts> struct overloaded : Ts... { using Ts::operator()...; };
template<class... Ts> overloaded(Ts...) -> overloaded<Ts...>; 
```

Enter fullscreen mode Exit fullscreen mode