# 获取 C 中的终端宽度

> 原文：<https://dev.to/pauljlucas/getting-the-terminal-width-in-c>

## 简介

我的一个开源项目是 [wrap](https://github.com/paul-j-lucas/wrap) ，我用它在编辑源代码时重新格式化注释。它的命令行选项之一是`--width=` *n* ，允许您指定换行的线宽。我想增加说`--width=term`的能力，这将使用`wrap`正在运行的终端窗口的当前宽度。那么如何获得终端窗口的当前宽度呢？事实证明，一个通用的解决方案比你想象的要难。

使通用解决方案更加困难的一个原因是，即使标准输入、输出或错误都没有连接到 TTY，因为它们被重定向到一个文件或管道，它也必须工作。事实上，`wrap`的用例是，当从 [vim](http://www.vim.org) 内部用作过滤器时，它必须工作。

## 方案一:`ioctl()`

如果你用[谷歌搜索](https://www.google.com/search?q=terminal+width+c)来解决这个问题，很多答案都说用`ioctl()`，比如:

```
unsigned get_term_width( void ) {
  struct winsize ws;
  if ( ioctl( STDIN_FILENO , TIOCGWINSZ, &ws ) != 0 &&
       ioctl( STDOUT_FILENO, TIOCGWINSZ, &ws ) != 0 &&
       ioctl( STDERR_FILENO, TIOCGWINSZ, &ws ) != 0 ) {
    fprintf( stderr, "ioctl() failed (%d): %s\n", errno, strerror( errno ) );
    return 0;
  }
  return ws.ws_col;
} 
```

Enter fullscreen mode Exit fullscreen mode

仅当至少一个标准输入、输出或错误连接到 TTY 时，这才有效，这意味着它不适用于`wrap`的用例。

## 方案二:`getmaxyx()`

如果你搜索得多一点，其他答案说使用`getmaxyx()`那是[诅咒库](https://en.wikipedia.org/wiki/Curses_(programming_library))的一部分，类似于:

```
unsigned get_term_width( void ) {
  initscr();                         // initialize curses
  int max_x, max_y;
  getmaxyx( stdscr, max_y, max_x );  // note: macro (no &) and max_x is second
  endwin();                          // teardown curses
  if ( max_x == -1 ) {
    fprintf( stderr, "getmaxyx() failed\n" );
    return 0;
  }
  return max_x;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样更好，因为标准输入、输出或误差中的至少一个是否连接到 TTY 并不重要。然而，如果你调整窗口大小，它不会注意到变化，并且*总是*返回原始大小。

## 方案三:`tgetnum()`

如果你搜索得更多，还有其他答案说使用`tgetnum()`，那是 [terminfo 库](https://en.wikipedia.org/wiki/Terminfo)的一部分。但是，要做到这一点，您必须:

1.  获取`TERM`环境变量的值；
2.  通过`tgetent()`从 terminfo 数据库获取终端数据；
3.  从`tgetnum()`获取列数。

大概是:

```
unsigned get_term_width( void ) {
  char const *const term = getenv( "TERM" );
  if ( term == NULL ) {
    fprintf( stderr, "TERM environment variable not set\n" );
    return 0;
  }

  char term_buf[ 1024 ];
  switch ( tgetent( term_buf, term ) ) {
    case -1:
      fprintf( stderr, "tgetent() failed: terminfo database not found\n" );
      return 0;
    case 0:
      fprintf( stderr, "tgetent() failed: TERM=%s not found\n", term );
      return 0;
  } // switch

  int const cols = tgetnum( "co" );  // number of (co)lumns
  if ( cols == -1 ) {
    fprintf( stderr, "tgetnum() failed\n" );
    return 0;
  }
  return cols;
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这和`getmaxyx()`有同样的问题，因为它没有注意到窗口大小的变化。

## 方案四:`tigetnum()`

如果你搜索了很长时间，你可能会找到一个答案，说使用`tigetnum()`，这也是 terminfo 库的一部分。但是，要做到这一点，您必须:

1.  获取`TERM`环境变量的值；
2.  通过`ctermid()`获取当前终端的路径，如`/dev/tty001`；
3.  `open()`那条小路；
4.  通过`setupterm()`从 terminfo 数据库获取终端数据；
5.  从`tigetnum()` ( *而不是* `tgetnum()`)中获取列数。

大概是:

```
unsigned get_term_width( void ) {
  char const *const term = getenv( "TERM" );
  if ( term == NULL ) {
    fprintf( stderr, "TERM environment variable not set\n" );
    return 0;
  }

  char const *const cterm_path = ctermid( NULL );
  if ( cterm_path == NULL || cterm_path[0] == '\0' ) {
    fprintf( stderr, "ctermid() failed\n" );
    return 0;
  }

  int tty_fd = open( cterm_path, O_RDWR );
  if ( tty_fd == -1 ) {
    fprintf( stderr,
      "open(\"%s\") failed (%d): %s\n", cterm_path, errno, strerror( errno )
    );
    return 0;
  }

  int cols = 0;
  int setupterm_err;
  if ( setupterm( (char*)term, tty_fd, &setupterm_err ) == ERR ) {
    switch ( setupterm_err ) {
      case -1:
        fprintf( stderr, "setupterm() failed: terminfo database not found\n" );
        goto done;
      case 0:
        fprintf( stderr,
          "setupterm() failed: TERM=%s not found in database or too generic\n",
          term
        );
        goto done;
      case 1:
        fprintf( stderr, "setupterm() failed: terminal is hardcopy\n" );
        goto done;
    } // switch
  }

  cols = tigetnum( (char*)"cols" );
  if ( cols < 0 )
    fprintf( stderr, "tigetnum() failed (%d)\n", cols );

done:
  if ( tty_fd != -1 )
    close( tty_fd );
  return cols < 0 ? 0 : cols;
} 
```

Enter fullscreen mode Exit fullscreen mode

此解(*终于！*)起作用——包括注意窗口大小变化。要获得像终端窗口的当前宽度这样“简单”的东西，需要做大量的工作。欢迎使用 C 和 Unix。