# 在 C++代码中嵌入文本文件(使用 Scons)

> 原文：<https://dev.to/mortoray/embedding-a-text-file-in-c-code-with-scons>

我需要一种在 C++代码中嵌入文本文件的方法。我最近在 Leaf 编译器中引入了 Leaf 代码，不想在运行时依赖文件。我找到了一种将文件作为`string`嵌入的方法，尽管这比我预期的要花更长的时间。

## 原始字符串

我先看了一下工具`xxd`。`-i`选项产生一个文件中字节的 C 常量。这是可行的，但是我想起 C++11 增加了原始字符串。一个`string`可以包含任何数据，包括空字节，所以应该没问题。

原始字符串给了我一种包含原始数据的方法。我有一个头文件，看起来像这样:

```
std::string dataBaseLeaf = R"~~~~( 
    ... the file data 
)~~~~"; 
```

Enter fullscreen mode Exit fullscreen mode

对于那些不熟悉原始字符串的人来说，`R"~~~~(`打开了原始字符串。匹配`)~~~~`的所有字符将按原样处理(无转义序列)。`~~~~`是我选择的一个分隔符。我们可以选择任何分隔符，但必须选择一个不出现在包含数据中的分隔符。

我将这个文件包含在另一个 C++文件中，并从`dataBaseLeaf`字符串中获取源文件的内容。

## Scons 构建

棘手的部分是弄清楚如何将它作为我的构建过程的一部分。每当我修改源文件时，我希望我的头文件被更新。我使用 Scons 作为构建工具，尽管有很多文档，但它缺少常见的用例。

我需要创造一个发电机，至少我认为这是它的名字。我使用结合了定制函数的`Action`命令从输入文件中产生 C++原始字符串。我们只看代码:

```
def RawStringIt(varName):
    def Impl(target, source, env):
        content = source[0].get_text_contents()
        with open(target[0].get_path(), 'w') as target_file:
            target_file.write( "std::string {} = R\"~~~~({})~~~~\";".format(varName,content))
        return 0

    return Action(Impl, "creating C++ Raw String $TARGET from $SOURCE" ) 
```

Enter fullscreen mode Exit fullscreen mode

`Impl`闭包执行实际的转换。没有太多，但我花了一段时间从各种参考资料中拼凑起来。我仍然不确定我这样做是否正确——感觉很奇怪，源文件有一个`get_text_contents()`,但是没有对应的写目标文件。`Action`将 Scons 中使用的实现包装成一个生产规则。

> 更聪明的实现会确保源文件中不存在分隔符`~~~~`。如果是的话，它应该选择一个新的。定界符可以是任何东西，我们不必去找它，所以一个随机数序列是合适的。我们可以继续尝试，直到我们找到一个工作(可能是第一次)。

我使用`RawStringIt`函数如下:

```
base_leaf = env.Command( 'include/runner/base.leaf.hpp', 'runner/base.leaf', RawStringIt("dataBaseLeaf") ) 
```

Enter fullscreen mode Exit fullscreen mode

这个`Command`创建了一个`base.leaf.hpp`文件，其中一个名为`dataBaseLeaf`的变量包含了`base.leaf`的内容。每当我改变源文件时，它都会重建原始字符串和依赖于它的程序。

我们可以看到`base_leaf`变量是这里的依赖集的一部分:

```
leaf = env.Program( 'leaf', [
    'bin/leaf.cpp',
    'bin/test_expecter.cpp',
    lib_lang, lib_parser, lib_util, lib_ir, lib_ir_llvm, lib_runner,
    ])
env.Requires( leaf, [base_leaf, base_unit_test] ) 
```

Enter fullscreen mode Exit fullscreen mode