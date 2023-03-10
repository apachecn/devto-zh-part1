# Swift 中流程类的助手。

> 原文：<https://dev.to/masakihori/helpers-for-process-class-in-swift-e68>

```
Process() <<< "/bin/echo" <<< ["Hello, World.\nこんにちは世界。"]
    | Process() <<< "/usr/bin/grep" <<< ["Hello"]
    >>> { output in

    output.string.map { print($0) }    // Hello, World.
} 
```

Enter fullscreen mode Exit fullscreen mode

下面的帮手。

```
/// 出力を簡単に扱うための補助的な型。FileHandleを隠蔽する。
struct Output {

    private let fileHandle: FileHandle

    init(fileHandle: FileHandle) {
        self.fileHandle = fileHandle
    }

    var data: Data {
        return fileHandle.readDataToEndOfFile()
    }

    var string: String? {
        return String(data: data, encoding: .utf8)
    }

    var lines: [String] {
        return string?.components(separatedBy: "\n") ?? []
    }
}

precedencegroup ArgumentPrecedence {

    associativity: left
    higherThan: AdditionPrecedence
}
infix operator <<< : ArgumentPrecedence

/// Processにexecutable pathを設定する。
func <<< (lhs: Process, rhs: String) -> Process {

    lhs.executableURL = URL(fileURLWithPath: rhs)
    return lhs
}

/// Processに引数を設定する。
func <<< (lhs: Process, rhs: [String]) -> Process {

    lhs.arguments = rhs
    return lhs
}

/// Processをパイプする。
func | (lhs: Process, rhs: Process) -> Process {

    let pipe = Pipe()

    lhs.standardOutput = pipe
    rhs.standardInput = pipe
    lhs.launch()

    return rhs
}

precedencegroup RedirectPrecedence {

    associativity: left
    lowerThan: LogicalDisjunctionPrecedence
}
infix operator >>> : RedirectPrecedence

/// Processの出力をOutput 型で受け取り加工などができる。
/// ジェネリクスを利用しているのでどのような型にでも変換して返せる。
func >>> <T>(lhs: Process, rhs: (Output) -> T) -> T {

    let pipe = Pipe()
    lhs.standardOutput = pipe
    lhs.launch()

    return rhs(Output(fileHandle: pipe.fileHandleForReading))
} 
```

Enter fullscreen mode Exit fullscreen mode