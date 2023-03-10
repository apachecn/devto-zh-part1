# Xcode 在自动完成时崩溃:代码片段！

> 原文：<https://dev.to/luisramos1337/xcode-crash-on-auto-complete-snippets-46f1>

我的 XCode 更新到 7.2 版本后崩溃了。每当我试图在 Obj-c 文件中使用自动完成时，都会发生这种情况。由于我的项目在 Swift 中，所以这并没有让我很困扰。当我实际需要编辑一些库代码时，事情变得更糟了！

它崩溃时出现了这个异常:

```
(NSInvalidArgumentException): -[__NSCFData isEqualToString:]: unrecognized selector sent to instance 0x7fd01c5d4530 
```

因为我很快就厌倦了使用文本编辑器来解决这个问题，所以我试图修复它。我用了一些插件，其中一个是 [fuzzy-autocomplete](https://github.com/FuzzyAutocomplete/FuzzyAutocompletePlugin) (如果你不用，那就去看看，太棒了！)我以为问题出在其中一个人身上。我一个接一个地把它们拿掉，每次我试的时候，砰！还在崩溃！

这很糟糕，因为插件是我唯一的选择。在谷歌搜索了一段时间后，我没有发现任何人有类似的问题，所以我猜测这与我的自定义安装有关。这很愚蠢，但我应该早点查看 XCode 崩溃日志...

```
6 0x00000001038f4708 +[IDECodeSnippetLibraryCompletionStrategy_scope:matchesScope:atBOL:] (in IDEKit) 
```

在检查了崩溃日志之后，很明显谁是罪魁祸首:Snippets！

这是我公寓里唯一的定制物品。我重命名了 snippets 文件夹，有效地阻止 XCode 找到它们。你猜怎么着？是啊！自动完成再次工作！我重新添加了所有我喜欢的插件(包括模糊自动补全)，然后回到编码上。我没有添加我的片段，因为我根本没有使用它们。但是用新的 XCode 重新创建它们应该可以解决这个问题。

使用 XCode 的又一天...

PS:顺便说一句，如果你不在 XCode 上使用插件，可以看看插件管理器[阿卡特兹](http://alcatraz.io/)！你一定会找到一些可以提高你的 XCode 使用率的东西:)