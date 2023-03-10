# 节点模块深入研究:操作系统

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-os-2469>

因此，我已经通读了 Node 代码库一段时间，现在我开始对 Node 主进程如何工作、不同模块如何工作以及代码库的 C++和 JavaScript 部分之间的交互有了相当好的了解。这里强调的是“体面”。节点代码库相当复杂，我一直在尽最大努力熟悉它。

也就是说，我认为通过检查一个内置模块`os`来弄清楚整个模块生命周期在 Node 中是如何工作的会很有趣。

如果你对`os`不熟悉，它是一个节点模块，允许开发者获得系统架构或主机名之类的东西。这是我在个人机器上运行时得到的输出的一个例子。

```
> const os = require('os');
undefined
> os.arch();
'x64'
> os.homedir();
'/Users/captainsafia'
> os.hostname();
'eniac' 
```

Enter fullscreen mode Exit fullscreen mode

很漂亮，对吧？

你可以在这里找到`os`模块[的 JavaScript 源代码。实际上，目前我对阅读 JavaScript 代码中的任何函数并不特别感兴趣。我感兴趣的一件事是绑定到在代码的 C++部分创建的模块对象的`process.binding`。](https://github.com/nodejs/node/blob/f2e62b52385ff5e778d997ba86db70975e5670c7/lib/os.js) 

```
const {
  getCPUs,
  getFreeMem,
  getHomeDirectory: _getHomeDirectory,
  getHostname: _getHostname,
  getInterfaceAddresses: _getInterfaceAddresses,
  getLoadAvg,
  getOSRelease: _getOSRelease,
  getOSType: _getOSType,
  getTotalMem,
  getUserInfo: _getUserInfo,
  getUptime,
  isBigEndian
} = process.binding('os'); 
```

Enter fullscreen mode Exit fullscreen mode

由此，我们可以看到该对象包含了很多函数，这些函数后来在`os`模块的公共 API 的部分中被调用。我想做的第一件事是试图找出上面列出的函数的确切定义位置。我在代码库中做了一些搜索，找到了这里定义的的`os`函数的 C++定义。例如，下面是上面提到的`GetOSType` / `getOSType`函数的定义。

```
static void GetOSType(const FunctionCallbackInfo<Value>& args) {
  Environment* env = Environment::GetCurrent(args);
  const char* rval;

#ifdef __POSIX__
  struct utsname info;
  if (uname(&info) < 0) {
    CHECK_GE(args.Length(), 1);
    env->CollectExceptionInfo(args[args.Length() - 1], errno, "uname");
    return args.GetReturnValue().SetUndefined();
  }
  rval = info.sysname;
#else // __MINGW32__
  rval = "Windows_NT";
#endif // __POSIX__ 
  args.GetReturnValue().Set(OneByteString(env->isolate(), rval));
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这个函数使用 Unix 中的`uname` [函数](https://www.systutorials.com/docs/linux/man/1-uname/)来获取关于操作系统的信息，并从中提取操作系统类型。它还有一些条件逻辑来评估它是运行在 Unix 系统上还是 Windows 系统上。

```
args.GetReturnValue().Set(OneByteString(env->isolate(), rval)); 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，return 语句不是 C++代码中常见的传统的`return`。我记得很久以前浏览过一本关于如何开发本机 Nod 扩展的书，这种特殊的返回代码允许代码库的 JavaScript 部分访问从 C++模块返回的数据。代码中最有趣的部分实际上一直在底部。

```
void Initialize(Local<Object> target,
                Local<Value> unused,
                Local<Context> context) {
  Environment* env = Environment::GetCurrent(context);
  env->SetMethod(target, "getHostname", GetHostname);
  env->SetMethod(target, "getLoadAvg", GetLoadAvg);
  env->SetMethod(target, "getUptime", GetUptime);
  env->SetMethod(target, "getTotalMem", GetTotalMemory);
  env->SetMethod(target, "getFreeMem", GetFreeMemory);
  env->SetMethod(target, "getCPUs", GetCPUInfo);
  env->SetMethod(target, "getOSType", GetOSType);
  env->SetMethod(target, "getOSRelease", GetOSRelease);
  env->SetMethod(target, "getInterfaceAddresses", GetInterfaceAddresses);
  env->SetMethod(target, "getHomeDirectory", GetHomeDirectory);
  env->SetMethod(target, "getUserInfo", GetUserInfo);
  target->Set(FIXED_ONE_BYTE_STRING(env->isolate(), "isBigEndian"),
              Boolean::New(env->isolate(), IsBigEndian()));
}

} // namespace os
} // namespace node

NODE_BUILTIN_MODULE_CONTEXT_AWARE(os, node::os::Initialize) 
```

Enter fullscreen mode Exit fullscreen mode

所以在这里，看起来我们正在初始化我们在 C++级别上编写的函数和我们在绑定到`os`模块时引用它们的名字之间的映射。我好奇的一件事是`NODE_BUILTIN_MODULE_CONTEXT_AWARE`宏函数中发生了什么。看起来我们传递给它的是`os`名称空间，它包含了这个 C++文件中定义的所有函数，以及初始化函数。我在代码库中搜索了一下，在这里找到了`NODE_BUILTIN_MODULE_CONTEXT_AWARE` [的代码](https://github.com/nodejs/node/blob/f3cd53751ba3f917a0996a8f38c991242a8fbc76/src/node_internals.h#L155)。

```
#define NODE_BUILTIN_MODULE_CONTEXT_AWARE(modname, regfunc) \
  NODE_MODULE_CONTEXT_AWARE_CPP(modname, regfunc, nullptr, NM_F_BUILTIN) 
```

Enter fullscreen mode Exit fullscreen mode

所以它看起来像这个宏函数，调用了`NODE_MODULE_CONTEXT_AWARE_CPP`宏函数，这个宏函数在同一个文件中定义，有如下定义。

```
#define NODE_MODULE_CONTEXT_AWARE_CPP(modname, regfunc, priv, flags) \
  static node::node_module _module = { \
    NODE_MODULE_VERSION, \
    flags, \
    nullptr, \
    __FILE__ , \
    nullptr, \
    (node::addon_context_register_func) (regfunc), \
    NODE_STRINGIFY(modname), \
    priv, \
    nullptr \
  }; \
  void _register_ ## modname() { \
    node_module_register(&_module); \
  } 
```

Enter fullscreen mode Exit fullscreen mode

哦！这个宏函数正在创建我在[上一篇博文](https://dev.to/captainsafia/how-does-node-load-built-in-modules-ln1-temp-slug-884682)中发现的`node_module`结构。原来，当一个模块被注册时，被调用的注册函数实际上是`Initialize`函数，它将函数关联加载到当前环境中。一切都开始有意义了。

这是我所理解的内置模块生命周期的故事。

*   在代码的 C++端，在特定模块的名称空间下定义了一组函数。例如，`GetOSType`函数是在`os`名称空间下定义的。
*   该模块使用一组宏函数注册到当前进程中。这种注册包括将上面定义的函数映射到一个名称集合，当我们提取绑定时，可以使用这些名称来引用它们。
*   与特定模块相关联的 JavaScript 代码使用`process.binding`从正在运行的进程中提取函数的注册名称。
*   使用`process.binding`提取的函数在 JavaScript 模块中作为公共 API 的一部分导出的函数中调用。例如，`os.type()` JavaScript 函数调用最终依赖于引用 C++中定义的`GetOSType`函数的`getOSType`绑定。

这对我来说是有意义的。其中肯定有我不明白的地方。主要是很好奇这个`process`对象的范围是什么。对我来说，理解原生 C++和 JavaScript 代码之间联系的关键是对什么是`process`有一个明确的认识。也许我会在另一个时间深入探讨这个问题…

如果你对这篇文章有任何问题或反馈，你可以在 Twitter 上联系我。