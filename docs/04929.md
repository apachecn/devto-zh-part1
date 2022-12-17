# 本地化到 JavaScript

> 原文：<https://dev.to/foresthoffman/localizing-go-to-javascript>

在为一个副业项目做 Go 后端工作时，我实现了一个定制的模板系统。对于我的项目，我需要能够将 nonce 值传递给我的 JavaScript。我意识到让前端的数据与后端保持同步需要大量的跑腿工作。为了省时省力，我构建了 [`localize`](https://github.com/foresthoffman/localize) 包。

这个包采用预定义的 Go 数据结构，并递归地将其翻译成 JavaScript 原语。被返回的 JavaScript 几乎可以以任何方式使用，但是它被设计成最适合与`html/template`包一起使用。由于`html/template`包支持调用分配给传递给`template.Template.Execute()`函数的数据的函数，模板可以自己启动本地化过程。一旦有了使用本地化包的模板设置，这就是一劳永逸的情况。最好的那种，在我看来。

下面是一个简单的语法示例:

```
import(
    "github.com/foresthoffman/localize"
)

func main() {
    // Generates a new localization map with the provided data.
    dataMap, err := localize.NewMap(
        // This will tell the localizer to assign the data to
        // the "_localData" global JavaScript variable.
        "_localData",
        localize.Data{
            "motd": "Hello world, welcome to a new day!",

            // "nonce" will hold an object with an element with
            // the key, "login", and the value,
            // "LaKJIIjIOUhjbKHdBJHGkhg"
            "nonce": map[string]string{
                "login": "LaKJIIjIOUhjbKHdBJHGkhg",
            },
        },
    )

    // ...proper error handling, data manipulation, etc.
} 
```

Enter fullscreen mode Exit fullscreen mode

EDIT (08/11/19): [`test/template.go`](https://github.com/foresthoffman/localize/blob/master/test/template.go) 提供了一个在实际的 HTTP 服务器上使用`localize`的非常方便的例子。

就是这样！:)