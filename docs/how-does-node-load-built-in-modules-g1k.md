# Node 如何加载内置模块？

> 原文：<https://dev.to/captainsafia/how-does-node-load-built-in-modules-g1k>

附录:在我发表这篇博文后，我从 Node 的一个维护者那里得到了一些关于我提到的一些事情的反馈。在下面的帖子中，我和代码提到的“内置模块”实际上是绑定，是由 C++创建的 JavaScript 对象，代表一个模块。似乎由于一些遗留原因，它们在实际代码中被称为“内置模块”。事实上，这篇博文的标题应该是**“节点如何注册模块绑定？”我无法在不弄乱大量超链接的情况下更改标题，所以我就把这个附录留在这里。好吧，享受这篇文章！**

因此，在我写的最后一篇博文中，我开始研究节点主进程是如何初始化的。我很快发现那里发生了很多事情(这是理所当然的！).特别引起我注意的一件事是对一个函数的[引用](https://github.com/nodejs/node/blob/8938c4c22d720c33441ce95d801056532b99ec18/src/node.cc#L4200)，该函数似乎在节点主进程的初始化阶段加载内置模块。

```
node::RegisterBuiltinModules(); 
```

Enter fullscreen mode Exit fullscreen mode

我想更深入地了解这一点，所以我开始查看代码库以了解更多。

旁注:在这些博文中，我对“代码库”的拼写方式做了一些改动。这主要是因为我不知道它应该拼写为“codebase”还是“code base”。我四处打探了一下，发现[stack exchange 上有一个有趣的讨论](https://english.stackexchange.com/questions/237835/codebase-or-code-base),似乎表明“codebase”是一种不太模糊的拼写方式，尽管“code base”也同样有效。

我最终在这里找到了`node:RegisterBuiltinModule`函数[的定义。](https://github.com/nodejs/node/blob/8938c4c22d720c33441ce95d801056532b99ec18/src/node.cc#L4613) 

```
void RegisterBuiltinModules() {
#define V(modname) _register_##modname();
  NODE_BUILTIN_MODULES(V)
#undef V
} 
```

Enter fullscreen mode Exit fullscreen mode

这段特殊的代码利用了一些特殊的 C++语法来定义宏。本质上，上面截取的代码中的第二行是说每次出现字符串`V(modname)`时，它都应该被替换为字符串`_register_##modname()`。根据我的理解，这基本上是创建一个缩写，这样就可以重复调用`_register_modname`,而不必输入完整的函数名。

接下来我要弄清楚的是`NODE_BUILTIN_MODULES`到底在做什么。我在节点代码库中的另一个源文件中找到了这个函数调用的定义。

```
#define NODE_BUILTIN_MODULES(V) \
  NODE_BUILTIN_STANDARD_MODULES(V) \
  NODE_BUILTIN_OPENSSL_MODULES(V) \
  NODE_BUILTIN_ICU_MODULES(V) 
```

Enter fullscreen mode Exit fullscreen mode

所以它看起来像是一个简单的包装器函数，调用其他一些函数(我假设)来加载不同的模块。上面最有趣的一行显然是`NODE_BUILTIN_STANDARD_MODULES`位。`NODE_BUILTIN_STANDARD_MODULES`是另一个功能宏，负责加载 Node 内的一些内置模块。如果你查看[源文件](https://github.com/nodejs/node/blob/f3cd53751ba3f917a0996a8f38c991242a8fbc76/src/node_internals.h#L101)，你会看到对`fs`、`os`和`buffer`模块的引用。

在这一点上，我有点困惑。到处都是这些功能宏，*似乎在加载内置模块，但是它们到底是如何连接到运行系统的。为什么我可以在 Node 中运行`require('fs')`并且一切都很方便？我注意到在`NODE_BUILTIN_STANDARD_MODULES`宏的定义上面有一个注释。* 

```
// A list of built-in modules. In order to do module registration
// in node::Init(), need to add built-in modules in the following list.
// Then in node::RegisterBuiltinModules(), it calls modules' registration
// function. This helps the built-in modules are loaded properly when
// node is built as static library. No need to depends on the
// __attribute__ ((constructor)) like mechanism in GCC. 
```

Enter fullscreen mode Exit fullscreen mode

所以这个评论真的很有帮助。看起来这些宏实际上是注册阶段发生的事情的前兆。那么注册阶段到底是什么样子的呢？我又仔细查看了一下代码，发现了我之前提到的`_register_##modname`函数的[引用](https://github.com/nodejs/node/blob/f3cd53751ba3f917a0996a8f38c991242a8fbc76/src/node_internals.h#L150)。

```
void _register_ ## modname() { \
  node_module_register(&_module); \
} 
```

Enter fullscreen mode Exit fullscreen mode

所以看起来`_register_##modname`基本上只是用需要注册的模块的引用来调用`node_module_register`。那么在`node_module_register`函数中发生了什么呢？更多窥探的时间到了！

我最终在这里找到了`node_module_register`函数[的函数定义。看起来是这样的。](https://github.com/nodejs/node/blob/8938c4c22d720c33441ce95d801056532b99ec18/src/node.cc#L2145) 

```
extern "C" void node_module_register(void* m) {
  struct node_module* mp = reinterpret_cast<struct node_module*>(m);

  if (mp->nm_flags & NM_F_BUILTIN) {
    mp->nm_link = modlist_builtin;
    modlist_builtin = mp;
  } else if (mp->nm_flags & NM_F_INTERNAL) {
    mp->nm_link = modlist_internal;
    modlist_internal = mp;
  } else if (!node_is_initialized) {
    // "Linked" modules are included as part of the node project.
    // Like builtins they are registered *before* node::Init runs.
    mp->nm_flags = NM_F_LINKED;
    mp->nm_link = modlist_linked;
    modlist_linked = mp;
  } else {
    modpending = mp;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

哦天啊！这里发生了很多事情。如果你看过很多 C/C++代码，这个函数的第一行是非常标准的。由于 C++是类型化语言，我们实际上是将参数中作为`m`传递的非类型化指针转换为一个专门类型化的指针，作为指向`mp`中模块的指针。

使用一个`node_module`结构引用该模块。我决定查看一下代码库，看看在`node_module`结构中存储了什么属性。我想这会让我对存储在结构中的不同属性有所了解。

旁注:如果你不熟悉 C/C++但熟悉 JavaScript，你可以把 struct 想象成一个对象。它基本上存储了标签和它的值之间的关联。

我在这里找到了`node_module`结构[的定义。不幸的是，结构中的字段没有注释，所以很难弄清楚发生了什么。](https://github.com/nodejs/node/blob/12c8b4d15471cb6211b39c3a2ca5b10fa4b9f12b/src/node.h#L459) 

```
struct node_module {
  int nm_version;
  unsigned int nm_flags;
  void* nm_dso_handle;
  const char* nm_filename;
  node::addon_register_func nm_register_func;
  node::addon_context_register_func nm_context_register_func;
  const char* nm_modname;
  void* nm_priv;
  struct node_module* nm_link;
}; 
```

Enter fullscreen mode Exit fullscreen mode

一些字段，如`nm_version`和`nm_filename`，是自我解释的，但其他的不是。什么是`nm_priv`和`nm_dso_handle`？我只有问题，没有答案。与我目前的探索相关，看起来与模块相关联的模块注册函数存储在结构本身中。得心应手！

我想我应该回头看看`register_module_name`函数，看看我是否能辨别出该结构中的其他字段是用于什么的。我在函数中注意到的一个模式是如下所示的代码片段。

```
mp->nm_link = modlist_builtin;
modlist_builtin = mp; 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们正在将结构中的一个字段设置为一个特定的值(`modlist_builtin`)，然后将该值设置为我们刚刚修改的节点模块结构。通过查看上面结构中的类型定义，我知道`nm_link`也是一个`node_module`结构，所以看起来我们在这里创建了一个模块引用链。我试图通过查看代码中使用了`modlist_builtin`的地方来验证我的假设，我发现了[这个片段](https://github.com/nodejs/node/blob/8938c4c22d720c33441ce95d801056532b99ec18/src/node.cc#L2179)。

```
node_module* get_builtin_module(const char* name) {
  return FindModule(modlist_builtin, name, NM_F_BUILTIN);
} 
```

Enter fullscreen mode Exit fullscreen mode

这肯定证实了我的怀疑，我们正在创造`node_modules`的链接结构。然后当我们想要获取一个特定的模块时，我们通过这个链接结构进行搜索来返回它。

我仍然想知道这些模块的实际注册会在什么时候发生。我试图通过在代码库中搜索字符串“nm_register_func”来解决这个问题。这是用于在上面的`node_module`结构中存储模块注册函数的引用的函数名。我找到了这个函数名的一些调用，但是最有启发性的一个是在节点库中的`DLOpen`函数中。`dlopen`是一个函数的通称，该函数负责使可执行文件中的函数标识符可供调用它们的程序使用。DLOpen 中的“DL”代表动态加载的**D**L，因为模块不是在程序开始时加载，而是在运行`require`时加载。

总结一下，我的理解是这样的。

*   当节点主流程启动时，它运行内置模块，并创建一个类似模块结构的链表，存储在`modlist_builtin`中。
*   结构包含了关于如何注册一个特定模块的信息。
*   当通过 require 加载一个模块时，存储在`node_module`结构中的`nm_register_func`被调用。

唷！这看起来很简单，只要你事后回想一下。如果我误解了代码的任何部分，或者如果你对 Node 中的内置模块加载有任何疑问，请[告诉我](https://twitter.com/captainsafia)。