# 在 Go with os/exec 中执行高级命令

> 原文：<https://dev.to/kjk/advanced-command-execution-in-go-with-osexec>

Go 对执行外部程序有很好的支持。让我们从头开始。

## 运行命令并捕获输出

下面是运行`ls -lah`并捕获其组合 stdout/stderr 的最简单方法。

```
func main() {
    cmd := exec.Command("ls", "-lah")
    out, err := cmd.CombinedOutput()
    if err != nil {
        log.Fatalf("cmd.Run() failed with %s\n", err)
    }
    fmt.Printf("combined out:\n%s\n", string(out))
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[高级执行/01-简单执行. go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/01-simple-exec.go) 。

## 分别捕获 stdout 和 stderr

如果您也想这样做，但是要分别捕获 stdout 和 stderr，该怎么办呢？

```
func main() {
    cmd := exec.Command("ls", "-lah")
    var stdout, stderr bytes.Buffer
    cmd.Stdout = &stdout
    cmd.Stderr = &stderr
    err := cmd.Run()
    if err != nil {
        log.Fatalf("cmd.Run() failed with %s\n", err)
    }
    outStr, errStr := string(stdout.Bytes()), string(stderr.Bytes())
    fmt.Printf("out:\n%s\nerr:\n%s\n", outStr, errStr)
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[advanced-exec/02-capture-stdout-stderr . go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/02-capture-stdout-stderr.go)。

## 捕捉输出同时也显示进度

如果命令需要很长时间才能完成呢？

如果除了捕获 stdout/stderr 之外，还能在控制台上看到它的进度，那就太好了。

这有点复杂，但并不可怕。

```
func copyAndCapture(w io.Writer, r io.Reader) ([]byte, error) {
    var out []byte
    buf := make([]byte, 1024, 1024)
    for {
        n, err := r.Read(buf[:])
        if n > 0 {
            d := buf[:n]
            out = append(out, d...)
            os.Stdout.Write(d)
        }
        if err != nil {
            // Read returns io.EOF at the end of file, which is not an error for us
            if err == io.EOF {
                err = nil
            }
            return out, err
        }
    }
    // never reached
    panic(true)
    return nil, nil
}

func main() {
    cmd := exec.Command("ls", "-lah")
    var stdout, stderr []byte
    var errStdout, errStderr error
    stdoutIn, _ := cmd.StdoutPipe()
    stderrIn, _ := cmd.StderrPipe()
    cmd.Start()

    go func() {
        stdout, errStdout = copyAndCapture(os.Stdout, stdoutIn)
    }()

    go func() {
        stderr, errStderr = copyAndCapture(os.Stderr, stderrIn)
    }()

    err := cmd.Wait()
    if err != nil {
        log.Fatalf("cmd.Run() failed with %s\n", err)
    }
    if errStdout != nil || errStderr != nil {
        log.Fatalf("failed to capture stdout or stderr\n")
    }
    outStr, errStr := string(stdout), string(stderr)
    fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[advanced-exec/03-live-progress-and-capture-v1 . go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/03-live-progress-and-capture-v1.go)

## 捕捉输出但也显示进度#2

以前的解决方案有效，但是`copyAndCapture`看起来像是我们在重新实现`io.Copy`。感谢 Go 对接口的使用，我们可以重用`io.Copy`。

我们将编写实现`io.Writer`接口的`CapturingPassThroughWriter`结构。它将捕获写入其中的所有内容，并将其写入底层的`io.Writer`。

```
// CapturingPassThroughWriter is a writer that remembers
// data written to it and passes it to w
type CapturingPassThroughWriter struct {
    buf bytes.Buffer
    w io.Writer
}

// NewCapturingPassThroughWriter creates new CapturingPassThroughWriter
func NewCapturingPassThroughWriter(w io.Writer) *CapturingPassThroughWriter {
    return &CapturingPassThroughWriter{
        w: w,
    }
}

func (w *CapturingPassThroughWriter) Write(d []byte) (int, error) {
    w.buf.Write(d)
    return w.w.Write(d)
}

// Bytes returns bytes written to the writer
func (w *CapturingPassThroughWriter) Bytes() []byte {
    return w.buf.Bytes()
}

func main() {
    var errStdout, errStderr error
    cmd := exec.Command("ls", "-lah")
    stdoutIn, _ := cmd.StdoutPipe()
    stderrIn, _ := cmd.StderrPipe()
    stdout := NewCapturingPassThroughWriter(os.Stdout)
    stderr := NewCapturingPassThroughWriter(os.Stderr)
    err := cmd.Start()
    if err != nil {
        log.Fatalf("cmd.Start() failed with '%s'\n", err)
    }

    go func() {
        _, errStdout = io.Copy(stdout, stdoutIn)
    }()

    go func() {
        _, errStderr = io.Copy(stderr, stderrIn)
    }()

    err = cmd.Wait()
    if err != nil {
        log.Fatalf("cmd.Run() failed with %s\n", err)
    }
    if errStdout != nil || errStderr != nil {
        log.Fatalf("failed to capture stdout or stderr\n")
    }
    outStr, errStr := string(stdout.Bytes()), string(stderr.Bytes())
    fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[advanced-exec/03-live-progress-and-capture-v2 . go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/03-live-progress-and-capture-v2.go)

## 捕捉输出但也显示进度#3

原来 Go 的标准库实现了 [io。多作家](https://golang.org/pkg/io/#MultiWriter)，这是更通用的版本`CapturingPassThroughWriter`。让我们用这个来代替:

```
func main() {
    var stdoutBuf, stderrBuf bytes.Buffer
    cmd := exec.Command("ls", "-lah")

    stdoutIn, _ := cmd.StdoutPipe()
    stderrIn, _ := cmd.StderrPipe()

    var errStdout, errStderr error
    stdout := io.MultiWriter(os.Stdout, &stdoutBuf)
    stderr := io.MultiWriter(os.Stderr, &stderrBuf)
    err := cmd.Start()
    if err != nil {
        log.Fatalf("cmd.Start() failed with '%s'\n", err)
    }

    go func() {
        _, errStdout = io.Copy(stdout, stdoutIn)
    }()

    go func() {
        _, errStderr = io.Copy(stderr, stderrIn)
    }()

    err = cmd.Wait()
    if err != nil {
        log.Fatalf("cmd.Run() failed with %s\n", err)
    }
    if errStdout != nil || errStderr != nil {
        log.Fatal("failed to capture stdout or stderr\n")
    }
    outStr, errStr := string(stdoutBuf.Bytes()), string(stderrBuf.Bytes())
    fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)

} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[advanced-exec/03-live-progress-and-capture-v3 . go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/03-live-progress-and-capture-v3.go)

能自己写代码就不错了，能很好的了解标准库就更好了！

## 写入程序的标准输入

我们知道如何读取程序的标准输出，但我们也可以写入它的标准输入。

没有 Go 库做 bzip2 压缩(标准库只有解压缩)。

我们可以使用`bzip2`通过以下方式进行压缩:

*   将数据写入临时文件
*   调用`bzip2 -c ${file_in}`并捕获它的标准输出

如果我们不必创建临时文件，那就更好了。

大多数压缩程序在 stdin 上接受要压缩/解压缩的数据。

为了在命令行上实现这一点，我们将使用下面的命令:`bzip2 -c <${file_in} >${file_out}`。

围棋里也是这样:

```
// compress data using bzip2 without creating temporary files
func bzipCompress(d []byte) ([]byte, error) {
    var out bytes.Buffer
// -c : compress
    // -9 : select the highest level of compresion
    cmd := exec.Command("bzip2", "-c", "-9")
    cmd.Stdin = bytes.NewBuffer(d)
    cmd.Stdout = &out
    err := cmd.Run()
    if err != nil {
        return nil, err
    }
    return out.Bytes(), nil
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[advanced-exec/06-feed-stdin . go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/06-feed-stdin.go)。

我们也可以调用`cmd.StdinPipe()`，它返回`io.WriteCloser`。它更复杂，但能更好地控制写作。

## 改变执行程序的环境

关于在 Go 中使用环境变量你需要知道的事情:

*   `os.Environ()`返回`[]string`，其中每个字符串以`FOO=bar`的形式表示，其中`FOO`是环境变量的名称，`bar`是值
*   `os.Getenv("FOO")`返回环境变量的值。

有时候你需要修改执行程序的环境。

您可以通过将`exec.Cmd`的`Env`成员设置为与`os.Environ()`相同的格式来实现。通常你不想构建一个全新的环境，而是通过你自己的环境增加更多的变量:

```
 cmd := exec.Command("programToExecute")
    additionalEnv := "FOO=bar"
    newEnv := append(os.Environ(), additionalEnv))
    cmd.Env = newEnv
    out, err := cmd.CombinedOutput()
    if err != nil {
        log.Fatalf("cmd.Run() failed with %s\n", err)
    }
    fmt.Printf("%s", out) 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[advanced-exec/05-change-environment . go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/05-change-environment.go)。

软件包 [shurcooL/go/osutil](https://godoc.org/github.com/shurcooL/go/osutil#Environ) 提供了稍微文明一点的操作环境变量的方式。

## 及早检查某个程序是否安装

假设你写了一个需要很长时间运行的程序。你在最后调用可执行文件`foo`来执行一些重要的任务。

如果`foo`可执行文件不存在，调用将失败。

在程序开始时检测到这一点，并使用描述性的错误消息尽早失败，这是一个好主意。

可以用`exec.LookPath`来做。

```
func checkLsExists() {
    path, err := exec.LookPath("ls")
    if err != nil {
        fmt.Printf("didn't find 'ls' executable\n")
    } else {
        fmt.Printf("'ls' executable is in '%s'\n", path)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

完整示例:[advanced-exec/04-check-exe-exists . go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/04-check-exe-exists.go)。

检查程序是否存在的另一种方法是尝试在无操作模式下执行(例如，许多程序支持`--help`选项)。

* * *

这篇文章是[围棋食谱](https://blog.kowalczyk.info/book/go-cookbook.html)的一部分