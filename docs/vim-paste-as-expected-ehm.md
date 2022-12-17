# 预期的 VIM 粘贴

> 原文：<https://dev.to/david_j_eddy/vim-paste-as-expected-ehm>

有时当粘贴代码并最终进入 VIM 时，格式会变得很混乱；尤其是当逻辑包含注释区域时。VIM 喜欢缩进，直到时间的尽头。

快速解决方法是将 VIMs 模式设置为`PASTE`，而不是`INSERT`。使用`:set paste`经常可以达到目的。

要切换模式，您可以设置一个热键，如 so `:set pastetoggle=<F2>`。