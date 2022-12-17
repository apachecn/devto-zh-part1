# 死代码问题

> 原文：<https://dev.to/apastuhov/dead-code-problem-3o2>

*最初发表于 2017 年 11 月 3 日的 apastuhov.com*

* * *

您使用大型代码库，并且您的项目有数百甚至数千个文件？我假设您的项目中有死代码。我想告诉你关于死代码的问题以及我们如何处理它。不过先来定义一下什么代码可以被称为*死*。

## 死代码定义

死代码是任何永远不会被执行的代码。它可能是一些条件、循环或任何文件，只是被简单地创建了，但没有在你的项目中使用。

## 为什么是问题？

这是一个问题，因为代码没有意义！你可以放下它，一切都会好的。我想提一下死代码的基本问题:

*   新来的人会阅读它，他/她会试着理解代码，甚至使用它。
*   它会占用您的存储库和工作目录中的一些空间。
*   它会被你的 IDE 插入，所以它会比它需要的多消耗内存。

如果你有死代码，你的项目会发生什么。

[![Everything will die](img/139870aa718d0074335e2deef4991018.png "Zombie code..")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BB-ThiVl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qfv581lxm0l6j9vfzy8x.gif)

## 为什么是 JavaScript 的问题？

JavaScript 是一种非常活跃的编程语言。开发者每天都会创建数十亿个应用和插件。例如，NPM(Node JS 应用程序的包管理器)有超过 450 000 个包，参见 [modulecounts](http://www.modulecounts.com/) ！它是超过 1tb 的代码！我假设一个包有 2MB 大小。我确信其中超过 25%是死代码...没人用它。

你不和 NodeJS 一起工作？你是前端开发人员吗？很好！我假设你的委托人改变主意了。:)而你会发展出他想要的所有特征。但是你会很匆忙地去做，所以你不会有任何时间去修改旧代码，甚至放弃它。

[![We always have some changes for you](img/7679874b42fae2e2fdcda3fef1b6a05b.png "We always have some changes for you")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3qTAWJSg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a19bclt7i7crut6gqqxr.gif)

## 任何现有的解决方案

如果你谷歌一下，你会发现每个人都在使用 Google Closure 或 WebPack。但我坚持认为，这不是真正解决问题的办法。这些工具只会删除公共脚本的死代码。但是它们没有告诉您应该删除哪些代码。

## 其他编程语言的解决方案

让我们看看其他一些流行的语言:

*   巨蟒- [秃鹫](https://pypi.python.org/pypi/vulture)
*   C，C++，C#，TCL - [coco](https://www.froglogic.com/coco/)
*   Java - [proguard](https://www.guardsquare.com/en/proguard)

没有评论..JavaScript 是局外人。

## 什么是理想的方案

嗨!..我们看到其他语言也有类似的情况。所以让我们想象一下 JavaScript 的理想解决方案。它必须检测:

*   执行不到的代码
*   Oxbow 代码
*   死亡商店
*   死亡文件

### 不可及的代码

永久退货声明。

```
function temp() {
    return 1;
    var a = 2; // Dead Code
} 
```

Enter fullscreen mode Exit fullscreen mode

不必要的条件。

```
function temp() {
    var a = 2;
    if (a > 3) {
         return 3; // Dead Code
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

不必要的循环。

```
function temp() {

    var a = 2;

    while (false) {

         return 3; // Dead Code

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
牛弓形码

```
function temp() {

    // ...

    function private_calculation() {

        return 2*2;

    }

    // ...

    this.calc = function () {

        // Previously private_calculation was called here...

        return 22;

    }

    // ...

}* 
```

*Enter fullscreen mode Exit fullscreen mode* *### 
  
死店

```
function func(a, b) {

    var x;

    var i = 300;

    while (i--) {

        x = a + b; // Dead store

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
死亡档案

假设您有一些带代码的文件，但是这个文件在您的项目中根本没有使用:没有导入，没有到那个文件的`<script src="...">`链接。

## 总结

在这里，我告诉你关于死代码的 JavaScript 问题，以及我们如何处理它。目前，没有解决方案能告诉我们哪些代码可以安全删除。

在这里写下你的看法——哪些功能必须有死代码的工具？我会更新目前的职位！

## 更新(2017 年 9 月)

从 Chrome 版本 59 开始，你可以用开发工具测试你的代码使用覆盖率。

[https://developers . Google . com/web/updates/2017/04/dev tools-release-notes # coverage](https://developers.google.com/web/updates/2017/04/devtools-release-notes#coverage)

它确实是定义旧的/过时的/死的代码的好工具，但是它有一个大问题:

> 它告诉您使用了多少代码，加载了多少代码。

定义未使用的代码——你需要通过点击用户界面上的所有按钮来浏览网站上的所有功能。*