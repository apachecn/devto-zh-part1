# “elm-package.json”文件

> 原文：<https://dev.to/zwilias/the-elm-packagejson-file-119e>

`elm-package.json`文件包含关于您的应用程序或包的元数据。Elm 平台使用它来编译您的代码并将其作为一个包分发。

要创建一个基本的`elm-package.json`文件，可以执行`elm-package install`或`elm-make`。这将建立一个简单的项目并下载默认的依赖项。

```
{  "version":  "1.0.0",  "summary":  "helpful summary of your project, less than 80 characters",  "repository":  "https://github.com/user/project.git",  "license":  "BSD3",  "source-directories":  [  "."  ],  "exposed-modules":  [],  "dependencies":  {  "elm-lang/core":  "5.1.1 <= v < 6.0.0",  "elm-lang/html":  "2.0.0 <= v < 3.0.0"  },  "elm-version":  "0.18.0 <= v < 0.19.0"  } 
```

Enter fullscreen mode Exit fullscreen mode

# 概述

| 财产 | 与应用相关 | 与包装相关 |
| --- | --- | --- |
| `version` |  | x |
| `summary` |  | x |
| `repository` |  | x |
| `license` |  | x |
| `source-directories` | x | x |
| `exposed-modules` |  | x |
| `dependencies` | x | x |
| `elm-version` | x | x |

**注意**:所有属性都是必需的，即使上面没有标记为相关。与项目无关的属性最好保持默认值不变。

## `version`

对于应用程序来说，根本不需要接触它。

`version`属性定义了你的包的公共 API 的[语义版本](https://semver.org/)。

只有稳定的包才能发布到[http://package.elm-lang.org/](http://package.elm-lang.org/)。因此，版本号从 1.0.0 开始。

一般不需要*手动*修改该值。相反，当你准备发布一个*新的*版本时，使用`elm-package bump`将你的 API 与之前上传的版本进行比较，并决定应用正确的新版本号。

在使用`elm-package publish`发布版本之前，需要对其进行标记，并且必须发布标记。在这样做之前使用`elm-package publish`将会给你关于如何标记和推送标记的具体指示。

## `summary`

对于应用程序来说，根本不需要接触它。

包裹描述，最多 80 个字符。

这是在的套餐列表上显示的描述。为了让人们能够找到您的包，在这里使用相关的关键字是一个好主意。

## `repository`

对于应用程序来说，根本不需要接触它。

对于包，这应该是 git 存储库，可以通过 https 访问，托管在 GitHub 上。您只需更改 URL 的`user/project`部分，并保持前缀`https://github.com/`和后缀`.git`不变。

请注意，该条目**区分大小写**。如果您在这里没有使用与您实际的 GitHub 用户名或 GitHub 库完全相同的大小写，安装将无法正常运行。

一般情况下，您会希望避免在这里使用任何“特殊”字符。例如，在用户名或项目中使用点号会破坏编译的代码。

除非您的 GitHub 用户名是特定于 Elm 的(例如`elm-lang`)，否则在包名前加上前缀`elm-`通常是个好主意。这使得人们更容易在 GitHub 上发现你的包。

## `license`

对于应用程序来说，根本不需要接触它。

发布本代码的许可证。请注意，大多数许可证还要求在您的存储库中包含一个许可证文件，并在您的自述文件中包含一个版权声明，这样许可证才有效。

在 GitHub 上使用许可证生成器使这个过程变得相当容易。

目前，这一领域不受限制。但是，建议使用合适的 [SPDX 许可证标识符](https://spdx.org/licenses/)。

## `source-directories`

属性给出了一个(相对)路径列表，Elm 编译器将在该列表中查找代码中引用的模块。

通常，如果您的源代码位于相对于`elm-package.json`位置的`src`目录中，这看起来就像`["src"]`。在`elm-test`的`elm-package.json`的情况下，通常会有一个对应的`[".", "../src"]`条目(第一个是测试所在的位置，第二个是测试中的代码所在的位置)。

注意，在 Elm 中，文件路径直接映射到模块名。例如，给定一个`"source-directories": ["src"]`，模块`Foo.Bar.Baz`应该位于`src/Foo/Bar/Baz.elm`。

在 Mac OS X 上，文件系统区分大小写，但不区分大小写。这意味着在开发过程中，您可以不命名上面的文件`src/fOO/bAR/bAZ.elm`。然而，使用你的包的人可能使用区分大小写的文件系统，所以你的包不适合他们。

因此，请使用与模块名称完全相同的大小写。

## `exposed-modules`

对于应用程序来说，根本不需要接触它。

属性表明你的包中的哪些模块是公开的。添加到这个列表中的模块将显示在 http://package.elm-lang.org 的[上](http://package.elm-lang.org)，当用户将你的包作为一个依赖项安装时，暴露的模块将对`import`可用。

当一个模块被添加到`exposed-modules`属性时，`elm-make`将为那些模块启用一组额外的检查，以确保所有暴露的变量都被正确地记录。你可以在 package.elm-lang.org/help/documentation-format[的](http://package.elm-lang.org/help/documentation-format)网站上阅读更多关于文件格式的信息。

## `dependencies`

这可能是应用程序最重要的属性。它由每一个依赖项的名称(由作者的名称和一个包名组合而成)到一系列语义版本的映射组成。一般语法是`<minimal> <= v < <maximal>`。

对于应用程序来说，限制范围或者甚至将它们固定到特定版本通常是个好主意(例如，`1.2.3 <= v < 1.2.4`将只允许版本 1.2.3)。这可以防止你意外地依赖可能改变的行为。为了额外的安全，你也可以在这里添加临时的依赖关系，并固定它们。这样，`elm-package.json`可以兼作一个锁文件。

对于库，建议使用较宽的范围(以最小化发生依赖冲突的风险),同时限制依赖项的数量。例如，如果您只需要某个包中的一个函数，那么最好是复制这个函数，而不是将这个包作为一个依赖项，让您的包的消费者也依赖它。

## `elm-version`

这是一个语义版本范围，指定您的软件包兼容的 Elm 版本。您可能希望将其设置为默认值，除非将软件包从旧版本的 Elm 升级到 0.18。