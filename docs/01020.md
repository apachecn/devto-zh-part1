# 节点模块深入研究:fs

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-fs-417l>

是时候深入了解另一个节点模块了！

我从人们那里得到了一些很好的反馈，他们认为在这些带注释的代码阅读中深入研究节点代码库的 C++部分会很有趣。我同意。老实说，我一直避免使用它，主要是因为我对自己的 C++知识和对系统级软件的理解缺乏信心。但是你知道吗，我把这些都放在一边，一头扎进节点代码库的 C++部分，因为我是一个勇敢无畏的开发者。

我这么说是为了澄清，不要把我说的任何话当成绝对的事实，如果你对我误解的代码有什么见解，请在 Twitter 上告诉我。

不管怎样，让我们开始有趣的事情吧。

关于`fs`模块，我想了很多。`fs`模块是 Node 中标准库的一部分，它允许开发人员与文件系统进行交互。你可以读文件，写文件，检查文件的状态。如果您正在使用 JavaScript 构建桌面应用程序或与后端服务器中的文件进行交互，这将非常方便。

我使用最多的`fs`函数之一是`exists`函数，它检查文件是否存在。这个功能最近被弃用，取而代之的是`fs.stat`或`fs.access`。因此，我想深入了解一下`fs.access`在 Node 中是如何工作的会很有趣。为了让我们都在同一页上，这里是你如何在一个应用程序中使用`fs.access`。

```
> const fs = require('fs');
undefined
> fs.access('/etc/passwd', (error) => error ? console.log('This file does not exist.') : console.log('This file exists.'));
undefined
> This file exists. 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。所以我们可以传递一个文件名和一个接受错误的回调。如果`error`存在，那么我们不能访问该文件，但是如果它不存在，那么我们可以。因此，让我们前往代码库中的 [`fs`模块，看看发生了什么。`fs.access`函数的代码如下所示。](https://github.com/nodejs/node/blob/8a86d9c1cf35fe4f892d483e3673083f5d8f42cf/lib/fs.js) 

```
fs.access = function(path, mode, callback) {
  if (typeof mode === 'function') {
    callback = mode;
    mode = fs.F_OK;
  } else if (typeof callback !== 'function') {
    throw new errors.TypeError('ERR_INVALID_CALLBACK');
  }

  if (handleError((path = getPathFromURL(path)), callback))
    return;

  if (typeof path !== 'string' && !(path instanceof Buffer)) {
    throw new errors.TypeError('ERR_INVALID_ARG_TYPE', 'path',
                               ['string', 'Buffer', 'URL']);
  }

  if (!nullCheck(path, callback))
    return;

  mode = mode | 0;
  var req = new FSReqWrap();
  req.oncomplete = makeCallback(callback);
  binding.access(pathModule.toNamespacedPath(path), mode, req);
}; 
```

Enter fullscreen mode Exit fullscreen mode

就像我之前提到的，它需要一个路径和一个回调。它还带有一个模式参数，你可以在这里阅读更多关于[的信息。函数中的前几行大多是标准的验证和安全检查。我将避免在这里讨论它们，因为我认为它们是不言自明的。我知道当人们对代码动手动脚的时候有点烦人，所以如果你对我忽略的这些行有什么具体的问题，尽管问我。](https://nodejs.org/api/fs.html#fs_fs_access_path_mode_callback)

当我们到达函数的最后几行时，代码变得更有趣了。

```
var req = new FSReqWrap();
req.oncomplete = makeCallback(callback);
binding.access(pathModule.toNamespacedPath(path), mode, req); 
```

Enter fullscreen mode Exit fullscreen mode

我从未见过这个`FSReqWrap`物体。我假设它是节点生态系统中用于处理异步请求的一些低级 API。我试图找出这个对象是在哪里定义的。它的 require 语句如下所示。

```
const { FSReqWrap } = binding; 
```

Enter fullscreen mode Exit fullscreen mode

所以看起来它是从`binding`中提取 FSReqWrap 对象。但是什么是`binding`？

```
const binding = process.binding('fs'); 
```

Enter fullscreen mode Exit fullscreen mode

嗯。所以看起来是用`'fs'`参数调用`process.binding`的结果。我已经看到这些`process.binding`调用散布在代码库中，但很大程度上避免深入探究它们是什么。今天不行！快速的谷歌搜索得到了[这个 StackOverflow 问题](https://stackoverflow.com/questions/24042861/nodejs-what-does-process-binding-mean)，这证实了我的猜测，即`process.binding`是 C++级代码暴露给代码库的 JavaScript 部分的方式。所以我在节点代码库中搜寻，试图找到`fs`的 C/C++代码所在的位置。我发现`fs`实际上有两个不同的 C 级源文件，一个与 Unix 相关联的[，另一个与 Windows](https://github.com/nodejs/node/blob/8a86d9c1cf35fe4f892d483e3673083f5d8f42cf/deps/uv/src/unix/fs.c) 相关联的[。](https://github.com/nodejs/node/blob/8a86d9c1cf35fe4f892d483e3673083f5d8f42cf/deps/uv/src/win/fs.c)

所以我试图看看在 Unix 的`fs` C 源代码中是否有类似于`access`函数的定义。`access`这个词在法典中被引用了四次。

两次[这里](https://github.com/nodejs/node/blob/8a86d9c1cf35fe4f892d483e3673083f5d8f42cf/deps/uv/src/unix/fs.c#L1075)。

```
#define X(type, action) \
  case UV_FS_ ## type: \
    r = action; \
    break;

    switch (req->fs_type) {
    X(ACCESS, access(req->path, req->flags)); 
```

Enter fullscreen mode Exit fullscreen mode

还有两次[这里](https://github.com/nodejs/node/blob/8a86d9c1cf35fe4f892d483e3673083f5d8f42cf/deps/uv/src/unix/fs.c#L1137-L1146)。

```
int uv_fs_access(uv_loop_t* loop,
                 uv_fs_t* req,
                 const char* path,
                 int flags,
                 uv_fs_cb cb) {
  INIT(ACCESS);
  PATH;
  req->flags = flags;
  POST;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道我之前说的“这个代码库的 C 部分让我紧张”是什么意思了。

我觉得`uv_fs_access`更容易调查。我不知道那个`X`函数宏业务是怎么回事，我不认为我有一种禅宗般的心态去弄清楚它。

好的！因此`uv_fs_access`函数似乎正在将`ACCESS`常数传递给`INIT`函数宏，看起来有点像这样。

```
#define INIT(subtype) \
  do { \
    if (req == NULL) \
      return -EINVAL; \
    req->type = UV_FS; \
    if (cb != NULL) \
      uv__req_init(loop, req, UV_FS); \
    req->fs_type = UV_FS_ ## subtype; \
    req->result = 0; \
    req->ptr = NULL; \
    req->loop = loop; \
    req->path = NULL; \
    req->new_path = NULL; \
    req->cb = cb; \
  } \
  while (0) 
```

Enter fullscreen mode Exit fullscreen mode

因此，`INIT`函数宏似乎正在初始化某个`req`结构中的字段。通过查看将`req`作为参数的函数的函数参数的类型声明，我认为 req 是一个指向`uv_fs_t`对象的指针。我发现一些文档非常简洁地说明了`uv_fs_t`是一种文件系统请求类型。我想这就是我所需要知道的！

旁注:为什么这段代码写在一个`do {} while (0)`中，而不仅仅是一系列函数调用。有人知道这可能是为什么吗？后期补充:我搜索了一下，找到了一个回答这个问题的 [StackOverflow 帖子](https://stackoverflow.com/questions/9495962/why-use-do-while-0-in-macro-definition)。

好的。因此，一旦这个文件系统请求对象被初始化，`access`函数就调用`PATH`宏，该宏执行以下操作。

```
#define PATH \
  do { \
    assert(path != NULL); \
    if (cb == NULL) { \
      req->path = path; \
    } else { \
      req->path = uv__strdup(path); \
      if (req->path == NULL) { \
        uv__req_unregister(loop, req); \
        return -ENOMEM; \
      } \
    } \
  } \
  while (0) 
```

Enter fullscreen mode Exit fullscreen mode

嗯。因此，这似乎是在检查与文件系统请求相关联的路径是否是有效路径。如果路径无效，它似乎会注销与请求相关联的循环。我不理解这段代码的很多细节，但我的直觉是，它对正在发出的文件系统请求进行验证。

`uv_fs_access`调用的下一个调用是对`POST`宏的调用，该宏有以下与之相关的代码。

```
#define POST \
  do { \
    if (cb != NULL) { \
      uv__work_submit(loop, &req->work_req, uv__fs_work, uv__fs_done); \
      return 0; \
    } \
    else { \
      uv__fs_work(&req->work_req); \
      return req->result; \
    } \
  } \
  while (0) 
```

Enter fullscreen mode Exit fullscreen mode

因此，`POST`宏实际上调用了与文件系统请求相关的动作循环，然后执行适当的回调。

在这一点上，我有点迷失了。我碰巧和另一位代码阅读爱好者茱莉亚·伊万斯(Julia Evans)一起参加了 [StarCon](https://starcon.io) 。我们坐在一起，仔细研究了`uv__fs_work`函数中的一些代码，看起来像这样。

```
static void uv__fs_work(struct uv__work* w) {
  int retry_on_eintr;
  uv_fs_t* req;
  ssize_t r;

  req = container_of(w, uv_fs_t, work_req);
  retry_on_eintr = !(req->fs_type == UV_FS_CLOSE);

  do {
    errno = 0;

#define X(type, action) \
  case UV_FS_ ## type: \
    r = action; \
    break;

    switch (req->fs_type) {
    X(ACCESS, access(req->path, req->flags));
    X(CHMOD, chmod(req->path, req->mode));
    X(CHOWN, chown(req->path, req->uid, req->gid));
    X(CLOSE, close(req->file));
    X(COPYFILE, uv__fs_copyfile(req));
    X(FCHMOD, fchmod(req->file, req->mode));
    X(FCHOWN, fchown(req->file, req->uid, req->gid));
    X(FDATASYNC, uv__fs_fdatasync(req));
    X(FSTAT, uv__fs_fstat(req->file, &req->statbuf));
    X(FSYNC, uv__fs_fsync(req));
    X(FTRUNCATE, ftruncate(req->file, req->off));
    X(FUTIME, uv__fs_futime(req));
    X(LSTAT, uv__fs_lstat(req->path, &req->statbuf));
    X(LINK, link(req->path, req->new_path));
    X(MKDIR, mkdir(req->path, req->mode));
    X(MKDTEMP, uv__fs_mkdtemp(req));
    X(OPEN, uv__fs_open(req));
    X(READ, uv__fs_buf_iter(req, uv__fs_read));
    X(SCANDIR, uv__fs_scandir(req));
    X(READLINK, uv__fs_readlink(req));
    X(REALPATH, uv__fs_realpath(req));
    X(RENAME, rename(req->path, req->new_path));
    X(RMDIR, rmdir(req->path));
    X(SENDFILE, uv__fs_sendfile(req));
    X(STAT, uv__fs_stat(req->path, &req->statbuf));
    X(SYMLINK, symlink(req->path, req->new_path));
    X(UNLINK, unlink(req->path));
    X(UTIME, uv__fs_utime(req));
    X(WRITE, uv__fs_buf_iter(req, uv__fs_write));
    default: abort();
    }
#undef X
  } while (r == -1 && errno == EINTR && retry_on_eintr);

  if (r == -1)
    req->result = -errno;
  else
    req->result = r;

  if (r == 0 && (req->fs_type == UV_FS_STAT ||
                 req->fs_type == UV_FS_FSTAT ||
                 req->fs_type == UV_FS_LSTAT)) {
    req->ptr = &req->statbuf;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧！我知道这看起来有点吓人。相信我，我第一次看到它的时候也很害怕。朱莉娅和我意识到的一件事是这段代码。

```
#define X(type, action) \
  case UV_FS_ ## type: \
    r = action; \
    break;

    switch (req->fs_type) {
    X(ACCESS, access(req->path, req->flags));
    X(CHMOD, chmod(req->path, req->mode));
    X(CHOWN, chown(req->path, req->uid, req->gid));
    X(CLOSE, close(req->file));
    X(COPYFILE, uv__fs_copyfile(req));
    X(FCHMOD, fchmod(req->file, req->mode));
    X(FCHOWN, fchown(req->file, req->uid, req->gid));
    X(FDATASYNC, uv__fs_fdatasync(req));
    X(FSTAT, uv__fs_fstat(req->file, &req->statbuf));
    X(FSYNC, uv__fs_fsync(req));
    X(FTRUNCATE, ftruncate(req->file, req->off));
    X(FUTIME, uv__fs_futime(req));
    X(LSTAT, uv__fs_lstat(req->path, &req->statbuf));
    X(LINK, link(req->path, req->new_path));
    X(MKDIR, mkdir(req->path, req->mode));
    X(MKDTEMP, uv__fs_mkdtemp(req));
    X(OPEN, uv__fs_open(req));
    X(READ, uv__fs_buf_iter(req, uv__fs_read));
    X(SCANDIR, uv__fs_scandir(req));
    X(READLINK, uv__fs_readlink(req));
    X(REALPATH, uv__fs_realpath(req));
    X(RENAME, rename(req->path, req->new_path));
    X(RMDIR, rmdir(req->path));
    X(SENDFILE, uv__fs_sendfile(req));
    X(STAT, uv__fs_stat(req->path, &req->statbuf));
    X(SYMLINK, symlink(req->path, req->new_path));
    X(UNLINK, unlink(req->path));
    X(UTIME, uv__fs_utime(req));
    X(WRITE, uv__fs_buf_iter(req, uv__fs_write));
    default: abort();
    }
#undef X 
```

Enter fullscreen mode Exit fullscreen mode

实际上是一个巨大的开关声明。看起来神秘的`X`宏实际上是 case 语句的语法糖，如下所示。

```
 case UV_FS_ ## type: \
    r = action; \
    break; 
```

Enter fullscreen mode Exit fullscreen mode

例如，这个宏函数调用`X(ACCESS, access(req->path, req->flags))`，实际上对应于下面的扩展 case 语句。

```
case UV_FS_ACCESS:
    r = access(req->path, req->flags)
    break; 
```

Enter fullscreen mode Exit fullscreen mode

所以我们的 case 语句最终调用了`access`函数，并将其响应设置为`r`。什么是`access`？Julia 帮助我意识到`access`是在 [unistd.h](http://pubs.opengroup.org/onlinepubs/7908799/xsh/unistd.h.html) 中定义的系统库的一部分。这就是 Node 与特定于系统的 API 实际交互的地方。

一旦在`r`中存储了一个结果，该函数就执行下面的代码。

```
if (r == -1)
  req->result = -errno;
else
  req->result = r;

if (r == 0 && (req->fs_type == UV_FS_STAT ||
               req->fs_type == UV_FS_FSTAT ||
               req->fs_type == UV_FS_LSTAT)) {
  req->ptr = &req->statbuf;
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，这基本上是检查从调用特定于系统的 API 接收到的结果是否有效，并将其存储回我前面提到的文件系统请求对象。有意思！

这就是这个代码阅读器。我通读了代码库的 C 部分，Julia 的帮助尤其有用。如果您有任何问题，或者希望对我可能误解的内容进行澄清，[请告诉我](https://twitter.com/captainsafia)。下次见！