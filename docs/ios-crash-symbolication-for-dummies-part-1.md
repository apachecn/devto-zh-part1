# iOS 碰撞象征的假人第 1 部分

> 原文：<https://dev.to/fishmanalex/ios-crash-symbolication-for-dummies-part-1>

许多开发人员使用 [Bugsee](https://www.bugsee.com) 是因为它强大的崩溃报告功能。事实上，就报告的准确性和细节数量而言，Bugsee 崩溃报告最近在所有 iOS 崩溃报告服务中[排名最高](http://www.crashprobe.com/ios/)。然而，Bugsee 并没有就此停止，它还提供了崩溃前的用户操作、控制台日志和网络流量的视频。

在接下来的一系列文章中，我们实际上将关注崩溃日志本身，解释它背后的魔力，并展示如何正确设置它。

本系列的第一篇文章是介绍性的。

## 什么是象征？

为了回答这个问题，我们必须简单地谈谈构建过程本身。不管我们的项目是用什么语言编写的(Objective C、Swift 或其他语言)，构建过程都会将我们人类可读的代码翻译成机器二进制代码。考虑下面的错误代码(你能发现这个错误吗？).

```
void initialize() {
     array = @[@"one", @"two", @"three"];
}

NSNumber* getElementFromArray(int index) {
    return array[index];
}

void printAllElements() {
    for (int i = 0; i <= 3; i++) {
        NSLog(@"%@", getElementFromArray(i));
    }
} 
```

建成后它最终会变成这样:

```
0x100117dec: stp    x29, x30, [sp, #-16]!     ; <--- Start of the initialize() method
<...skipped...>
0x100117e9c: ldp    x29, x30, [sp], #16
0x100117ea0: ret    
0x100117ea4: bl     0x10022d83c
0x100117ea8: stp    x29, x30, [sp, #-16]!     ; <--- Start of the printAllElements() method
0x100117eac: mov    x29, sp
0x100117eb0: sub    sp, sp, #32                
0x100117eb4: stur   wzr, [x29, #-4]
0x100117eb8: ldur   w8, [x29, #-4]
0x100117ebc: cmp    w8, #3                    
0x100117ec0: b.gt   0x100117f08               
0x100117ec4: ldur   w0, [x29, #-4]
0x100117ec8: bl     0x100117f14               ; <---- this is where it calls getElementFromArray()
0x100117ecc: mov    x29, x29
0x100117ed0: bl     0x10022d668               
<...skipped...>
0x100117f0c: ldp    x29, x30, [sp], #16
0x100117f10: ret    
0x100117f14: stp    x29, x30, [sp, #-16]!     ; <--- Start of getElementFromArray() method
0x100117f18: mov    x29, sp
0x100117f1c: sub    sp, sp, #16               
0x100117f20: adrp   x8, 436
0x100117f24: add    x8, x8, #2520              
0x100117f28: adrp   x9, 452
0x100117f2c: add    x9, x9, #1512             
0x100117f30: stur   w0, [x29, #-4]
0x100117f34: ldr    x9, [x9]
0x100117f38: ldursw x2, [x29, #-4]
0x100117f3c: ldr    x1, [x8]
0x100117f40: mov    x0, x9
0x100117f44: bl     0x10022d608               ; <--- Here we send message to NSArray to retrive that element
0x100117f48: mov    sp, x29
0x100117f4c: ldp    x29, x30, [sp], #16
0x100117f50: ret 
```

正如你从这个例子中看到的，构建过程去掉了所有的符号(变量和方法名)，它也不知道任何关于我们代码布局的信息，如果我们用空格来分隔函数，所有的信息都丢失了。所以现在当崩溃发生时(它将会发生，毕竟我们访问了数组边界之外的元素)，如果我们没有正确设置符号，这是我们最终得到的唯一崩溃信息:

```
NSRangeException: *** -[__NSArrayI objectAtIndex:]: index 3 beyond bounds [0 .. 2]
0 CoreFoundation      0x1857A51B8
1 libobjc.A.dylib     0x1841DC55C
2 CoreFoundation      0x1856807F4
3 MyApplication       0x100117f48
4 MyApplication       0x100117ecc
5 ... 
```

这是相当原始的，不是很有用。我们知道它在 CoreFoundation 系统方法中的某个方法中失败了，而这个方法又是从 libobjc 中的某个方法调用的。A.dylib，它又被 CoreFoundation 中的另一个方法调用，后者又被我们的应用程序调用(最后！).但是 0x100117f48 是什么？具体在哪里？是什么文件，函数还是行号？这正是象征出现的地方。

符号化是将返回地址翻译回人类可读的方法/文件名和行号的过程。

成功的符号化将生成以下报告:

```
NSRangeException: *** -[__NSArrayI objectAtIndex:]: index 3 beyond bounds [0 .. 2]
0 CoreFoundation      __exceptionPreprocess + 124
1 libobjc.A.dylib     objc_exception_throw + 52
2 CoreFoundation      -[__NSArrayI objectAtIndex:] + 180
3 MyApplication       getElementFromArray (MyFile.m:22)
4 MyApplication       printAllElements (MyFile.m:27) 
```

现在很明显，崩溃是由 MyFile.m 的第 22 行中的一些不正确的数组访问引起的，这些访问恰好在 getElementsArray 方法中。如果我们需要更多的上下文，我们可以很容易地看到在同一文件的第 27 行 printAllElements 调用了这个。

## 什么是 dSYM 文件？

幸运的是，XCode 可以被指示保留在构建过程中丢失的大量数据。它可以把它放在应用程序内部，但这不是一个好主意。我们不希望我们的应用程序带有所有这些额外的调试信息，这将使我们的竞争对手和黑客很容易对应用程序进行逆向工程。我们想让它生成，但保持在苹果商店。这正是 dSYM 文件的意义所在。在构建过程中，XCode 从主可执行文件中剥离所有调试信息，并将其放在一个名为 dSYM 的特殊文件中。这有助于保持我们的可执行文件较小，更容易分发给满意的客户。

如果我们的应用程序使用框架，那么产品文件夹将为每个构建的框架生成一个单独的 dSYM 文件。最终，如果我们想要覆盖我们的基础，并能够在我们的应用程序中的每个可能的位置象征性地显示一次碰撞，那么所有这些都是需要的。

不用说，在构建应用程序的特定版本时生成的 dSYM 文件只能用于表示特定版本的崩溃。dSYM 文件由一个唯一的 ID (UUID)来标识，每次我们修改和重建代码时，这个 ID 都会改变，这个 ID 用来将一个符号文件与一个特定的崩溃进行匹配。dSYM 可能与多个 UUID 相关联，因为它可能包含多个体系结构的调试信息。

使用 dwarfdump 命令可以轻松检索 dSYM 的 UUID:

```
$ dwarfdump -u MyApplication.app.dSYM
UUID: 9F665FD6-E70C-3EB9-8622-34FD9EC002CA (armv7) MyApplication.app.dSYM
UUID: 8C2F9BB8-BB3F-37FE-A83E-7F2FF7B98889 (arm64) MyApplication.app.dSYM 
```

上面的 dSYM 有我们的应用程序的 arm7 和 arm64 版本的调试信息，每个版本都有自己的 UUID。

这些 dSYM 文件可以而且应该手动存储，以便将来在生产版本中作为崩溃的象征。或者，它们可以上传到 Bugsee 之类的崩溃报告服务，在那里它们将被放在一个 vault 中，并最终用于处理特定构建的崩溃。通常，一个特殊的[构建阶段](https://docs.bugsee.com/sdk/ios/symbolication/)被添加到负责将 dSYM 文件上传到 vault 的构建过程中。

## iOS 崩溃期间会发生什么？

在崩溃期间，设备上正在收集以下信息:

*   崩溃/异常类型和异常特定消息(如果可用)
*   每个线程的堆栈跟踪(原始形式，我们之前看到的这些不可读的返回地址的列表)
*   所有图像的列表(应用程序加载的用户和系统框架和扩展。每个都有一个唯一的 UUID 来帮助它匹配正确的 dSYM 文件)
*   关于具体的构建、设备、崩溃时间等的其他信息。这些与象征过程不太相关，但仍然很重要。

该信息被发送到崩溃报告服务进行处理，在那里它将与在构建时已经上载的适当的 dSYM 文件相匹配，或者将在以后手动上载。象征过程发生在服务器上，并产生一个漂亮的，人类可读的崩溃报告，可以通过 web 仪表板查看或作为文件下载。该报告通常包括上面列出的项目(基本信息、崩溃/异常详细信息和符号化堆栈跟踪，前提是所有星号都对齐，并且所有符号文件都已正确上传和处理。

这就是典型的崩溃报告服务所提供的。Bugsee 提供的远不止这些，举几个例子，使用 Bugsee，这些报告还包括一个交互式播放器，可以同步播放以下内容:

*   崩溃前的屏幕和用户交互视频
*   网络流量，具有完整的请求和响应头和正文
*   系统和应用程序跟踪(磁盘空间、cpu 负载、顶视图和窗口名称等。)
*   您的自定义跟踪
*   控制台日志

这提供了更多的上下文，在试图调试一个只有现场客户才会遇到的回避性问题时，这些上下文会有很大的帮助。

在本系列的下一篇文章中，我们将深入探讨符号化过程本身，并展示如何手动符号化一个地址或一份完整的苹果崩溃报告。