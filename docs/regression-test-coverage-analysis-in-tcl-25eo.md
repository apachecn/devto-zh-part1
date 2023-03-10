# TCL 回归测试覆盖分析

> 原文：<https://dev.to/highperformancecoder/regression-test-coverage-analysis-in-tcl-25eo>

如果你像我一样，你喜欢有大量的回归测试来防止你在编写一些复杂的代码时犯愚蠢的错误。虽然代码覆盖工具存在于主要的开发环境中，但是一个主要的盲点是如何进行 TCL 的覆盖分析，当你的应用程序(例如 [Minsky](http://minsky.sf.net) )开始使用大量的 TCL 应用程序逻辑时，这就成了一个问题。

快速搜索表明，你可以购买主动的 TCL 做事方式(对我来说不是很有用)，或者使用名为 [Nagelfar](https://sourceforge.net/projects/nagelfar) 的应用程序。不幸的是，Nagelfar 真的假设你是在一个标准的环境中编码，比如 wish 或者 tclsh，而不是在一个应用程序脚本环境中，比如 Minsky 或者 [EcoLab](http://ecolab.sf.net) 。然后意识到我可以用 TCL 相当简单地做到这一点。好吧，我确实有一些错误的转折，这需要时间，但我发现我可以附加一个命令来触发 TCL 中执行的每一步。然后，我查看直接封闭的堆栈框架，查看细节，比如我正在执行哪一行，并将这些保存到数据库中。因为我是在 EcoLab 环境中这样做的，所以我使用 cachedDBM 类来累积执行计数作为它们的发现。最后，我编写了一个 C++程序，该程序读入一个 TCL 文件，识别我所在的过程，检查数据库中是否有该过程的条目或文件行号，并生成与 gcov 类似的输出，其中###表示没有执行的行。

C++代码被称为 tcl-cov，目前位于 Minsky 的测试目录中，尽管我正在考虑将它移到 ecolab utilities 目录中。

要添加到主应用程序中的 TCL 代码？这里是:

```
proc attachTraceProc {namesp} {
    foreach p [info commands $namesp*] {
        if {$p ne "::traceProc"} {
            trace add execution $p enterstep traceProc
        }
    }
    # recursively process child namespaces
    foreach n [namespace children $namesp] {
        attachTraceProc ${n}::
    }
}`

# check whether coverage analysis is required 
if [info exists env(MINSKY\_COV)] {  
# trace add execution proc leave enableTraceProc  
 proc traceProc {args} {  
 array set frameInfo [info frame -2]  
 if {$frameInfo(type)=="proc"} {  
 minsky.cov.add $frameInfo(proc) $frameInfo(line)  
 }  
 if {$frameInfo(type)=="source"} {  
 minsky.cov.add $frameInfo(file) $frameInfo(line)  
 }  
 }  
# open coverage database, and set cache size  
 minsky.cov.init $env(MINSKY\_COV) w  
 minsky.cov.max\_elem 10000  
# attach trace execuation to all created procs  
 attachTraceProc ::  
} 
```

coverage 数据库的名称通过 MINSKY_COV 环境变量传递。minsky.cov.add 是一个将文件/行或 proc/line 的计数器加 1 的命令。traceProc 命令被附加到所有已定义的过程，这需要遍历所有名称空间，因此递归调用 attachTraceProc(从全局名称空间::)开始。

就是这样！享受吧。