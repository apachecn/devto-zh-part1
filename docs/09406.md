# 命令行

> 原文：<https://dev.to/vknabel/commandmine-24j>

Command mine 是一个 swift 库的概念，用于解析命令行参数。它被设计成支持 CLIS 的异步实现，甚至可以在框架内部使用。

## 定义

CommandMine 就是从矿石中提取矿物质。

```
let goldmine = Mine<Gold>() // declare your mine
 .drift( // One way to get to your gold
  named: "init",
  digging drift: Drift, // Prepares your Shaft
  to shaft: execute // your actual program
 ) 
```

Enter fullscreen mode Exit fullscreen mode

### 轴

描述 *Rails* 工厂的协议。

### 矿井

你的程序。是一个轴。

### 漂移

一种特殊形式的竖井，将原矿(`[String]`)分解成矿物。

### 矿石

传递给程序的参数。

### 绝杀

围绕矿物的事件包装。

### 矿物

你想要的矿石。

### 轨道

传送你充满的知识。*传说*中*可观测量*的简单类型别名。

### 电梯

与外界最快的连接。打印和读取线周围的 Rx 包装。

电梯背后的想法是使您的 CLI 可以作为库嵌入，无需任何更改。

```
let ele = Elevator<String, String>()
ele.onNext(.error("")) //.success("")

Elevator. 
```

Enter fullscreen mode Exit fullscreen mode

## 复用性

CommandMine 试图让你的 CLI 独立于 STDIO，可以异步使用。

此外，保持部分代码的可重用性和可替换性也很重要:您的 CLI 可能会不断发展。

### 框架支持

当涉及到内部或更高级的工具时，框架比普通的 CLI 更适合，因为它消除了在您的路径中处理另一个二进制文件的需要，您的用户需要安装并保持更新。相反，它将在您自己的目标中编译。

为了将你的项目分割成一个框架和 CLI，你只需在`YourMine`中声明你的矿并漂移，其他的都在`YourShaft`中，在你的可执行文件的`main.swift`中你只需启动你的矿。

> *提示:*你可以用`mine init`创建这个项目布局。

```
import YourMine
yourMine.runMain() 
```

Enter fullscreen mode Exit fullscreen mode

因此,`YourShaft`对那些想使用你的逻辑提供一个完整的新 CLI 的人，或者对非 CLI 的人都有好处。起初，将你的矿和巷道导出到`YourMine`看起来很笨拙，但是它实际上可以帮助其他人将你的项目作为子命令嵌入(毕竟所有的矿都是复杂的竖井)或者只是重用一个巷道。

## 总结

*   `Mine`:你的 CLI，任务参数
*   `Drift`:解析轴的参数，同步
*   `Cage`:选项
*   `Shaft`:异步命令
*   `Elevator`:用户反馈

一个`Drift`是一个`Observable`工厂，在其帮助下发出一个`Lore`或一个错误。

```
import CommandMine

let main = Shaft(named: "rock", summary: "")
 .drift(named: "init", explainedBy: "Creates a new project") { (cage: EmptyLore) in
  return initObsi
 }

main.run { result in
 switch result {
 case .success(_), .usage(_, _), .error(_):
  break
 }
}

Shaft.name
Shaft.instructions
Shaft.drifts

enum MineResult<A> {
 case success(A)
 case usage(String?, String?)
 case error(Error) // thrown errors will be inserted
}
final class/struct Drift<Arguments, Result>: Drifty {
 let bindTo: (Arguments) -> Observable<MineEvent<Result>>
}

Drifty.map
Drifty.flatMap

final class/struct Shaft<A>: Drifty {
 let usage: (String?, String?)
 let rootDrift: Drifts<[String], A>
}
extension Shaft {
 init(named: String, instructions: String? = nil)
 func drift<Cage>(named: String, instructions: String? = nil, cage: Cage.Type = Cage.self, _: @escaping Drift<Cage, A>) -> Shaft<A>
}

extension Drift {
 static func positional(parameter: @escaping (String) -> Observable<A>) -> [String] -> Observable<MineResult<([String], A)>> {
  return { args in
   guard let arg = args.first else {
    return .of(.usage(nil, nil))
   }
   return Jfkf
  }
 }

 static func exhaustive()
 func optional()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

在 Swift 中建模 CLI 的另一个概念是 [ArgumentOverture](https://vknabel.com/pages/ArgumentOverture) ，它对实际程序的影响要小得多。

这个概念可能太大，太难适应它将提供的好处。虽然名字挺好听的。😅