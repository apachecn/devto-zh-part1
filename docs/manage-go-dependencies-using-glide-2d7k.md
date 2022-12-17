# 使用 Glide 管理 Go 依赖项

> 原文：<https://dev.to/ykyuen/manage-go-dependencies-using-glide-2d7k>

*最初发布在[水手长博客](https://blog.boatswain.io/post/manage-go-dependencies-using-glide/)上。*

* * *

**更新@ 2018-01-17**

你可能想考虑使用一个新的依赖管理工具，叫做 [dep](https://github.com/golang/dep) ，正如 [nasa9084](https://dev.to/nasa9084/comment/2317) 所建议的。

* * *

[Glide](https://glide.sh/) 是一个 [Go](https://golang.org/) 包管理工具，类似于 [NodeJS](https://nodejs.org/en/) 中的 [npm](https://www.npmjs.com/) 。让我们创建一个简单的例子，看看它是如何工作的。

## 在 GOPATH 中创建项目

为了解析 [Go](https://golang.org/) 包路径，项目文件夹必须在 *$GOPATH* 下。在这个例子中，项目的根文件夹是*$ GOPATH/src/git lab . com/yk yuen/glide-example*。

## 一个 hello world 围棋程序

让我们创建一个简单的 [Go](https://golang.org/) 程序。

`main.go`

```
package main

import "fmt"

func main() {
  fmt.Println("hello world")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 构建并运行

```
[ykyuen@camus glide-example]$ go build -o hello
[ykyuen@camus glide-example]$ ./hello
hello world 
```

Enter fullscreen mode Exit fullscreen mode

## 添加一个 Go 依赖项

dustin/go-humanize 是一个有用的 [Go](https://golang.org/) 包，用于格式化数字、字符串和时间。让我们更新我们的[围棋](https://golang.org/)程序如下。

`main.go`

```
package main

import humanize "github.com/dustin/go-humanize"
import "fmt"

func main() {
  fmt.Println("hello world")
  fmt.Printf("That file is %s.\n", humanize.Bytes(82854982)) // That file is 83 MB.
  fmt.Printf("You're my %s best friend.\n", humanize.Ordinal(193)) // You are my 193rd best friend.
  fmt.Printf("You owe $%s.\n", humanize.Comma(6582491)) // You owe $6,582,491.
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们尝试构建它，它将抛出以下错误。

```
[ykyuen@camus glide-example]$ go build -o hello
main.go:3:8: cannot find package "github.com/dustin/go-humanize" in any of:
  /home/ykyuen/tools/go1.8.4/src/github.com/dustin/go-humanize (from $GOROOT)
  /home/ykyuen/go/src/github.com/dustin/go-humanize (from $GOPATH) 
```

Enter fullscreen mode Exit fullscreen mode

这是因为我们还没有下载 [dustin/go-humanize](https://github.com/dustin/go-humanize) 。不使用 [Glide](https://glide.sh/) ，我们可以通过运行 *go get* 命令来解决这个构建错误。

```
go get github.com/dustin/go-humanize 
```

Enter fullscreen mode Exit fullscreen mode

但是在这种情况下，任何签出项目的人都不知道构建程序需要什么包。如果您已经运行了 *go get* 命令，请在继续之前删除该包。

## 滑翔道

### 创建 glide.yaml

*glide.yaml* 可以由 *glide create* 命令生成。它将扫描 [Go](https://golang.org/) src 文件，并包含所有需要的依赖项。下面是这个例子的输出。

```
[ykyuen@camus glide-example]$ glide create
[INFO]  Generating a YAML configuration file and guessing the dependencies
[INFO]  Attempting to import from other package managers (use --skip-import to skip)
[INFO]  Scanning code to look for dependencies
[INFO]  --> Found reference to github.com/dustin/go-humanize
[INFO]  Writing configuration file (glide.yaml)
[INFO]  Would you like Glide to help you find ways to improve your glide.yaml configuration?
[INFO]  If you want to revisit this step you can use the config-wizard command at any time.
[INFO]  Yes (Y) or No (N)?
Y
[INFO]  Looking for dependencies to make suggestions on
[INFO]  --> Scanning for dependencies not using version ranges
[INFO]  --> Scanning for dependencies using commit ids
[INFO]  Gathering information on each dependency
[INFO]  --> This may take a moment. Especially on a codebase with many dependencies
[INFO]  --> Gathering release information for dependencies
[INFO]  --> Looking for dependency imports where versions are commit ids
[INFO]  No proposed changes found. Have a nice day. 
```

Enter fullscreen mode Exit fullscreen mode

`glide.yaml`

```
package: gitlab.com/ykyuen/glide-example
import:
- package: github.com/dustin/go-humanize 
```

Enter fullscreen mode Exit fullscreen mode

### 将 Go 依赖项下载到供应商文件夹

接下来，我们执行 *glide install* 命令来下载依赖项。

```
[ykyuen@camus glide-example]$ glide install
[INFO]  Lock file (glide.lock) does not exist. Performing update.
[INFO]  Downloading dependencies. Please wait...
[INFO]  --> Fetching updates for github.com/dustin/go-humanize
[INFO]  Resolving imports
[INFO]  Downloading dependencies. Please wait...
[INFO]  Setting references for remaining imports
[INFO]  Exporting resolved dependencies...
[INFO]  --> Exporting github.com/dustin/go-humanize
[INFO]  Replacing existing vendor dependencies
[INFO]  Project relies on 1 dependencies. 
```

Enter fullscreen mode Exit fullscreen mode

这将生成 *glide.lock* 文件和包含 [dustin/go-humanize](https://github.com/dustin/go-humanize) 包的 *vendor* 文件夹。 *glide.lock* 文件包含包的版本。它可能是一个**标签**或者一个 **git 提交散列**。

`glide.lock`

```
hash: de8aada0e0453f13dc438db5fb412db797d701d6afeadcc052c477fd55e01aa8
updated: 2017-12-04T23:20:53.616704682+08:00
imports:
- name: github.com/dustin/go-humanize
  version: 8929fe90cee4b2cb9deb468b51fb34eba64d1bf0
testImports: [] 
```

Enter fullscreen mode Exit fullscreen mode

### 更新依赖关系的版本

8929fe9 提交已经过时了。我们可以修改 *glide.lock* 文件中的版本，并再次运行 *glide install* 来获得您的软件包的期望版本。

`glide.lock`

```
hash: de8aada0e0453f13dc438db5fb412db797d701d6afeadcc052c477fd55e01aa8
updated: 2017-12-04T23:20:53.616704682+08:00
imports:
- name: github.com/dustin/go-humanize
  version: bb3d318650d48840a39aa21a027c6630e198e626
testImports: [] 
```

Enter fullscreen mode Exit fullscreen mode

```
[ykyuen@camus glide-example]$ glide install
[INFO]  Downloading dependencies. Please wait...
[INFO]  --> Found desired version locally github.com/dustin/go-humanize bb3d318650d48840a39aa21a027c6630e198e626!
[INFO]  Setting references.
[INFO]  --> Setting version for github.com/dustin/go-humanize to bb3d318650d48840a39aa21a027c6630e198e626.
[INFO]  Exporting resolved dependencies...
[INFO]  --> Exporting github.com/dustin/go-humanize
[INFO]  Replacing existing vendor dependencies 
```

Enter fullscreen mode Exit fullscreen mode

### 构建并再次运行

```
[ykyuen@camus glide-example]$ go build -o hello
[ykyuen@camus glide-example]$ ./hello
hello world
That file is 83 MB.
You're my 193rd best friend.
You owe $6,582,491. 
```

Enter fullscreen mode Exit fullscreen mode

### 查看厂商文件夹？

对于 [NodeJS](https://nodejs.org/en/) 项目，通常的做法是我们会忽略代码库中的 *node_modules* 文件夹。另一方面， [Go](https://golang.org/) 社区有不同的观点。

*   我是否应该将“供应商”目录添加到。gitignore 如果我正在使用 glide 或 godep 这样的工具？

没有绝对的答案。选择你觉得更舒服的选项。

### 其他顾虑

当项目变得越来越大时，glide 可能需要相当长的时间才能工作，因为依赖树变得越来越大。此外，很难管理依赖关系的版本。

正如 [nasa9084](https://dev.to/nasa9084/comment/2317) 所建议的，你可能想要考虑使用一种新的依赖管理工具，叫做 [dep](https://github.com/golang/dep) ，正如 [Glide](https://glide.sh/) 团队所建议的。dep 越来越受欢迎，并在积极发展中。请务必查看一下 [dep 的项目状态](https://github.com/golang/dep#current-status)并选择一个您感觉更舒服的项目。

## 总结

*   项目文件夹必须在 *$GOPATH* 下。
*   通过扫描所有源代码，依赖关系将被自动包含。
*   依赖的版本由 *glide.lock* 管理。
*   是否提交*供应商*文件夹仍是一个悬而未决的问题。
*   依赖树变大时的性能问题。
*   完整示例见[gitlab.com](https://gitlab.com/ykyuen/glide-example)。