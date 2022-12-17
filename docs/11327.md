# 在 mac 版的 IntelliJ IDEA + IdeaVim 上^[无效

> 原文：<https://dev.to/shiena/mac-intellij-idea-ideavim-ogb>

# Reason

因为键盘在 JIS 排列中也会被识别为 US 排列，所以`[`位置的键被识别为`]`。

# Solution

## 配合机器

按下 US 数组的`[`位置的键即可，因此在 JIS 数组中输入`^@`。

## 对准机器

### v0.33 to drop

IdeaVIM v0.33 更改了键的分配方式，并为以下文件之一设置键映射:
而且从 v0.35 开始不再使用`~/.vimrc`和`~/_vimrc`。

*   `~/.ideavimrc`
*   `~/_ideavimrc`
*   ~~T2`~/.vimrc`~~
*   ~~T2`~/_vimrc`~~

mac 的 JIS 排列键盘在`¥`中输入反斜线，在`Option+¥`中输入反斜线很不方便，所以顺便更换。

```
inoremap <c-]> <esc>
inoremap ¥ \
inoremap \ ¥ 
```

参考页

*   [IdeaVIM v0.33 发布](http://d.hatena.ne.jp/masanobuimai/20140429/1398758972)
*   [IdeaVIM 与基于 map 命令的各模式键映射对应的](http://www.karakaram.com/ideavim-map-supported)

### v0.33 is not clear.

在`Settings -> Keymap -> Editor Actions -> Escape`中分配`^[`。