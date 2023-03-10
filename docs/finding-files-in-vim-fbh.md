# 在 Vim 中查找文件

> 原文：<https://dev.to/lepovirta/finding-files-in-vim-fbh>

Vim 最酷的特性之一是它可以很容易地利用命令行工具来扩展它的特性集。在本文中，我将展示一个使用 Vimscript 为 Unix `find`命令创建 UI 的演练。该脚本提供了一种从 Vim 中查找文件的方法，在单独的 Vim 窗口中列出文件，并允许快速访问找到的文件。

## 核心脚本

为了显示文件搜索结果，我们需要为它们创建一对缓冲区和一个窗口。我们不是为每个搜索结果创建一个新的对，而是尽可能重用现有的对。这样，旧的搜索结果就不会让用户界面变得混乱。

`InitFindWindow`为搜索结果初始化窗口和缓冲区。它遍历可用的窗口来查找显示搜索结果的现有缓冲区(第 5-11 行)。通过逐个跳转到每个窗口(第 6 行)，并查询值`findbuf`的`filetype`设置(第 7 行)，来完成识别。如果函数找到了正确的缓冲区，函数就在那个窗口中停止(第 8-9 行)。如果找不到缓冲区，该函数将跳回到第一个窗口(第 14 行)，并创建一个新窗口及其下的缓冲区(第 15 行)。它还为新的缓冲区将文件类型设置为`findbuf`(第 16 行)。调用该函数后，应该会选择搜索窗口。

```
function! s:InitFindWindow()
    let startwin = winnr()
    let findwindownr = 0

    for winnumber in range(1, winnr('$'))
        execute winnumber . 'wincmd w'
        if &filetype == 'findbuf'
            let findwindownr = winnumber
            break
        endif
    endfor

    if !findwindownr
        execute startwin . 'wincmd w'
        botright new
        setfiletype findbuf
    endif
endfunction 
```

Enter fullscreen mode Exit fullscreen mode

初始化搜索窗口后，需要用最新的搜索结果填充它。函数`ReplaceContentsWithCommand`首先清空缓冲区(第 2 行)，然后将来自给定 shell 命令(参数`cmd`)的结果追加到缓冲区(第 3 行)。使用 Vim 的`read`和`!`命令的组合将命令结果放入缓冲器。`!`命令执行给定的 shell 命令，而`read`命令将该命令的输出附加到缓冲区。最后，该函数删除所有的空行(第 4 行)，并将光标返回到缓冲区的第一行(第 5 行)。

```
function! s:ReplaceContentsWithCommand(cmd)
    silent %d
    execute 'silent $read !' . a:cmd
    g/^$/d
    1
endfunction 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，搜索结果窗口的高度是 Vim 窗口全高的一半。让我们试着调整一下窗户的高度，好吗？在函数`MinimalWindowSize`中，我们将使用这三个值中最小的一个作为窗口高度:搜索结果中的行数、全高度的一半或者作为参数提供的高度值。每个人显然都会对搜索窗口高度有自己的偏好，所以要相应调整。

```
function! s:MinimalWindowSize(maxsize)
    let buflines = line('$')
    let wsize = min([a:maxsize, &lines / 2, l:buflines])
    execute 'resize ' . l:wsize
endfunction 
```

Enter fullscreen mode Exit fullscreen mode

为了调用文件搜索，我们将把这三个函数结合成一个函数，并为它设置一个自定义命令。函数`Find`将一个搜索模式作为参数，并将其与从`g:findcmd`找到的命令连接起来。默认情况下，如果没有显式设置其他命令，将使用第 2 行显示的 find 命令。变量`g:findwinsize`也可用于调整搜索窗口的最大尺寸。

该函数初始化窗口，使用 find 命令更新窗口内容，并在必要时调整窗口大小。为了确保内容可以更新，该函数在更新之前启用缓冲区修改。该函数还设置窗口的状态行，以显示所使用的搜索模式。

为了向用户公开该函数，我们设置了一个命令，用给定的参数调用该函数。因此调用命令`:Find *.html`将搜索当前目录中的所有 HTML 文件。

```
function! s:Find(pattern)
    let defaultcmd = 'find . \( -type f -o -type l \) -iname'
    let cmd = exists('g:findcmd') ? g:findcmd : l:defaultcmd
    let fullcmd = l:cmd . " '" . a:pattern . "'"
    let maxwinsize = exists('g:findwinsize') ? g:findwinsize : 10

    call s:InitFindWindow()
    setl modifiable
    call s:ReplaceContentsWithCommand(l:fullcmd)
    setl nomodifiable
    call s:MinimalWindowSize(l:maxwinsize)
    let &l:statusline='Find: ' . a:pattern . ' %= %l/%L'
endfunction

command! -nargs=1 Find call s:Find(<q-args>) 
```

Enter fullscreen mode Exit fullscreen mode

## 扩展脚本

通过向`findbuf`文件类型添加功能，可以进一步扩展搜索窗口。这可以通过设置在创建缓冲区时自动运行的命令来实现。例如，将行`autocmd FileType findbuf setl number`添加到您的`vimrc`将为搜索窗口启用行编号。

由于搜索缓冲区是一个短暂的视图，而不是一个真实的文件，因此它的所有与文件相关的功能都可以被禁用:

*   设置`buftype=nofile`确保缓冲区不与任何文件相关联。
*   设置`bufhidden=wipe`和`nobuflisted`确保缓冲区不在缓冲区列表中。
*   设置`noswapfile`确保缓冲区的内容保存在内存中，而不是交换到磁盘。

```
au FileType findbuf setl buftype=nofile bufhidden=wipe nobuflisted noswapfile 
```

Enter fullscreen mode Exit fullscreen mode

此外，通过关闭换行并打开行光标，可以进一步调整 UI，使其看起来更像“菜单”。当换行被禁用时，每行仅显示一个文件。行光标高亮显示光标下的整行。

```
au FileType findbuf setl nowrap cursorline 
```

Enter fullscreen mode Exit fullscreen mode

一个有用的功能是能够从搜索窗口快速打开文件。Vim 已经通过键映射`gf`和`Ctrl-W f`支持这一点。第一个在同一个窗口中从光标下打开文件，而第二个在新窗口中打开文件。但是，它缺少在另一个现有窗口中打开文件的功能。下面的清单显示了如何在前一个窗口中打开光标下的文件。该功能映射到键盘上的 enter 键。

```
function! CursorFileInPreviousWindow()
    let cursorfile = expand('<cfile>')
    wincmd p
    execute 'e ' . l:cursorfile
endfunction

au FileType findbuf
 \ nnoremap <buffer> <Return> :call CursorFileInPreviousWindow()<CR> 
```

Enter fullscreen mode Exit fullscreen mode

## 替代品

如果本文中展示的文件搜索解决方案不适合您，还有几个备选方案可供选择。

Vim 支持通过其内置命令`:e`搜索文件。当您在参数中添加通配符并使用 tab 键完成命令时，通配符将被展开，Vim 将让您从选择中选择一个文件。例如，输入`:e **/*.html`并按 tab 键将列出当前目录中的所有 HTML 文件。但是，一旦选择了文件，文件列表会立即消失。

还有 Vim 插件支持查找和打开文件。下面列出的插件是支持这些和许多其他特性的众多插件中的几个。它们还支持更动态的搜索，每次按键都会更新搜索结果。

*   [CtrlP](https://kien.github.io/ctrlp.vim/)
*   [Vim-CtrlSpace](https://github.com/szw/vim-ctrlspace)
*   [Unite](https://github.com/Shougo/unite.vim)