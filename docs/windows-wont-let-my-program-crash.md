# Windows 不会让我的程序崩溃

> 原文：<https://dev.to/erikmcclure/windows-wont-let-my-program-crash>

众所周知，windows 有一个坏习惯，如果你在 WinProc 回调函数中，T1 会吃掉你的异常 T2。这种行为会导致各种各样的混乱，就像你的程序消失在空气中，没有任何错误信息，因为堆栈溢出终止了程序，而没有抛出异常。我没有意识到的是，它还会吃掉`assert()`，这让调试变得很糟糕，因为断言会抛出，整个用户回调会立即终止，而不会展开任何堆栈，然后 windows 就会...继续下去，即使程序现在处于可笑的腐败状态，因为只执行了一半的功能。

当试图找到一种方法来解决这个问题时，我发现 windows 可以选择至少 4 种不同的方法来从你的程序中吃掉异常。我已经用下面的代码告诉内核停止吃我的异常:

```
HMODULE kernel32 = LoadLibraryA("kernel32.dll");
  assert(kernel32 != 0);
  tGetPolicy pGetPolicy = (tGetPolicy)GetProcAddress(kernel32, "GetProcessUserModeExceptionPolicy");
  tSetPolicy pSetPolicy = (tSetPolicy)GetProcAddress(kernel32, "SetProcessUserModeExceptionPolicy");
  if(pGetPolicy && pSetPolicy && pGetPolicy(&dwFlags))
    pSetPolicy(dwFlags & ~EXCEPTION_SWALLOWING); // Turn off the filter 
```

Enter fullscreen mode Exit fullscreen mode

然而，尽管如此，COM 本身还是在我的程序周围包装了一个完整的`try {} catch {}`语句，所以我也必须想办法把它关掉。显然[微软](https://blogs.msdn.microsoft.com/oldnewthing/20110120-00/?p=11713/)的一些天才决定默认行为应该是在他们制作 COM 的时候吞下异常，现在他们不能改变这个默认行为，因为这会破坏所有现在依赖 COM 吞下异常来正常运行的应用程序！所以，我用下面的代码关闭了它:

```
CoInitialize(NULL); // do this first
if(SUCCEEDED(CoInitializeSecurity(NULL, -1, NULL, NULL, RPC_C_AUTHN_LEVEL_PKT_PRIVACY,
  RPC_C_IMP_LEVEL_IMPERSONATE, NULL, EOAC_DYNAMIC_CLOAKING, NULL)))
{
  IGlobalOptions *pGlobalOptions;
  hr = CoCreateInstance(CLSID_GlobalOptions, NULL, CLSCTX_INPROC_SERVER, IID_PPV_ARGS(&pGlobalOptions));
  if(SUCCEEDED(hr))
  {
    hr = pGlobalOptions->Set(COMGLB_EXCEPTION_HANDLING, COMGLB_EXCEPTION_DONOT_HANDLE);
    pGlobalOptions->Release();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有两个额外的函数可以在你的程序中吞掉异常:`_CrtSetReportHook2`和`SetUnhandledExceptionFilter`，但这两个都是针对 SEH 或 C++异常的，我抛出的是断言，而不是异常。通过用我自己的版本替换断言#define，我实际上能够验证抛出一个实际的 C++异常*会不会导致程序崩溃...但是一个断言没有。具体来说，一个断言调用`abort()`，引发`SIGABRT`，使任何正常程序崩溃。然而，事实证明，Windows 正在吃掉中止信号，以及我试图引发的所有其他信号，这是一个问题，因为一半的库是用 C 编写的，而 C 显然不能引发 C++异常。断言失败甚至出现在输出中...但是没有让程序崩溃！* 

```
Assertion failed!

Program: ...udio 2015\Projects\feathergui\bin\fgDirect2D_d.dll
File: fgEffectBase.cpp
Line: 20

Expression: sizeof(_constants) == sizeof(float)*(4*4 + 2) 
```

Enter fullscreen mode Exit fullscreen mode

无论我做什么，Windows 都拒绝让断言失败使程序崩溃，甚至在调试器中触发断点。事实上，调用输出一条`int 3` CPU 指令的`__debugbreak()`内部函数时，*完全忽略了*，就好像它根本不存在一样。在不使用 C++异常的情况下，使程序崩溃的唯一可靠的方法是做一些类似于除以 0 的事情，或者尝试写入一个空指针，这将触发 segfault。

任何优秀的开发人员都应该使用断言来验证他们的假设，所以让断言无声地失败，然后*破坏程序*比忽视它们的存在还要糟糕！现在你可以在你的代码中有一个触发的断言，终止那个回调，让你的程序处于中断状态，然后下一个被处理的消息因为奇怪和奇异的原因爆炸，这没有意义，因为它们是不可能的。

我已经很难让我的程序运行起来，我没想到让它们崩溃会这么难。