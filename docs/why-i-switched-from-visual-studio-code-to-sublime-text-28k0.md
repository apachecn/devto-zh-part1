# Sublime Text vs Vscode 为什么我从 Visual Studio 代码切换到 Sublime Text

> 原文：<https://dev.to/restoreddev/why-i-switched-from-visual-studio-code-to-sublime-text-28k0>

最近，我改用 [Sublime Text](https://www.sublimetext.com/) 作为我的主要代码编辑器。一年多来，我一直使用 [Visual Studio 代码](https://code.visualstudio.com/)来编写代码。这两个编辑器非常相似，但有足够的差异，我想分享是什么导致我使用 Sublime 全职。*注意:这篇文章不是要抨击一项技术。我试着从我的个人经验给出一个诚实的比较，但是选择代码编辑器是一个主观的过程，所以每个人对自己喜欢的会有不同的看法。*

## 是什么让我转行

### 伟大的符号分析

当你在 Sublime Text 中打开一个项目时，它会自动启动一个叫做“符号分析”的过程，这是一个在你的代码中寻找关键词的时髦术语。符号分析的伟大之处在于，我可以键入 Cmd + Shift + R 来弹出一个符号搜索菜单，并在我的代码中快速找到类名和方法。我主要使用 PHP，所以如果我已经知道我正在使用的类名是`PostController`，我可以在符号搜索中搜索它，并立即在编辑器中打开我的 PHP 类文件。

VS 代码也支持符号搜索，但是，它只支持一些现成的语言。有一个第三方 PHP 符号分析器可以处理 VS 代码，但是，我发现它很难处理大型代码库，而 Sublime 没有问题。

### 超快

Sublime Text 是您可以用来编写代码的最快的文本编辑器。它几乎立即打开，并执行非常快速的搜索。微软在保持 VS 代码性能方面做得很好，然而，VS 代码是基于[电子](https://electronjs.org/)的。Electron 是一个框架，用于捆绑 Chromium 的一个实例以及用 JavaScript/Node.js 编写的代码。它使编辑器具有很强的可扩展性，但将 Chromium 的整个实例用于文本编辑器会使应用程序启动缓慢并使用更多内存。Sublime Text 是用 C++写的原生 app，所以它的占用空间要低很多。

### 更好的 Vim 绑定

写代码的时候真的很喜欢用 Vim 键绑定。尽管我喜欢 Vim 键盘快捷键，但我仍然喜欢使用标准的文本编辑器来利用侧边栏文件列表和文件标签等现代功能。我发现 Sublime 的 Vim 支持比 VS 代码更准确，帮助我写代码更快一点。Sublime 支持 Vim 绑定，但是如果你使用 [Vintageous](https://github.com/guillermooo/Vintageous) 插件，你可以获得更多的特性。

## 我从 Visual Studio 代码中错过的东西

### 功能丰富的工具条

VS 代码有一个非常好的侧边栏，允许更灵活地创建和移动文件。Sublime 有一个更好的侧边栏插件，还有其他键盘快捷键插件，如 [AdvancedNewFile](https://github.com/skuroda/Sublime-AdvancedNewFile) 使过渡更容易，但我有时会想念 VS Code 侧边栏的开箱即用功能。

### 内置调试器

VS 代码有一个内置的调试器，可以和许多编程语言一起工作。这使得使用 PHP 的 xdebug 变得非常简单。即使 Sublime 有调试插件，它们也不像 VS Code 提供的开箱即用那样可靠。在这种情况下，如果我在调试一些棘手的东西，我仍然会打开 VS 代码。

## 结论

文字编辑说到底都是个人喜好和工作要求。对于我的用例来说，Sublime 是一种非常愉快的体验，帮助我更快地编写代码。如果你想了解更多关于崇高文本的知识，Jeffrey Way 在 Laracasts 上有一个关于它的课程，Wes Bos 也写了一本关于它的[书](http://wesbos.com/sublime-text-book/)。

在评论里让我知道你最喜欢的编辑是什么！