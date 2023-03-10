# 用 Go 构建一个简单的系统托盘应用程序

> 原文：<https://dev.to/osuka42/building-a-simple-system-tray-app-with-go-899>

[![System tray in go](img/7dd8947ffb7618615496b2f364ce9aaa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6rx1H1eZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v0s37zp20nm6ik5l5itb.png) 
动手:让我们构建一个系统托盘应用程序，显示当前的当地时间，并可以选择其他国家。
我们将使用 [Getlantern](https://getlantern.org) 的库 [systray](https://github.com/getlantern/) 。

```
package main

import (
    "fmt"
    "io/ioutil"

    "github.com/getlantern/systray"
)

func main() {
    systray.Run(onReady, onExit)
}

func onReady() {
    systray.SetIcon(getIcon("assets/clock.ico"))
    systray.SetTitle("I'm alive!")
    systray.SetTooltip("Look at me, I'm a tooltip!")
}

func onExit() {
    // Cleaning stuff here.
}

func getIcon(s string) []byte {
    b, err := ioutil.ReadFile(s)
    if err != nil {
        fmt.Print(err)
    }
    return b
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以马上试试这个代码。唯一能做的就是在系统托盘中显示一个简单的图标消息；仅此而已。让我们分开一点。在 out `main()`中，我们做的唯一一件事是用函数`Run`调用库 systray 的一个新实例，这个函数接收一对参数，在这里我们定义在应用程序退出时做什么。
`SetIcon``SetTitle``SetTooltip`都挺直白的。我们应该考虑的唯一事情是`SetIcon`将我们想要显示为[]字节的图标作为参数。我们使用函数`getIcon`来读取。ico 文件并返回[]字节。

**提示**:找到免费图标的好地方是[图标档案馆](http://www.iconarchive.com/)。

现在，我们不想再有*我还活着！*如题，所以我们要用定时器的本地值来更新每一秒；让我们更新我们的`onReady()`函数，并添加几个函数来帮助我们获得时间值:

```
func onReady() {
    systray.SetIcon(getIcon("assets/clock.ico"))
    for {
        systray.SetTitle(getTime())
        systray.SetTooltip("Look at me, I'm a tooltip!")
        time.Sleep(1 * time.Second)
    }
}

func getTime() string {
    t := time.Now()
    hour, min, sec := t.Clock()
    return ItoaTwoDigits(hour) + ":" + ItoaTwoDigits(min) + ":" + ItoaTwoDigits(sec)
}

// ItoaTwoDigits time.Clock returns one digit on values, so we make sure to convert to two digits
func ItoaTwoDigits(i int) string {
    b := "0" + strconv.Itoa(i)
    return b[len(b)-2:]
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们跑步，我们会看到我们的时钟每秒都在更新；但这不是实现自动更新的最佳方式。实现`time.Sleep()`将让我们暂停当前实例，但是我们不想阻塞我们的主进程。在这种情况下，我们将使用一个 [Go 例程](https://gobyexample.com/goroutines)。

```
go func() {
    for {
    systray.SetTitle(getTime())
    systray.SetTooltip("Look at me, I'm a tooltip!")
        time.Sleep(1 * time.Second)
    }
}() 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候添加一些菜单项了。我们可以用`systray.AddMenuItem("Title", "Tooltip")`轻松做到。这个功能按下后会返回一个频道。所以，我要添加一些像这样的项目。

```
localTime := systray.AddMenuItem("Local time", "Local time")
hcmcTime := systray.AddMenuItem("Ho Chi Minh time", "Asia/Ho_Chi_Minh")
sydTime := systray.AddMenuItem("Sydney time", "Australia/Sydney")
gdlTime := systray.AddMenuItem("Guadalajara time", "America/Mexico_City")
sfTime := systray.AddMenuItem("San Fransisco time", "America/Los_Angeles")
systray.AddSeparator()
mQuit := systray.AddMenuItem("Quit", "Quits this app") 
```

Enter fullscreen mode Exit fullscreen mode

同样，在代码的顶部，让我们添加一种方法来跟踪正在使用的时区:

```
var (
    timezone string
) 
```

Enter fullscreen mode Exit fullscreen mode

并将`systray.SetTitle(getClockTime(timezone))`替换为`systray.SetTitle(getTime())`。
现在，当一个菜单项被按下时，一个获取的方法。

```
go func() {
    for {
        select {
    case <-localTime.ClickedCh:
            timezone = "Local"
        case <-hcmcTime.ClickedCh:
            timezone = "Asia/Ho_Chi_Minh"
        case <-sydTime.ClickedCh:
            timezone = "Australia/Sydney"
        case <-gdlTime.ClickedCh:
            timezone = "America/Mexico_City"
        case <-sfTime.ClickedCh:
            timezone = "America/Los_Angeles"
        case <-mQuit.ClickedCh:
            systray.Quit()
            return
        }
    }
}() 
```

Enter fullscreen mode Exit fullscreen mode

另外，让我们修改一下我们的`getTime`函数，以便与我们的时区选择器更加兼容。

```
func getClockTime(tz string) string {
    t := time.Now()
    utc, _ := time.LoadLocation(tz)

    hour, min, sec := t.In(utc).Clock()
    return ItoaTwoDigits(hour) + ":" + ItoaTwoDigits(min) + ":" + ItoaTwoDigits(sec)
} 
```

Enter fullscreen mode Exit fullscreen mode

**提示**:时区标识符被称为 *IANA 时区标识符*；像那样谷歌一下。你可以在[时间](https://time.is/)内轻松获得你需要的城市的标识符。

所以，我们所有的代码应该看起来像:

```
package main

import (
    "fmt"
    "io/ioutil"
    "strconv"
    "time"

    "github.com/getlantern/systray"
)

var (
    timezone string
)

func main() {
    systray.Run(onReady, onExit)
}

func onReady() {
    timezone = "Local"

    systray.SetIcon(getIcon("assets/clock.ico"))

    localTime := systray.AddMenuItem("Local time", "Local time")
    hcmcTime := systray.AddMenuItem("Ho Chi Minh time", "Asia/Ho_Chi_Minh")
    sydTime := systray.AddMenuItem("Sydney time", "Australia/Sydney")
    gdlTime := systray.AddMenuItem("Guadalajara time", "America/Mexico_City")
    sfTime := systray.AddMenuItem("San Fransisco time", "America/Los_Angeles")
    systray.AddSeparator()
    mQuit := systray.AddMenuItem("Quit", "Quits this app")

    go func() {
        for {
            systray.SetTitle(getClockTime(timezone))
            systray.SetTooltip(timezone + " timezone")
            time.Sleep(1 * time.Second)
        }
    }()

    go func() {
        for {
            select {
            case <-localTime.ClickedCh:
                timezone = "Local"
            case <-hcmcTime.ClickedCh:
                timezone = "Asia/Ho_Chi_Minh"
            case <-sydTime.ClickedCh:
                timezone = "Australia/Sydney"
            case <-gdlTime.ClickedCh:
                timezone = "America/Mexico_City"
            case <-sfTime.ClickedCh:
                timezone = "America/Los_Angeles"
            case <-mQuit.ClickedCh:
                systray.Quit()
                return
            }
        }
    }()
}

func onExit() {
    // Cleaning stuff here.
}

func getClockTime(tz string) string {
    t := time.Now()
    utc, _ := time.LoadLocation(tz)

    hour, min, sec := t.In(utc).Clock()
    return ItoaTwoDigits(hour) + ":" + ItoaTwoDigits(min) + ":" + ItoaTwoDigits(sec)
}

// ItoaTwoDigits time.Clock returns one digit on values, so we make sure to convert to two digits
func ItoaTwoDigits(i int) string {
    b := "0" + strconv.Itoa(i)
    return b[len(b)-2:]
}

func getIcon(s string) []byte {
    b, err := ioutil.ReadFile(s)
    if err != nil {
        fmt.Print(err)
    }
    return b
} 
```

Enter fullscreen mode Exit fullscreen mode

她在哪里

你知道实现这些功能的不同/更好的方法吗？似乎我们正在硬编码时区选择/频道，你能找到优化这部分的方法吗？

你可以在[github.com/Osuka42g/simple-clock-systray](http://github.com/Osuka42g/simple-clock-systray)中找到所有准备运行的代码。黑进去。ðŸ