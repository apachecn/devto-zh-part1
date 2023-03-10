# Vim 中的注释行

> 原文：<https://dev.to/lepovirta/commenting-lines-in-vim-36fe>

Emacs 有一个很好的命令叫做`comment-region`，可以用来注释代码行。该命令将注释符号添加到选定区域中每一行的开头。不幸的是，Vim 中没有等效的命令，但是只需几行 Vimscript 就可以轻松创建类似的命令。

```
fu! CommentLines() range
    let commentsymbol = exists('b:commentsymbol') ? b:commentsymbol : '//'
    let beginsWithComment = getline(a:firstline) =~ ('\M^' . l:commentsymbol)
    for linenum in range(a:firstline, a:lastline)
        let line = getline(linenum)
        let replacement = l:beginsWithComment
 \ ? substitute(line, '\M^' . l:commentsymbol . '\s\?', '', '')
 \ : l:commentsymbol . ' ' . line
        if exists('b:commentsymbolend')
            let l:replacement = l:beginsWithComment
 \ ? substitute(l:replacement, '\M\s\?' . b:commentsymbolend . '$', '', '')
 \ : l:replacement . ' ' . b:commentsymbolend
        endif
        call setline(linenum, replacement)
    endfor
    call cursor(a:lastline + 1, 1)
endfunction

fu! CommentSymbol(start, ...)
    let b:commentsymbol = a:start
    if a:0 >= 1
        let b:commentsymbolend = a:1
    elseif exists('b:commentsymbolend')
        unlet b:commentsymbolend
    endif
endfunction
command! -nargs=0 -range Comment <line1>,<line2>call CommentLines()
command! -nargs=+ CommentSymbol call CommentSymbol(<f-args>) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码清单中，函数`CommentLines`用于注释选定的范围。命令`Comment`是该功能的简称，可以像任何其他 range 命令一样调用。例如，调用`:5,10Comment`将注释掉第 5 行和第 10 行之间的内容。在没有范围的情况下调用该命令将(取消)注释光标下的行。下面是如何将命令绑定到一个组合键，比如`<Leader>c` :

```
nnoremap <silent> <Leader>c :Comment<cr>
vnoremap <silent> <Leader>c :Comment<cr> 
```

Enter fullscreen mode Exit fullscreen mode

可以使用两个缓冲区局部变量来配置注释符号。该函数使用变量`b:commentsymbol`作为注释符号。默认情况下，如果未设置变量，则使用`//`作为注释符号。还可以指定一个可选变量`b:commentsymbolend`，它将被附加到注释行中。可以使用`let` :
将注释符号直接设置到变量中

```
" Python comments
let b:commentsymbol = '#'

" C style comments
let b:commentsymbol = '/*'
let b:commentsymbolend = '*/' 
```

Enter fullscreen mode Exit fullscreen mode

或者，可以使用命令`CommentSymbol`:

```
" Python comments
CommentSymbol #

" C style comments
CommentSymbol /* */ 
```

Enter fullscreen mode Exit fullscreen mode

如果上面显示的评论功能不是你喜欢的，看看这些插件吧:

*   [解说. vim](https://github.com/tpope/vim-commentary)
*   [书呆子评论员](https://github.com/scrooloose/nerdcommenter)
*   [t 备注](http://www.vim.org/scripts/script.php?script_id=1173)