# Node 中的 process.binding()是如何工作的？

> 原文：<https://dev.to/captainsafia/how-does-processbinding-in-node-work-45a6>

这篇文章的另一个标题是:进入 V8 虫洞。想知道为什么吗？请继续阅读！

所以我已经深入研究这些节点模块有一段时间了。

在我的上一篇文章的[中，我深入到了代码库的 C 部分，并简要提到了`process.binding`以及它是如何用于向 Node 公开内部模块(用 C 编写)的。我对这是如何工作的细节很好奇，所以我决定深入研究一下。](https://dev.to/captainsafia/node-module-deep-dive-fs-417l)

我首先在谷歌上搜索“什么是 process.binding node ”,找出这方面的现有资料。我发现这个[相当有用的滑梯](http://lanceball.com/process-bindings)由[兰斯球](http://lanceball.com)生产。有趣的部分从第 12 张[幻灯片](http://lanceball.com/process-bindings/#/12)开始，它概述了“Node.js 主函数接受一个进程对象”我决定去寻找与这条语句相关的代码。这些幻灯片大约是 3 年前的，所以我不得不做一些挖掘来找到提到的实际代码行。我尽了最大努力，但还是找不到幻灯片中提到的初始化节点流程的入口点。

我想知道在过去的三年里，初始化是否已经转移到代码库的非 JavaScript 部分。也许节点进程及其依赖项完全是通过 C++扩展初始化的？我不确定。如果你对此有任何见解，请告诉我。

下一张幻灯片讨论了如何在 C++中初始化 Process 对象，以及它的一些基本属性。果然，我在这里找到了代码。与 Lance 在幻灯片中引用的代码相比，它发生了很大的变化，让我们来看一看。

```
void SetupProcessObject(const FunctionCallbackInfo<value>& args) {
  Environment* env = Environment::GetCurrent(args);

  CHECK(args[0]->IsFunction());

  env->set_push_values_to_array_function(args[0].As<function>());
  env->process_object()->Delete(
      env->context(),
      FIXED_ONE_BYTE_STRING(env->isolate(), "_setupProcessObject")).FromJust();
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧！所以看起来像是`SetupProcessObject`函数将回调作为参数，并配置传递的“环境”。我想做的第一件事是弄清楚`Environment::GetCurrent(args)`到底做了什么。这个函数调用在这个文件中使用得相当多，我认为弄清楚它到底做了什么是非常好的。我试图检查`node.cc`文件的头文件，看它是否引用了某种`env`或`environment`文件。果然，有一个对`env-inl.h`头文件的引用，所以我去找了一下，在这里找到了。它定义了几个`GetCurrent`函数，每个函数接受不同类型的参数。我试图找到带有`FunctionCallbackInfo<value>`参数的`GetCurrent`函数，该参数与上面代码片段中传递给它的参数相似，并找到了以下内容。

```
inline Environment* Environment::GetCurrent(
    const v8::FunctionCallbackInfo<:value>& info) {
  CHECK(info.Data()->IsExternal());
  return static_cast<environment>(info.Data().As<:external>()->Value());
} 
```

Enter fullscreen mode Exit fullscreen mode

所以看起来这个函数正在做的是使用`Data()`函数从`info`对象中提取一些数据，并将其值作为`v8:External`数据类型返回。我以前从未见过`static_cast`操作符，所以我对它做了一些调查，[发现](https://en.wikipedia.org/wiki/Static_cast)负责进行显式类型转换。这证实了我的假设，即该函数主要负责从`FunctionCallbackInfo`对象中提取关于当前节点运行环境的细节，并将其作为`Environment`类型返回。无论如何，我现在对所有这些事情都不太感兴趣。我特别好奇的是这里的下一行。

```
 env->process_object()->Delete(
      env->context(),
      FIXED_ONE_BYTE_STRING(env->isolate(), "_setupProcessObject")).FromJust();
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧！因此，这条线似乎从环境中提取了“process_object”并在其中添加了一些东西。我查看了代码库，发现`process_object()`函数返回一个`Local<object>`类型的对象。通过四处打探，我发现这个`Local<object>`是在 JavaScript 引擎 V8 库中定义的类型。通过查看[的一些文档](https://v8docs.nodesource.com/node-0.8/db/d85/classv8_1_1_object.html)，我发现`Local<object>`是 V8 中其他对象(如日期对象和数字对象)继承的对象。

接下来我想弄清楚`env->context()`是什么。从代码库中一些头文件的窥探中，我发现它是一个`v8:Context`对象。我又读了一些[文档](http://bespin.cz/~ondras/html/classv8_1_1Context.html)，发现 Node 似乎使用了上下文对象来维护当前运行进程的基本配置细节，比如其上安装了什么扩展。

下一部分有趣的代码是这个小家伙。

```
FIXED_ONE_BYTE_STRING(env->isolate(), "_setupProcessObject") 
```

Enter fullscreen mode Exit fullscreen mode

嗯。wut。我首先想弄清楚`FIXED_ONE_BYTE_STRING`到底做了什么。根据名称，我怀疑它会根据给定的参数生成一个固定长度的字符串。第二个参数显然是一个字符串，所以这里没有太多的挖掘，但是我对第一个参数`env->isolate()`很好奇。在我阅读了代码库不同部分的更多代码后，我发现`env->isolate()`可能代表了一个 V8:Isolate 对象，你可以在这里阅读更多关于[的内容。](https://v8docs.nodesource.com/node-0.8/d5/dda/classv8_1_1_isolate.html)

好吧，我在那里经历了很多虫洞，并没有真正找到我需要知道的东西(我们真的找到了我们需要知道的东西吗？).我将做更多的阅读，更好地了解理解 Node 和 V8 之间的桥梁的探索性路线图。现在好的一点是，我对探索 V8 和 Node 之间的桥梁感觉更舒服了。对我来说，这是一个不稳定和令人伤脑筋的问题，但我慢慢开始明白了。向前！

如果你知道更多关于 V8 的来源，并想澄清我上面提到的任何事情，[让我知道](https://twitter.com/captainsafia)。