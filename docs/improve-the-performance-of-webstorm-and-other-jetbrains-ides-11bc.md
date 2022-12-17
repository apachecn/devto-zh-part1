# 提高 WebStorm(和其他 JetBrains IDEs)的性能

> 原文：<https://dev.to/adammckenna/improve-the-performance-of-webstorm-and-other-jetbrains-ides-11bc>

你用过 WebStorm 吗？那么 PHPStorm，或者实际上 JetBrains 套件中的任何 ide 呢？它们是一个令人印象深刻的 ide 集合，包含了许多特性——集成终端、本地历史和 VCS 集成、“智能”导入等等。

但是，这种功能膨胀是有代价的吗？绝对的。

WebStorm 和其他 JetBrains IDEs 的性能与 web 社区中流行的轻量级文本编辑器相比相形见绌。想到了 Visual Studio 代码和 *Atom* 。

不要害怕！有解决办法。

但是首先，对于那些不熟悉 IDE 的人，让我们后退一步。

## 什么是 WebStorm？

WebStorm 是一个流行的、强大的 web 开发 IDE。它是由 [JetBrains](https://www.jetbrains.com/) 开发的一个更大的 ide 集合之一——以前被称为 *IntelliJ* 。这些 ide 旨在为流行的编码语言提供最终的开发环境。

其他 JetBrains IDEs 包括 PHP 的 *PHPStorm* ，Java 的 *IntelliJ IDEA* ，Python 的 *PyCharm* ，C#的 *ReSharper* (作为 Visual Studio 的*扩展)，等等。*

甚至 Android Studio T1 也运行在 IntelliJ 平台上，从技术上来说，它是 T2 谷歌的集成开发环境。记住这一点，本文中探讨的大多数优化都可以转移到其他 JetBrains IDEs 上。

但是，正如我们提到的，尽管 WebStorm 是目前最强大的 ide 之一，但它肯定不是性能最好的。事实上，在较旧的机器上，它可能是一个机器杀手。

让我们探索如何通过调整开箱即用的设置来增加 WebStorm 的加载时间和性能。

## 优化 WebStorm 的性能

### 设置配置文件

我们将从定制 WebStorm 目录中的一些设置开始。

但是首先，为了定制 WebStorm 设置，我们需要创建一些配置文件。我们将创建两个配置文件:`idea.properties`和`webstorm.vmoptions`。

要创建这些文件，请打开 WebStorm 并进入**帮助**。

选择**编辑自定义属性…** 。一个对话框将提示`idea.properties`文件不存在。

点击**是**创建。文件将会打开。现在，您可以添加配置设置。该文件用于定制 WebStorm 属性。

要创建另一个文件，再次进入**帮助**并选择**编辑定制虚拟机选项...**。

一个对话框将提示`webstorm.vmoptions`文件不存在。

点击**是**创建一个。文件将会打开。现在，您可以添加配置设置。该文件用于定制 WebStorm 虚拟机选项。

为了便于将来参考，可以在以下目录中找到这些文件:

Windows 7 / 8 / 10 - `<SYSTEM DRIVE>\Users\<USER ACCOUNT NAME>\.<PRODUCT><VERSION>`

OS X /苹果电脑- `~/Library/Preferences/<PRODUCT><VERSION>`

### 提高性能

现在我们的配置文件已经设置好了，我们可以更改 WebStorm 的设置来优化性能。

首先，让我们更改`webstorm.vmoptions`文件中的一些选项。

打开新创建的文件，选择所有选项，并用以下代码片段替换它们:

```
-Xms1024m 
-Xmx1536m 
-XX:MaxPermSize=1024m 
-XX:ReservedCodeCacheSize=512m 
-XX:+UseCompressedOops 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将使用 2015 年推出的名为`zero-latency`的实验属性来消除 IDE 延迟。

为此，将以下行添加到您的`idea.properties`文件中:

```
editor.zero.latency.typing=true 
```

Enter fullscreen mode Exit fullscreen mode

我们将继续通过取消不必要的功能来优化 WebStorm 的使用。这样做将减少特性膨胀并增加系统内存。

### 外表和行为

首先，让我们在**外观和行为**首选项中禁用自动更新检查和统计数据发送。

为此，转到**首选项**并选择**外观&行为**。

然后转到**系统设置**并选择**更新**选项卡。从这里，取消选择*自动检查…* 的更新

接下来，从系统设置中选择“数据共享”菜单，并取消选择*发送使用统计数据*。

## 编辑

现在，让我们在**编辑器**首选项中更改一些设置。

在**首选项**中，转到**编辑器**选项卡，选择**实时模板**。取消选择任何不使用的模板。

Emmet 是一个 IDE 插件，帮助改进 HTML 和 CSS 开发工作流程。

这是一个很棒的插件，但是它有很多选项没有被使用，浪费了宝贵的系统内存。

要删除这些不必要的选项，进入**编辑器标签**并选择 **Emmet** 。浏览 CSS、JSX 和 HTML 的选项，取消选择任何你不使用的选项。

**意图**是 JetBrains 应用程序家族的一个强大功能。当你编码的时候，它会对你发现的问题提出解决方案。

然而，默认情况下，WebStorm 在*意图*偏好窗口中选择了每个选项。您不太可能会使用列出的每种技术/语言，因此取消选择您不使用的选项。

要取消选择选项，进入**编辑器的**选项卡，选择**意图**。同样，取消选择任何不适用的语言和技术。

## 插件

快到了。接下来，让我们摆弄我们的插件。

在**首选项**中，转到**插件**标签。

默认情况下，所有本机插件都将被启用。

你不太可能把它们都用上。滚动并取消选择任何不适用于您使用的语言和技术的插件。

## 目录

启动时，WebStorm 会加载项目中的每个目录(文件夹)。然而，其中一些方向，尤其是像`node_modules`目录这样的供应商目录，可能永远不会在 WebStorm 中触及。加载这些目录浪费了宝贵的资源。

要禁用未使用的目录，在**首选项**中，转到**目录**并将任何未在 WebStorm 中编辑的目录设置为`Excluded`。

## 语言&框架

在**首选项**中，转到**语言&框架> JavaScript >库**并取消选择任何未使用的库。

如果您没有使用 [Compass](http://compass-style.org/) 从 Sass 生成 CSS 文件，请确保*“启用 Compass 支持”*未启用。为此，请访问**语言&框架>指南针**。

## 工具

**Web 浏览器**部分用于将您的项目快速启动到启用的浏览器中。

如果不使用此功能，请将其禁用。为此，在**偏好设置**中，转到**网络浏览器**，取消选择除您选择的浏览器之外的所有浏览器。

## 结论

所以，就这样了。

现在我们已经成功清理了 WebStorm，您应该会发现启动时间和总体性能显著增加，这样您就可以专注于重要的事情:web 开发。

## 来源

*   [通过这些定制让 WebStorm 变得更好——Victor sav kin，FreeCodeCamp](https://medium.freecodecamp.com/make-webstorm-better-with-these-customizations-c038c9e5f84b#.ifjrzkk00)
*   [如何加速 WebStorm - StackOverflow](https://stackoverflow.com/questions/29388626/how-to-speed-up-webstorm)
*   [文件‘idea . properties’——JetBrains 文档](https://www.jetbrains.com/help/idea/2016.3/file-idea-properties.html)