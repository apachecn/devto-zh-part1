# WordPress 编码标准的 Neovim 和 neoformat 设置

> 原文：<https://dev.to/matsuikazuto/neovim-and-neoformat-setting-for-wordpress-coding-standards-bn7>

# 编码标准很重要

我是 WordPress 开发者。我意识到要写可读的代码，但是因为我是人，所以我会犯错。

让我们使用格式化程序。我想从 Neovim 运行 phpcbf。

伟大的插件是 [neoformat](https://github.com/sbdchd/neoformat)

以下是我的设置。

```
let g:neoformat_php_phpcbf = {
 \ 'exe': 'phpcbf',
 \ 'args': [
 \ '--standard=WordPress',
 \ '--extensions=php',
 \ '%',
 \ '||',
 \ 'true'
 \ ],
 \ 'stdin': 1,
 \ 'no_append': 1
 \ }
let g:neoformat_enabled_php = ['phpcbf']
nnoremap <leader>nf :Neoformat<cr> 
```

Enter fullscreen mode Exit fullscreen mode