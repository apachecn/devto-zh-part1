# 详细介绍 C++17:修复和弃用

> 原文：<https://dev.to/fenbf/c17-in-details-fixes-and-deprecation>

新的 C++标准 C++17 即将被接受和发布。已经有了一个工作草案，不久前它进入了最终的 ISO 投票。这是一个学习和了解新特性的好机会。

让我们慢慢开始，今天我们将看看语言/库修复和删除的元素。

**注**:这篇文章最初发表在我的博客: [Bartek 的编码博客:C++17 的细节:修复和弃用](http://www.bfilipek.com/2017/05/cpp17-details-fixes-deprecation.html)作为关于 C++17 细节系列的一部分。

## 文档和链接

首先，如果你想自己钻研标准，可以在这里阅读最新的草案:

[N4659，2017-03-21，**工作草案，编程语言 C++** 标准](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/n4659.pdf)——链接也出现在【isocpp.org】的[上](https://isocpp.org/)。

编译器支持: [C++编译器支持](http://en.cppreference.com/w/cpp/compiler_support)

在 Visual Studio 中(从 VS 2015 Update 3 开始)你可以尝试使用标准版本开关，测试你的代码是否符合给定的标准:[编译器中的标准版本开关](https://blogs.msdn.microsoft.com/vcblog/2016/06/07/standards-version-switches-in-the-compiler/)。

而且，我还准备了一份清单，对**所有 C++17 语言特性的简明描述** : [**在这里抓取**](http://eepurl.com/cyycFz) 。是一页的参考卡，PDF。

还有更多:今年年初，我发表了一篇关于 C++17 大部分特性的长篇合作文章，[请看这里](http://www.bfilipek.com/2017/01/cpp17features.html)。如果你想更新它，添加更多的例子，只需在回购上做一个拉请求[。](https://github.com/fenbf/cpp17features)

## 移除的东西

该语言的草稿现在已经超过了 1586 页！由于兼容性要求，添加了新功能，但没有删除太多功能。幸运的是，有些事情可能会消失。

### 移除三字符

三字符是一种特殊的字符序列，当系统不支持 ISO 646 字符集的 7 位类 ASCII 字符时可以使用。比如`??=`产生`#`，`??-`产生`~`。顺便说一下:所有 C++的基本源字符集都适合 7 位 ASCII 码。这些序列很少被使用，通过删除它们，代码的翻译阶段可能会更简单。

如果想了解更多:[c++ 03-c++中三元图序列的用途？-堆栈溢出](https://stackoverflow.com/questions/1234582/purpose-of-trigraph-sequences-in-c)，或[有向图和三向图-维基百科](https://en.wikipedia.org/wiki/Digraphs_and_trigraphs)。

更多详情请见: [N4086](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4086.html) 。如果你真的需要 Visual Studio 的 trigraphs，看看 [/Zc:trigraphs switch](https://msdn.microsoft.com/en-us/library/ee462497.aspx) 。此外，其他编译器可能会以某种方式离开支持。其他编译器状态:在 GCC: 5.1 和 Clang: 3.5 中完成。

### 删除注册关键字

在 2011 C++标准中，`register`关键字被弃用，因为它没有任何意义。现在它被移除了。这个关键字是保留的，在未来的版本中可能会重新使用(例如`auto`关键字被重用，现在是一个强大的东西)。

更多详情: [P0001R1](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0001r1.html) ，MSVC 2017: **尚未**。在 GCC: 7.0 和 Clang: 3.8 中完成。

### 删除不推荐使用的运算符++(bool)

这个操作符已经被废弃很久了！在 C++98 中，人们决定最好不要使用它。但只有在 C++17 中，委员会同意将其从语言中删除。

更多详情: [P0002R1](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0002r1.html) ，MSVC 2017: **尚未**。在 GCC: 7.0 和 Clang: 3.8 中完成。

### 从 C++17 中移除不推荐使用的异常规范

在 C++17 中异常规范将是类型系统的一部分(见 [P0012R1](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0012r1) )。该标准仍然包含旧的和不赞成使用的异常规范，这些规范似乎不实用并且没有被使用。

例如:

```
void fooThrowsInt(int a) throw(int) {  
   printf_s("can throw ints\n");  
   if (a == 0)  
      throw 1;  
} 
```

以上代码从 C++11 开始就不推荐使用了。唯一实用的异常声明是`throw()`,这意味着这段代码不会抛出任何东西。但是从 C++11 开始建议使用`noexcept`。

例如，在 clang 4.0 中，您会得到以下错误:

```
error: ISO C++1z does not allow dynamic exception specifications [-Wdynamic-exception-spec]
note: use 'noexcept(false)' instead 
```

更多详情: [P0003R5](http://wg21.link/p0003r5) ，MSVC 2017: **尚未**。在 GCC: 7.0 和 Clang: 4.0 中完成。

### 删除 auto_ptr

这是我最喜欢的语言更新之一！

在 C++11 中我们得到了智能指针:`unique_ptr`、`shared_ptr`和`weak_ptr`。由于 move 语义，该语言最终可以支持正确的唯一资源转移。`auto_ptr`在语言中是一个老问题——在这里看到[完整的原因——为什么 auto_ptr 被弃用](http://www.bfilipek.com/2013/02/smart-pointers-gotchas.html#deprecated)。它应该几乎自动转换为`unique_ptr`。有一段时间`auto_ptr`被弃用了(从 C++11 开始)。许多编译器会这样报告:

```
warning: 'template<class> class std::auto_ptr' is deprecated 
```

现在它进入了僵尸状态，基本上，你的代码就编译不出来了。

以下是来自:MSVC 2017 使用`/std:c++latest`时的错误:

```
error C2039: 'auto_ptr': is not a member of 'std' 
```

如果你需要从`auto_ptr`到`unique_ptr`的转换帮助，你可以检查 Clang Tidy，因为它提供自动转换: [Clang Tidy:现代化-替换-自动-ptr](https://clang.llvm.org/extra/clang-tidy/checks/modernize-replace-auto-ptr.html) 。

更多详情: [N4190](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0001r1.html)

在链接的论文 [N4190](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0001r1.html) 中:还删除了其他库项目:`unary_function` / `binary_function`、`ptr_fun()`、`mem_fun()` / `mem_fun_ref()`、`bind1st()` / `bind2nd()`和`random_shuffle`。

## 修理

我们可以争论什么是语言标准中的修正，什么不是。下面我挑选了三件事，听起来像是对以前标准中遗漏的东西的修复。

### 直接列表初始化的新自动规则

从 C++11 开始，我们遇到了一个奇怪的问题:

```
auto x { 1 }; 
```

推导为`initializer_list`。有了新的标准，我们可以解决这个问题，因此它将推导出`int`(正如大多数人最初猜测的那样)。

要做到这一点，我们需要理解两种初始化方式:复制和直接。

```
auto x = foo(); // copy-initialization
auto x{foo}; // direct-initialization, initializes an
             // initializer_list (until C++17)
int x = foo(); // copy-initialization
int x{foo}; // direct-initialization 
```

对于直接初始化，C++17 引入了新的规则:

```
For a braced-init-list with only a single element, auto
deduction will deduce from that entry;
For a braced-init-list with more than one element, auto
deduction will be ill-formed. 
```

例如:

```
auto x1 = { 1, 2 }; // decltype(x1) is std::initializer_list<int>
auto x2 = { 1, 2.0 }; // error: cannot deduce element type
auto x3{ 1, 2 }; // error: not a single element
auto x4 = { 3 }; // decltype(x4) is std::initializer_list<int>
auto x5{ 3 }; // decltype(x5) is int 
```

Ville Voutilainen 在 [N3922](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3922.html) 和 [Auto 和 brassed-init-lists](http://open-std.org/JTC1/SC22/WG21/docs/papers/2013/n3681.html)中提供了更多细节。从 MSVC 14.0，GCC: 5.0，Clang: 3.8 就开始工作了。

### 不带消息的 static_assert

不言自明。它只允许有条件而不传递消息，有消息的版本也将可用。它将与其他断言兼容，如 BOOST_STATIC_ASSERT(它从一开始就没有任何消息)。

```
static_assert(std::is_arithmetic_v<T>, "T must be arithmetic");
static_assert(std::is_arithmetic_v<T>); // no message needed since C++17 
```

更多详情: [N3928](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n3928.pdf) ，MSVC 2017 支持，GCC: 6.0，Clang: 2.5。

### 基于范围的森林中不同的开始和结束类型

因为 C++11 基于范围的 for 循环在内部被定义为:

```
{
   auto && __range = for-range-initializer;
   for ( auto __begin = begin-expr,
              __end = end-expr;
              __begin != __end;
              ++__begin ) {
        for-range-declaration = *__begin;
        statement
   }
} 
```

如你所见，`__begin`和`__end`具有相同的类型。这可能会引起一些麻烦——例如，当你有一个不同类型的哨兵时。

在 C++17 中，它变成了:

```
{
  auto && __range = for-range-initializer;
  auto __begin = begin-expr;
  auto __end = end-expr;
  for ( ; __begin != __end; ++__begin ) {
    for-range-declaration = *__begin;
    statement
  }
} 
```

`__begin`和`__end`的类型可能不同；只有比较运算符是必需的。这个小小的改变让 Range TS 用户有了更好的体验。

更多详情请见 [P0184R0](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0184r0.html) ，MSVC 2017 支持，GCC: 6.0，Clang: 3.6。

## 摘要

语言标准有所提高，但是委员会中有一些动作要移除和清理一些特性。出于兼容性的原因，我们不能删除所有的问题，但一个接一个，我们可以得到一些改善。

在我的博客上，我发表了更多关于 C++17 细节的文章，所以去那里继续关注吧:)

再次提醒，记得抢我的 [**C++17 语言 Ref 卡**](http://eepurl.com/cyycFz) 。

### 喜欢这篇文章？

如果你想从我这里读到更多，请访问我在 bfilipek.com 的博客。我每周都写关于 native/c++编程的故事。