# 比较 Java，Go 和 Rust 中的错误处理。

> 原文：<https://dev.to/legolord208/comparing-error-handling-in-java-go-and-rust>

错误检查无处不在。有时候比其他时候更复杂。以文件管理为例。你必须记住每次回来都要关闭文件...如果不是自动的。

我们来看看 Java 是怎么做到的。首先，我们来看看 Java 6-。

```
BufferedReader reader;
try {
    reader = new BufferedReader(new FileInputStream("test.txt"));
    // read file
    if (condition) {
        // closed by finally block
        return;
    }

    // closed by finally block
} catch(Exception e) {
    // do proper error handling
    // closed by finally block
} finally {
    try {
        reader.close();
    } catch(Exception e) {
        // do proper error handling
    }
}

System.out.println("File read"); 
```

Enter fullscreen mode Exit fullscreen mode

我的天啊。好吧，太可怕了。我们来看看 Java 7+是怎么做的。

```
try(BufferedReader reader = new BufferedReader(new FileInputStream("test.txt"))) {
    // read file
    if (condition) {
        // automatically closed
        return;
    }

    // automatically closed
} catch(Exception e) {
    // do proper error handling
    // automatically closed
}

System.out.println("File read") 
```

Enter fullscreen mode Exit fullscreen mode

即...非常好。恭喜你，Java。让我们看看围棋是怎么做到的。

```
file, err := os.Open("test.txt") // no semicolon :(
                                 // i love semicolons :(
if err != nil {
    // do proper error handling
}
defer file.Close()
// read file
if condition {
    // closed by defer
    return
}

fmt.Println("File read")
// File closed on return, so you probably wanna return ASAP 
```

Enter fullscreen mode Exit fullscreen mode

关于 Go 有一点需要注意:独立的错误处理。你不能处理所有聚集在一起的错误。这很好。这让你可以具体说明到底哪里出了问题。Go 中的错误也是字符串。你犯了一个带有错误的自定义错误。新的(“哦不”)。好东西
现在我们来看看 Rust。

```
{ // Start a new scope. You don't *have* to do this
    let file = match File::open("test.txt") {
        Ok(file) => file,
        Err(err) => {
            // do proper error handling
            return;
        }
    };
    // read file
    if condition {
        // out of scope - automatically closed
        return;
    }

    // out of scope - automatically closed
}

println!("File read"); 
```

Enter fullscreen mode Exit fullscreen mode

写完这个，我不得不再看一遍代码，心想“就是这样”。
如你所见，我显然最喜欢 Rust's。Java 7 的也不错。Go 是好的，但是 defer 语句只在函数端执行，而不是基于作用域的。你当然可以手动关闭它，但那也很糟糕。
另一方面，Go 错误类型很容易处理。`errors.New("custom error")`令人惊叹。Java 的不算太差，但是 Rust 的对于我们这些懒人来说是“最差”的，但也是最强大的。另外，有一些宏可以修复这个问题。