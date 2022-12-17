# Rust FFI:向外界发送字符串

> 原文：<https://dev.to/huytd/rust-ffi-sending-strings-to-the-outside-world-50l>

*原文发布在[我的博客](https://thefullsnack.com/en/string-ffi-rust.html)上*

* * *

**外来函数接口** (FFI)是 Rust 最重要的特性让它与世界的另一部分如 C/C++、Ruby、Python、Node.js、C#，...

官方的 FFI 文档比以前有了很大的改进，但是它仍然不能满足那些想要深入研究 FFI 的人。

了解 FFI 的另一个更好的来源是 Rust FFI Omnibus，它收集了一些例子，向您展示如何在许多语言中使用它。

我在阅读如何在这些资源中使用字符串时感到非常困惑，所以我决定写一篇新的帖子来讨论这个话题，重点是使用 FFI 将字符串发送到其他语言。

## 散发着一串

首先，我们需要了解铁锈的`String`。让我们构建一个返回`String` :
的简单函数

```
#[no_mangle]
pub extern fn string_from_rust() -> String {
  "Hello World".to_string()
} 
```

Enter fullscreen mode Exit fullscreen mode

和 Node.js 代码来读取它:

```
const ffi = require('ffi');

// The path should be 'rstring/target/debug/librstring.so' on Linux environment
let lib = ffi.Library('rstring/target/debug/librstring.dylib', {
  string_from_rust: ['string', []]
});

let result = lib.string_from_rust();
console.log(result); 
```

Enter fullscreen mode Exit fullscreen mode

运行这段代码，您将会看到:

```
$ node ffi.js
[1]    63179 segmentation fault  node ffi.js 
```

Enter fullscreen mode Exit fullscreen mode

崩溃了！但是为什么呢？

原因很简单。`String`是一个只在 Rust 中实现的类型，其他语言(本例中为 Node.js)没有类似于`std::string::String`的东西。所以它不能从 Rust 中读取返回值。

### 字符串必须作为指针返回

对于仅在 Rust 中可用的特定数据类型，如`String`或`Vector`，我们应该将它作为`Pointer`发送到保存其值的内存块。

让我们稍微修改一下我们的 Rust 代码:

```
#[no_mangle]
pub extern fn string_from_rust() -> *const u8 {
  "Hello World".as_ptr()
} 
```

Enter fullscreen mode Exit fullscreen mode

`*const u8`是一个`Pointer`的类型。

再次运行 Node.js 代码，结果如下:

```
$ node ffi.js
Hello WorldHello World 
```

Enter fullscreen mode Exit fullscreen mode

伙计们，我们有好消息和坏消息...

好消息是我们现在可以看到`String`了。坏消息是看起来不对劲。

### NUL 终止的字符串

在 Rust 中，`String`不是以 NUL 结尾的(不是以`\0`结尾)，但是其他语言中的字符串是这样的。在这种情况下，Node.js 不知道我们要获取的文本的结尾在哪里。

*哦，说到 NUL 终止的字符串，有一个人[去年四月用他的 NUL 终止的字符串生成箱打破了 Windows](http://sasheldon.com/blog/2017/05/07/how-i-broke-cargo-for-windows/) 上的整个 Rust 生态系统。*

解决方案？只需在我们的`String`末尾插入`\0`。

```
#[no_mangle]
pub extern fn string_from_rust() -> *const u8 {
  "Hello World\0".as_ptr()
} 
```

Enter fullscreen mode Exit fullscreen mode

再运行一次，现在看起来不错:

```
$ node ffi.js
Hello World 
```

Enter fullscreen mode Exit fullscreen mode

好的。但是等等，当我想处理 String 时，我必须一直放入`\0`字符吗？其实不是，Rust 也提供了一个 C 兼容的字符串类型，叫做`std::ffi::CString`。

您可以很容易地从一个字符串片:
创建一个`CString`

```
CString::new("Hello World").unwrap() 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看如何使用`CString`向 Node.js:
发送一个字符串

```
#[no_mangle]
pub extern fn string_from_rust() -> *const c_char {
    let s = CString::new("Hello World").unwrap();
    s.as_ptr()
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们从一个字符串片段创建一个`CString`，然后我们返回一个指向它的值的指针，就像我们之前做的一样。

```
$ node ffi.js 
```

Enter fullscreen mode Exit fullscreen mode

哎呀！没有显示任何内容。为什么？

### std::mem::算了留着吧

Rust 很聪明，在这种情况下，太聪明了。它会自动释放超出其范围的任何变量的内存块。

仔细看看我们的`string_from_rust()`函数。我们创建了一个`CString`，然后将一个**指针**返回给保存其值的内存块，然后呢？我们要走出`string_from_rust()`功能的范围，也就是说，`s`现在已经不在范围内了。所以，铁锈完成了它的使命，杀死了`s`！

```
pub extern fn string_from_rust() -> *const c_char { 
    let s = CString::new("Hello World").unwrap(); <---.  
    s.as_ptr()                                        | The scope of s
} <---------------------------------------------------' 
```

Enter fullscreen mode Exit fullscreen mode

在 Node.js 应用程序中，我们收到了指针`s`，它指向一个被释放的内存块。这就是为什么我们什么也看不见。

那么我们如何告诉 Rust 不要释放我们字符串的内存呢？

我们用`std::mem::forget`！用法很简单:

```
#[no_mangle]
pub extern fn string_from_rust() -> *const c_char {
    let s = CString::new("Hello World").unwrap();
    let p = s.as_ptr();
    std::mem::forget(s);
    p
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将`s`字符串的`Pointer`存储在一个变量(`p`)中。

然后我们用`std::mem::forget`把它从铁锈的责任中释放出来。

绳子现在漏了出来。Node.js 现在可以读取它的值:

```
$ node ffi.js
Hello World 
```

Enter fullscreen mode Exit fullscreen mode

## 发出一个向量的字符串

有时候，仅仅发送一个`String`是不够的，你需要发送一堆`String`。

从上一节中我们了解到，我们需要发送一个以 NUL 结尾的字符串`String`，比如`String` + `\0`或者`CString`。

是可调整大小的数组，也是 Rust 中特有的类型之一。这意味着，我们需要将它作为一个`Pointer`返回。因此，我们这里将有一个`Pointer`到一个`String`的`Pointer`。这和 C 的数组挺像的。

```
#[no_mangle]
pub extern fn string_array() -> *const *const u8 {
    let v = vec![
        "Hello\0".as_ptr(),
        "World\0".as_ptr()
    ];
    v.as_ptr()
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Node.js 端，我们需要使用来自`npm`的`ref-array`包来实现来自返回的`Buffer`的`Array`。

```
const ffi = require('ffi');
const array = require('ref-array');
const StringArray = array('string');

let lib = ffi.Library('rstring/target/debug/librstring.so', {
  string_array: [StringArray, []]
});

let b = lib.string_array();
b.length = 2;
console.log(b); 
```

Enter fullscreen mode Exit fullscreen mode

我们在 Node.js 中定义了一个新的数据类型，叫做`StringArray`，借助`ref-array`将`Buffer`数据转换成一个`string`的数组。

```
const StringArray = array('string'); 
```

Enter fullscreen mode Exit fullscreen mode

因为它是一个`Array`，我们需要有固定的大小。所以我们需要指定一个数组的`length`来使它可读。

像这样:

```
$ node ffi.js
[ 'ï¿½ï¿½\u0002\u0002', '8+ï¿½ï¿½ï¿½~', buffer: <Buffer > ] 
```

Enter fullscreen mode Exit fullscreen mode

否则，你只会得到`Buffer`而不知道它的内容。

```
$ node ffi.js
[ buffer: <Buffer> ] 
```

Enter fullscreen mode Exit fullscreen mode

哦等等！什么？为什么有奇怪的弦？

还记得`std::mem::forget`吗？我们也有同样的问题。Rust 在退出`string_array()`函数时也释放了向量`v`。所以我们需要`forget`它。

```
#[no_mangle]
pub extern fn string_array() -> *const *const u8 {
    let v = vec![
        "Hello\0".as_ptr(),
        "World\0".as_ptr()
    ];
    let p = v.as_ptr();
    std::mem::forget(v);
    p
} 
```

Enter fullscreen mode Exit fullscreen mode

现在好了:

```
$ node ffi.js
[ 'Hello', 'World', buffer: <Buffer> ] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

玩`std::mem::forget`和泄露内存是不可取的，我们不应该过度使用它。

许多人建议，在生产中，我们不应该手动完成所有这些事情，利用现有的项目是一个更好的主意，例如 Mozilla Research 的负责人 Dave Herman 的 Neon。我完全同意这一点。他为此掉了很多头发，所以我们不需要掉我们的，jk。

* * *

我希望阅读这篇文章对你有帮助，就像写这篇文章对我有帮助一样。任何反馈都将不胜感激。