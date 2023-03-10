# 在苹果脚本中调用函数

> 原文：<https://dev.to/onmyway133/calling-function-inside-apple-script-ec5>

原帖[https://github.com/onmyway133/blog/issues/88](https://github.com/onmyway133/blog/issues/88)

我一直在用苹果脚本做我的 Finder 扩展 [FinderGo](https://github.com/onmyway133/FinderGo) 。由于[沙箱](https://www.objc.io/issues/14-mac/sandbox-scripting/)，所有脚本必须位于`Application Scripts`文件夹中。

今天，我正在重写我的 Xcode 扩展 [XcodeWay](https://github.com/onmyway133/XcodeWay) 。在 Xcode 8 之前，我们可以用 Xcode 插件和各种魔法实现梦想[https://github . com/on myway 133/Xcode way/blob/1.0/Xcode way/Helper/ftgenvironmentmanager . m # L50](https://github.com/onmyway133/XcodeWay/blob/1.0/XcodeWay/Helper/FTGEnvironmentManager.m#L50)。但是自从引入 Xcode 源代码编辑器扩展后，它就不再工作了。所以我重写了 XcodeWay 作为扩展【https://github.com/onmyway133/XcodeWay/releases/tag/1.1.0 T4】

扩展必须在沙箱内运行。如果你把你的`XcodeWayExtensions.entitlements`中的`App Sandbox`切换到`NO`，它不会加载。所以沙盒在我想做的事情上限制了我很多。而且在 Xcode 9 下，我不能用`NSWorkspace`打开`Finder`。

所以我想我也可以使用`Apple Script`，它非常有效。唯一的限制是代码重用，因为我只知道如何运行整个脚本。一种方法是导入其他苹果脚本[https://stack overflow . com/questions/2606136/import-applescript-methods-in-another-applescript](https://stackoverflow.com/questions/2606136/import-applescript-methods-in-another-applescript)但是我想我会把所有的函数都写在 1 个脚本里面，并找出如何调用具体的函数。

我说的函数也指处理程序和过程。多亏了开源，我遇到了这个片段[编剧](https://github.com/chockenberry/Scriptinator/blob/master/Scriptinator/AppDelegate.m)给了我很大的启发。

这是我的[脚本](https://github.com/onmyway133/XcodeWay/blob/master/XcodeWayExtensions/Script/XcodeWayScript.scpt)，包含了很多函数。这里是 [ScriptRunner](https://github.com/onmyway133/XcodeWay/blob/master/XcodeWayExtensions/Helper/ScriptRunner.swift#L28) 解释如何建立`NSAppleEventDescriptor`。注意，你需要`import Carbon`

```
import Carbon

func eventDescriptior(functionName: String) -> NSAppleEventDescriptor {
  var psn = ProcessSerialNumber(highLongOfPSN: 0, lowLongOfPSN: UInt32(kCurrentProcess))
  let target = NSAppleEventDescriptor(
    descriptorType: typeProcessSerialNumber,
    bytes: &psn,
    length: MemoryLayout<ProcessSerialNumber>.size
  )

  let event = NSAppleEventDescriptor(
    eventClass: UInt32(kASAppleScriptSuite),
    eventID: UInt32(kASSubroutineEvent),
    targetDescriptor: target,
    returnID: Int16(kAutoGenerateReturnID),
    transactionID: Int32(kAnyTransactionID)
  )

  let function = NSAppleEventDescriptor(string: functionName)
  event.setParam(function, forKeyword: AEKeyword(keyASSubroutineName))

  return event
} 
```

Enter fullscreen mode Exit fullscreen mode