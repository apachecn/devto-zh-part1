# C++中的函数式状态机，第 2 部分

> 原文：<https://dev.to/tmr232/a-functional-style-state-machine-in-c-part-2>

首先，道歉。本文的第一部分发表于 5 月 26 日。现在是九月。那时我已经完成了这部分的大部分代码。但是最终完成代码需要更多的努力。一旦完成，解释需要一段时间。有相当多的东西我必须自己先学会。所以现在，几个月后，我向 C++和模板元编程之神献上这份谦卑的礼物。

* * *

## 泛指ðÿœˆ

在[第 1 部分](https://dev.to/tmr232/a-functional-style-state-machine-in-c)中，我们创建了我们的`State`或`SelfReturning`类(下面提供供参考)。它可以工作，但是正如你所看到的——每当我们改变函数参数或返回类型时都需要修改。

[编译](https://godbolt.org/g/1XqEhY)

```
struct SelfReturning {
    using RetType = std::pair<SelfReturning, const Context>;
    using FuncType = RetType(*)(const Context&, Event);

    SelfReturning(FuncType func) : _func{func} {};
    RetType operator()(const Context& ctx, Event evt) { return _func(ctx, evt); }

    FuncType _func;
};

using State = SelfReturning; 
```

Enter fullscreen mode Exit fullscreen mode

我们要做的第一件事就是摆脱这个要求。首先，函数参数。
[编译](https://godbolt.org/g/RNnzcc)T3】

```
template <class... Args>
struct SelfReturning {
    using RetType = std::pair<SelfReturning, const Context>;
    using FuncType = RetType(*)(Args... args);      // (1)

    SelfReturning(FuncType func) : _func{func} {};
    RetType operator()(Args... args) {              // (2)
        return _func(std::forward<Args>(args)...);
    }

    FuncType _func;
};

using State = SelfReturning<const Context&, Event>; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用[可变模板](http://en.cppreference.com/w/cpp/language/parameter_pack)和[完美转发](http://en.cppreference.com/w/cpp/utility/forward)将所有函数参数直接转发给目标函数。你可以看到在(1)和(2)中我们使用了`Args...`而不是常见的`Args&&...`。这是因为类型是由类模板定义的，而不是在函数调用中推导出来的。

在这之后，我们处理返回类型。
这里我们来看另一个递归问题。返回类型`std::pair<SelfReturning, const Context>`依赖于我们的`SelfReturning`类型，`SelfReturning`本身依赖于返回类型。这意味着仅仅传入返回类型是不行的(很像我们最初的返回类型问题)。为了解决这个问题，我们使用了一个[模板——模板参数](http://en.cppreference.com/w/cpp/language/template_parameters)。

[编译](https://godbolt.org/g/DVgRPx)

```
template <template <class T> class Base, class... Args> // (1)
struct SelfReturning {
    using RetType = Base<SelfReturning>; //(2)
    using FuncType = RetType(*)(Args... args);

    SelfReturning(FuncType func) : _func{ func } {}
    RetType operator() (Args... args) {
        return _func(std::forward<Args>(args)...);
    }

    FuncType _func;
};

template <class T>
using PairWithCtx = std::pair<T, const Context>; // (3)

using State = SelfReturning<PairWithCtx, const Context&, Event>; 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们传入返回类型的模板。在(2)中，我们用我们的`SelfReturning`类实例化该类型。正如我们之前看到的，C++允许这种类型的递归，所以我们是安全的。在(3)中，我们将返回类型模板定义为一对，第二个成员是`const Context`。
搞定。

但是如果我们只想返回`SelfReturning`类呢？为此，我们定义了一个新的模板——身份模板。

```
template <class T>
struct identity {
    using type = T;
};

template <class T>
using identity_t = typename identity<T>::type; 
```

Enter fullscreen mode Exit fullscreen mode

我们定义`identity`结构来保存一个类型，并使用`identity_t`别名来直接访问该类型。这看起来有点奇怪，但是 C++不允许我们直接给模板参数起别名。当用一个类型替换`identity_t`模板时，我们又得到了安全类型。使用那个，我们可以直接返回`SelfReturning`。

```
using State = SelfReturning<identity_t>; 
```

Enter fullscreen mode Exit fullscreen mode

不过，就我个人而言，我讨厌明确地写下琐碎的案例。所以还是用点阴招吧。

```
template <template <class T> class Base = identity_t, class... Args> // (1)
struct SelfReturning {
    using RetType = Base<SelfReturning>;
    using FuncType = RetType(*)(Args... args);

    SelfReturning(FuncType func) : _func{ func } {}
    RetType operator() (Args... args) {
        return _func(std::forward<Args>(args)...);
    }

    FuncType _func;

    template <class... AltArgs>
    using WithArgs = SelfReturning<Base, AltArgs...>; // (2)
}; 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们简单地添加`identity_t`作为`Base`的默认参数。这让我们把最简单的情况(返回`SelfReturning`，不带参数)写成`SelfReturning<>`。很好。然而，如果我们将任何东西放入这组模板参数中，它将覆盖`identity_t`。这就是(2)中的代码的用途。我们将`WithArgs`设置为`SelfReturning`,使用它已经拥有的`Base`参数，因此只接受参数的模板参数。现在，我们可以轻松地编写以下所有内容。
[编译](https://godbolt.org/g/xvtKaE)

```
using Trivial = SelfReturning<>;
using InPair = SelfReturning<PairWithCtx>;

using TrivialWithArgs = SelfReturning<>::WithArgs<Context&, Event>;
using InPairWithArgs = SelfReturning<PairWithCtx>::WithArgs<const Context&, Event>;
// Or alternatively
using InPairWithArgs2 = SelfReturning<PairWithCtx, const Context&, Event>; 
```

Enter fullscreen mode Exit fullscreen mode

在第 1 部分中，我承诺泛化`SelfReturning`类并获得一些编译时保证。我们已经完成了我们的泛化目标，所以是时候让一些安全到位了。

## 增加安全ðÿš”

虽然我们使用`switch`语句来区分不同的事件很好也很简洁，但它也有点容易出错。很容易遗漏一个事件(尽管可以使用编译器错误来防止)，或者意外地将一个事件误认为另一个事件。如果我们想在传递事件通知的同时传递信息，后者尤其适用。避免这些错误的一个简单方法是使用函数重载而不是 switch 语句来解决选择。考虑以下

```
#include <cstdio>  
// (1)
enum class EventType {A, B};

void Switch(EventType evt) {
    switch(evt) {
        case EventType::A:
            puts("A");
            return;
        case EventType::B:
            puts("B");
            return;
    }
}

// (2)
struct EventA {};
struct EventB {};

void Overload(EventA) { puts("A"); }
void Overload(EventB) { puts("B"); } 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们使用一个`switch`来辨别事件类型。很容易忘记一个`return`或一个`break`。如果我们传递更多的数据，那么`Switch`的签名可能会变成`void Switch(EventType evt, void* data)`。那绝对不好。在(2)中，我们不能弄错类型，数据可以很容易地在事件结构中传递。遗憾的是，事件没有不同的类型，C++不允许异构容器。是吗？

输入 C++17 '的眼睛`std::variant`的眼睛。

你会问，`std::variant`是什么？嗯，是一辆`union`。一个*安全的*T2！安全意味着你只能从它那里得到一个值，如果它真的存在的话。不再有类型混淆；不再施放`void`指针。但是我们如何从`std::variant`中获取价值呢？当然是用`std::visit`！
[编译](https://godbolt.org/g/EfBahi)，[执行](http://coliru.stacked-crooked.com/a/95c37042037573ca)

```
#include <variant> #include <vector> #include <cstdio>  
struct EventA {}; // (1)
struct EventB {};

struct EventHandler {  // (2)
    void operator() (EventA) { puts("A"); }
    void operator() (EventB) { puts("B"); }
};

using event_t = std::variant<EventA, EventB>;

int main() {
    std::vector<event_t> events = {EventA{}, EventB{}};

    for (auto& event : events) {
        std::visit(EventHandler{}, event); // (3)
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在(1)中，我们定义了新的事件类型。这次他们是不同的类型，不仅仅是价值观不同。在(2)中，我们定义了我们的事件处理器。我们所需要的是为每一种可能的类型提供一个函数重载，一个具有多个`operator()`方法的结构是一种简单的方法。现在剩下要做的就是用我们的处理程序和一个事件调用`std::visit`。如果我们忘记处理一个事件类型——代码[不会编译！](https://godbolt.org/g/Lzu3xV)这样，我们*知道*我们总是处理所有的事件类型，从不混淆。

现在，如果您喜欢上一部分，您可能不喜欢为每个函数编写不同的处理程序类。它完全破坏了代码的局部性。但是，我们用的是 C++17，不是吗？
[编译](https://godbolt.org/g/bqsqE9)，[执行](http://coliru.stacked-crooked.com/a/31074c40d4c9654d)

```
#include <variant> #include <vector> #include <cstdio>  
template<class... Ts> struct overloaded : Ts... { using Ts::operator()...; }; // (1)
template<class... Ts> overloaded(Ts...) -> overloaded<Ts...>;

struct EventA {};
struct EventB {};

using event_t = std::variant<EventA, EventB>;

int main() {
    std::vector<event_t> events = {EventA{}, EventB{}};

    for (auto& event : events) {
        std::visit(overloaded {         // (2)
            [] (EventA) { puts("A"); },
            [] (EventB) { puts("B"); }
        }, event);
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你不熟悉 C++17，这里可能有很多东西需要理解。在(1)中，我们定义了一个接受多个 lambdas 并重载它们的类。在(2)中，我们实例化了那个类来内联我们的事件处理函数。对这段代码的完整解释有点长，所以我写了另一篇文章来解释它。

应用到状态机上，会是这样的:
[编译](https://godbolt.org/g/4VNxEM)，[执行](http://coliru.stacked-crooked.com/a/3bfd9a9220c60cb4)

```
#include <tuple> #include <cstdio> #include <cstdlib> #include <variant>  
template<class... Ts> struct overloaded : Ts... { using Ts::operator()...; };
template<class... Ts> overloaded(Ts...) -> overloaded<Ts...>;

struct EventA {};
struct EventB {};

using Event = std::variant<EventA, EventB>;

struct Context {
    Context Inc() const {
        return Context{counter + 1};
    }
    int counter = 0;
};

template <class T>
struct identity {
    using type = T;
};

template <class T>
using identity_t = typename identity<T>::type;

template <template <class T> class Base = identity_t, class... Args>
struct SelfReturning {
    using RetType = Base<SelfReturning>;
    using FuncType = RetType(*)(Args... args);

    SelfReturning(FuncType func) : _func{ func } {}
    RetType operator() (Args... args) {
        return _func(std::forward<Args>(args)...);
    }

    FuncType _func;

    template <class... AltArgs>
    using WithArgs = SelfReturning<Base, AltArgs...>;
};

template <class T>
using PairWithCtx = std::pair<T, const Context>;

using State = SelfReturning<PairWithCtx>::WithArgs<const Context&, Event>;

State::RetType A(const Context&, Event);
State::RetType B(const Context&, Event);

State::RetType A(const Context& ctx, Event evt) {
    printf("State A, counter = %d\n", ctx.counter);
    return std::visit(overloaded{
        [&] (EventA) { return make_pair(A, ctx); },
        [&] (EventB) { return make_pair(B, ctx.Inc()); }
    }, evt);
}

State::RetType B(const Context& ctx, Event evt) {
    printf("State B, counter = %d\n", ctx.counter);
    return std::visit(overloaded{
        [&] (EventA) { return make_pair(A, ctx.Inc()); },
        [&] (EventB) { return make_pair(B, ctx); }
    }, evt);
}

int main() {
    State state = A;
    Context ctx{};
    Event events[] = {EventB{}, EventA{}, EventB{}, EventA{}, };

    for (auto evt : events) {
        std::tie(state, ctx) = state(ctx, evt);
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，变化很小。

## 传入数据ðÿšš

至此，是时候解决我在第 1 部分中完全忽略的一个问题了。
传入数据。

我们当前的状态机模型基于这样一种想法，即事件本身是状态需要的唯一信息。这太天真了。在许多现实生活场景中，事件会携带数据。现在，使用`std::variant`，我们可以将数据放入不同的事件类型中。我们所需要做的就是将数据成员添加到事件结构中。我们将新的数据承载事件定义如下:

```
struct EventA {
    const char* msg{nullptr};
};
struct EventB {
    int number{0};
}; 
```

Enter fullscreen mode Exit fullscreen mode

其他都不需要改变。现在，在状态函数中，我们可以很容易地访问事件数据:

```
State::RetType A(const Context& ctx, Event evt) {
    printf("State A, counter = %d\n", ctx.counter);
    return std::visit(overloaded{
        [&] (EventA e) { 
            if (e.msg != nullptr) {
                printf("A message = \"%s\"", e.msg);
            } else {
                puts("A message = nullptr");
            }
            return make_pair(A, ctx); 
        },
        [&] (EventB) { return make_pair(B, ctx.Inc()); }
    }, evt);
}

State::RetType B(const Context& ctx, Event evt) {
    printf("State B, counter = %d\n", ctx.counter);
    return std::visit(overloaded{
        [&] (EventA e) { return make_pair(A, ctx.Inc()); },
        [&] (EventB e) { 
            printf("B number = %d\n", e.number);
            return make_pair(B, ctx); 
        }
    }, evt);
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。

现在把所有东西放在一起，我们得到下面的代码:

[编译](https://godbolt.org/g/rR5udQ)，[执行](http://coliru.stacked-crooked.com/a/273170beeb72c9a7)，

```
#include <tuple> #include <cstdio> #include <cstdlib> #include <variant>  
template<class... Ts> struct overloaded : Ts... { using Ts::operator()...; };
template<class... Ts> overloaded(Ts...) -> overloaded<Ts...>;

struct EventA {
    const char* msg{nullptr};
};
struct EventB {
    int number{0};
};

using Event = std::variant<EventA, EventB>;

struct Context {
    Context Inc() const {
        return Context{counter + 1};
    }
    int counter = 0;
};

template <class T>
struct identity {
    using type = T;
};

template <class T>
using identity_t = typename identity<T>::type;

template <template <class T> class Base = identity_t, class... Args>
struct SelfReturning {
    using RetType = Base<SelfReturning>;
    using FuncType = RetType(*)(Args... args);

    SelfReturning(FuncType func) : _func{ func } {}
    RetType operator() (Args... args) {
        return _func(std::forward<Args>(args)...);
    }

    FuncType _func;

    template <class... AltArgs>
    using WithArgs = SelfReturning<Base, AltArgs...>;
};

template <class T>
using PairWithCtx = std::pair<T, const Context>;

using State = SelfReturning<PairWithCtx>::WithArgs<const Context&, Event>;

State::RetType A(const Context&, Event);
State::RetType B(const Context&, Event);

State::RetType A(const Context& ctx, Event evt) {
    printf("State A, counter = %d\n", ctx.counter);
    return std::visit(overloaded{
        [&] (EventA e) { 
            if (e.msg != nullptr) {
                printf("A message = \"%s\"", e.msg);
            } else {
                puts("A message = nullptr");
            }
            return make_pair(A, ctx); 
        },
        [&] (EventB) { return make_pair(B, ctx.Inc()); }
    }, evt);
}

State::RetType B(const Context& ctx, Event evt) {
    printf("State B, counter = %d\n", ctx.counter);
    return std::visit(overloaded{
        [&] (EventA e) { return make_pair(A, ctx.Inc()); },
        [&] (EventB e) { 
            printf("B number = %d\n", e.number);
            return make_pair(B, ctx); 
        }
    }, evt);
}

int main() {
    State state = A;
    Context ctx{};
    Event events[] = {EventB{}, EventA{}, EventB{}, EventB{10}, EventA{}, EventA{"Hello, world!"}};

    for (auto evt : events) {
        std::tie(state, ctx) = state(ctx, evt);
    }

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结ðÿž”

正如承诺的那样，我们使用了一些黑暗模板魔法来实现:

1.  对`SelfReturning`的一个很好的概括，允许自定义返回类型和参数类型；
2.  通过用重载解决方案替换`switch`语句来提高编译时安全性；
3.  随事件传递数据。
4.  希望一路上有很多乐趣。