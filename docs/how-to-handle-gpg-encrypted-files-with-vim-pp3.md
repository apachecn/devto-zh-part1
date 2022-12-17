# 如何用 VIM 处理 GPG 加密文件

> 原文：<https://dev.to/mnlwldr/how-to-handle-gpg-encrypted-files-with-vim-pp3>

我喜欢用 [vim](http://www.vim.org/) 在简单的文件里写小纸条。有时候我想把这些文件加密，这样就不是每个人都能读到里面的所有内容。我正在寻找解决办法，发现了[这个](https://wiki.debianforum.de/Vim_mit_gnupg)。

```
augroup aencrypted
    au!
    " First make sure nothing is written to ~/.viminfo while editing
    " an encrypted file.
    autocmd BufReadPre,FileReadPre *.asc set viminfo=
    " We don't want a swap file, as it writes unencrypted data to disk
    autocmd BufReadPre,FileReadPre *.asc set noswapfile
    " Switch to binary mode to read the encrypted file
    autocmd BufReadPre,FileReadPre *.asc set bin
    autocmd BufReadPre,FileReadPre *.asc let ch_save = &ch|set ch=2
    autocmd BufReadPost,FileReadPost *.asc '[,']!sh -c "gpg --decrypt 2> /dev/null"
    " Switch to normal mode for editing
    autocmd BufReadPost,FileReadPost *.asc set nobin
    autocmd BufReadPost,FileReadPost *.asc let &ch = ch_save|unlet ch_save
    autocmd BufReadPost,FileReadPost *.asc execute ":doautocmd BufReadPost " . expand("%:r")
    " Convert all text to encrypted text before writing
    autocmd BufWritePre,FileWritePre *.asc '[,']!sh -c "gpg --default-recipient-self -ae 2>/dev/null"
    " Undo the encryption so we are back in the normal text, directly
    " after the file has been written.
    autocmd BufWritePost,FileWritePost *.asc u
augroup END

augroup bencrypted
    au!
    " First make sure nothing is written to ~/.viminfo while editing
    " an encrypted file.
    autocmd BufReadPre,FileReadPre *.gpg set viminfo=
    " We don't want a swap file, as it writes unencrypted data to disk
    autocmd BufReadPre,FileReadPre *.gpg set noswapfile
    " Switch to binary mode to read the encrypted file
    autocmd BufReadPre,FileReadPre *.gpg set bin
    autocmd BufReadPre,FileReadPre *.gpg let ch_save = &ch|set ch=2
    autocmd BufReadPost,FileReadPost *.gpg '[,']!sh -c "gpg --decrypt 2> /dev/null"
    " Switch to normal mode for editing
    autocmd BufReadPost,FileReadPost *.gpg set nobin
    autocmd BufReadPost,FileReadPost *.gpg let &ch = ch_save|unlet ch_save
    autocmd BufReadPost,FileReadPost *.gpg execute ":doautocmd BufReadPost " . expand("%:r")
    " Convert all text to encrypted text before writing
    autocmd BufWritePre,FileWritePre *.gpg '[,']!sh -c "gpg --default-recipient-self --armor -ev 2>/dev/null"
    " Undo the encryption so we are back in the normal text, directly
    " after the file has been written.
    autocmd BufWritePost,FileWritePost *.gpg u
augroup END 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以用`vim YOURNOTE.gpg`创建一个新文件。保存时会加密，打开时会解密。