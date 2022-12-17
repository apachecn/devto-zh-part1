# 今天我学到了:一些 VIM 技巧

> 原文：<https://dev.to/shriharshmishra/today-i-learned-some-vim-tricks-5d3>

# VIM 技巧 1——使用 NERDTree 插件创建新文件

许多 Vim 用户依赖 NERDTree 插件来浏览目录，但很少使用它在不离开 Vim 的情况下创建新文件(至少我是一个这样的 VIM 用户)。

我发现了一个很好的博客条目，它告诉我如何去做- [链接这里](https://sookocheff.com/post/vim/creating-a-new-file-or-directoryin-vim-using-nerdtree/)

总结一下-

*   打开树面板
*   导航到要创建新文件或目录的目录。
*   按`m` -打开 NERDTree 文件系统菜单。
*   键入`a`创建子节点，并键入文件或目录的名称。对于目录，在末尾追加`/`。
*   就是这样。

# VIM 诀窍 2——使用 VIM 内置特性创建新文件。

当我在上面的步骤中做实验时，我犯了一个愚蠢的错误。在用 NERDTree 键入我的实验之前，我没有打开新创建的文件。所以我在一个未命名的缓冲区中输入了我的内容。为了摆脱困境，我决定依靠 VIM 的帮助。这就是我所做的-

*   键入`:h save` -这将打开 VIM 的帮助窗口
*   VIM 中的`saveas`命令帮助我们将当前缓冲区存入一个文件。
*   使用`!`选项保存到现有文件。
*   我将让读者通过自己的实践来尝试这一点。