# 用 vimshell 控制字符编码

> 原文：<https://dev.to/shiena/vimshell-5g55>

# 前言

对于在 Windows 的命令提示符下感到功能不足的 vimmer 来说，[vimshell](https://github.com/Shougo/vimshell.vim/) 是可靠的存在。
但是，像 Git 和 Go 这样输出 UTF-8 的命令会乱码。
介绍这种情况下的应对方法。

## exe 的编码选项

执行其他指令的`exe`内部指令有`--encoding`选项，可以在此指定指令输出的字符编码。
可以指定的字符编码符合 iconv。

Use case

```
$ exe --encoding=utf-8 git log 
```

也可以如下在`~/.vimshrc`中定义 alias 吧。

```
alias git='exe --encoding=utf-8 git' 
```

## g:vim shell _ interactive _ encodings 変数

`g:vimshell_interactive_encodings`变量为键中含有命令名称、值为字符编码的词典，执行与键一致的命令后，其输出将按值指定的字符编码进行转换。
另外，如果键中包含`/`，则会检查是否包含在命令的路径中，而不是命令名称中。

仅在 Windows 的情况下设定了以下初始值。

| 钥匙 | Qi |
| --- | --- |
| `/MinGW/bin/` | `utf-8` |
| `/msysgit/bin/` | `utf-8` |
| `/cygwin/bin/` | `utf-8` |
| `gosh` | `utf-8` |
| `fakecygpty` | `utf-8` |

## Live use cases

### Windowsの場合

因为使用的是通过 SourceTree 安装的 git，所以进行了以下设定。
这样指定目录时，也可以一并指定`ls`和`find`等其他命令。

```
let g:vimshell_interactive_encodings = {
\'/Users/'.$USERNAME.'/AppData/Local/Atlassian/SourceTree/git_local/':'utf-8',
\} 
```

### OSX 的情况

因为 OSX 的默认字符编码是 UTF-8，所以看起来没有问题，但是因为只有文件名使用了作为更改 NFD 的 UTF-8-MAC，所以输出文件名的`ls`和`find`中包含浊点、半浊点的文件名的显示很奇怪
虽然有点粗暴，但是在`/`中指定了`utf-8-mac`。
另外，也同样转换`g:vimshell_user_prompt`的当前目录名称。

```
let g:vimshell_interactive_encodings = {
\'/':'utf-8-mac',
\}
let g:vimshell_user_prompt = 'iconv(fnamemodify(getcwd(), ":~"), "utf-8-mac", "char")' 
```