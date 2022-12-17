# 从其他语言调用 Go 函数

> 原文：<https://dev.to/vladimirvivien/calling-go-functions-from-other-languages>

从 1.5 版本开始，Go 编译器通过`-buildmode`标志引入了对几种构建模式的支持。这些构建模式被称为 [*go 执行模式*](https://docs.google.com/document/d/1nr-TQHw_er6GOQRsF6T43GGhFDelrAP0NqSS_00RgZQ/edit) ，它们扩展了 Go 工具，将 Go 包编译成多种格式，包括 Go 档案、Go 共享库、C 档案、C 共享库和(1.8 中引入的)Go 动态插件。

这篇文章是关于将 Go 包编译成 C 共享库的。在这种构建模式下，编译器输出一个标准的共享对象二进制文件(`.so`)，将 Go 函数公开为 C 风格的 API。这里我们讨论如何创建可以从 *C* 、 *Python* 、 *Ruby* 、 *Node* 和 *Java* 调用的 Go 库。

这里讨论的所有源代码都可以在 [GitHub](https://github.com/vladimirvivien/go-cshared-examples) 上找到。

## 围棋代码

首先，让我们编写 Go 代码。假设我们已经编写了一个`awesome` Go 库，我们希望它对其他语言可用。在将代码编译到共享库中之前，需要遵循四个要求:

*   该包必须是一个`main`包。编译器将把包及其所有依赖项构建成一个共享的二进制对象。
*   源必须导入伪包`"C"`。
*   使用`//export`注释来注释您希望其他语言可以访问的函数。
*   必须声明空的`main`函数。

下面的 Go 源代码导出了四个函数`Add`、`Cosine`、`Sort`和`Log`。诚然，令人敬畏的图书馆并不令人印象深刻。然而，它多样化的函数签名将帮助我们探索类型映射的含义。

文件[牛逼. go](https://github.com/vladimirvivien/go-cshared-examples/blob/master/awesome.go)

```
package main

import "C"

import (
    "fmt"
    "math"
    "sort"
    "sync"
)

var count int
var mtx sync.Mutex

//export Add
func Add(a, b int) int {
    return a + b
}

//export Cosine
func Cosine(x float64) float64 {
    return math.Cos(x)
}

//export Sort
func Sort(vals []int) {
    sort.Ints(vals)
}

//export Log
func Log(msg string) int {
    mtx.Lock()
    defer mtx.Unlock()
    fmt.Println(msg)
    count++
    return count
}

func main() {} 
```

使用`-buildmode=c-shared`构建标志编译包，以创建共享对象二进制文件:

```
go build -o awesome.so -buildmode=c-shared awesome.go 
```

完成后，编译器输出两个文件:C 头文件`awesome.h`和共享目标文件`awesome.so`，如下所示:

```
-rw-rw-r –    1362 Feb 11 07:59 awesome.h
-rw-rw-r – 1997880 Feb 11 07:59 awesome.so 
```

注意，`.so`文件大约 2 Mb，对于这样一个小的库来说相对较大。这是因为整个 Go 运行时机器和依赖包都被塞进了一个共享对象二进制文件中(类似于编译一个静态的可执行二进制文件)。

### 头文件

头文件使用 [cgo](https://golang.org/cmd/cgo/) 语义定义了映射到 Go 兼容类型的 C 类型。

```
/* Created by “go tool cgo” – DO NOT EDIT. */
...
typedef long long GoInt64;
typedef unsigned long long GoUint64;
typedef GoInt64 GoInt;
typedef double GoFloat64;
...
typedef struct { const char *p; GoInt n; } GoString;
typedef struct { void *data; GoInt len; GoInt cap; } GoSlice;
...
#endif
...
extern GoInt Add(GoInt p0, GoInt p1);
extern GoFloat64 Cosine(GoFloat64 p0);
extern void Sort(GoSlice p0);
extern GoInt Log(GoString p0);
... 
```

### 共享对象文件

编译器生成的另一个文件是一个`64-bit ELF`共享对象二进制文件。我们可以使用`file`命令来验证它的信息。

```
$> file awesome.so
awesome.so: ELF 64-bit LSB shared object, x86–64, version 1 (SYSV), dynamically linked, BuildID[sha1]=1fcf29a2779a335371f17219fffbdc47b2ed378a, not stripped 
```

使用`nm`和`grep`命令，我们可以确保我们的 Go 函数在共享对象文件中导出。

```
$> nm awesome.so | grep -e "T Add" -e "T Cosine" -e "T Sort" -e "T Log"
00000000000d0db0 T Add
00000000000d0e30 T Cosine
00000000000d0f30 T Log
00000000000d0eb0 T Sort 
```

## 从 C

使用共享对象库从 c 调用 Go 函数有两种方法，第一，我们可以在编译时静态绑定共享库，但在运行时动态链接。或者，让 Go 函数符号在运行时动态加载和绑定。

### 动态链接

在这种方法中，我们使用头文件静态引用共享对象文件中导出的类型和函数。代码简单明了，如下所示(省略了一些打印语句):

文件 [client1.c](https://github.com/vladimirvivien/go-cshared-examples/blob/master/client1.c)

```
#include <stdio.h>
#include "awesome.h"

int main() {
    printf("Using awesome lib from C:\n");

    GoInt a = 12;
    GoInt b = 99;
    printf("awesome.Add(12,99) = %d\n", Add(a, b)); 
    printf("awesome.Cosine(1) = %f\n", (float)(Cosine(1.0)));

    GoInt data[6] = {77, 12, 5, 99, 28, 23};
    GoSlice nums = {data, 6, 6};
    Sort(nums);
    ...
    GoString msg = {"Hello from C!", 13};
    Log(msg);
} 
```

接下来，我们编译 C 代码，指定共享对象库:

```
$> gcc -o client client1.c ./awesome.so 
```

当执行生成的二进制文件时，它链接到 awesome.so 库，调用从 Go 导出的函数，输出如下所示。

```
$> ./client
awesome.Add(12,99) = 111
awesome.Cosine(1) = 0.540302
awesome.Sort(77,12,5,99,28,23): 5,12,23,28,77,99,
Hello from C! 
```

### 动态加载

在这种方法中，C 代码使用动态链接加载器库(`libdl.so`)来动态加载和绑定导出的符号。它使用在`dhfcn.h`中定义的函数，比如`dlopen`打开库文件，`dlsym`查找符号，`dlerror`检索错误，`dlclose`关闭共享库文件。

因为绑定和链接是在源代码中完成的，所以这个版本更长。但是，它在做和以前一样的事情，如下面的代码片段所示(省略了一些打印语句和错误处理)。

文件 [client2.c](https://github.com/vladimirvivien/go-cshared-examples/blob/master/client2.c)

```
#include <stdlib.h>
#include <stdio.h>
#include <dlfcn.h>

// define types needed
typedef long long go_int;
typedef double go_float64;
typedef struct{void *arr; go_int len; go_int cap;} go_slice;
typedef struct{const char *p; go_int len;} go_str;

int main(int argc, char **argv) {
    void *handle;
    char *error;

    handle = dlopen ("./awesome.so", RTLD_LAZY);
    if (!handle) {
        fputs (dlerror(), stderr);
        exit(1);
    }

    go_int (*add)(go_int, go_int)  = dlsym(handle, "Add");
    if ((error = dlerror()) != NULL)  { ... }
    go_int sum = (*add)(12, 99); 
    printf("awesome.Add(12, 99) = %d\n", sum);

    go_float64 (*cosine)(go_float64) = dlsym(handle, "Cosine");
    go_float64 cos = (*cosine)(1.0);
    printf("awesome.Cosine(1) = %f\n", cos);

    void (*sort)(go_slice) = dlsym(handle, "Sort");
    go_int data[5] = {44,23,7,66,2};
    go_slice nums = {data, 5, 5};
    sort(nums);

    go_int (*log)(go_str) = dlsym(handle, "Log");
    go_str msg = {"Hello from C!", 13};
    log(msg);

    dlclose(handle);
} 
```

在前面的代码中，我们定义了自己的 Go 兼容 C 类型子集`go_int`、`go_float`、`go_slice`和`go_str`。我们使用`dlsym`来加载符号`Add`、`Cosine`、`Sort`和`Log`，并将它们分配给各自的函数指针。接下来，我们编译将它与`dl`库(不是 awesome.so)链接的代码，如下所示:

```
$> gcc -o client client2.c -ldl 
```

执行代码时，C 二进制文件加载并链接到共享库 awesome.so，产生以下输出:

```
$> ./client
awesome.Add(12, 99) = 111
awesome.Cosine(1) = 0.540302
awesome.Sort(44,23,7,66,2): 2,7,23,44,66,
Hello from C! 
```

## 来自 Python

在 Python 中，事情变得简单一些。我们使用`ctypes` [外来函数库](https://docs.python.org/3/library/ctypes.html)从 awesome.so 共享库中调用 Go 函数，如下面的代码片段所示(省略了一些打印语句)。

文件 [client.py](https://github.com/vladimirvivien/go-cshared-examples/blob/master/client.py)

```
from ctypes import *

lib = cdll.LoadLibrary("./awesome.so")
lib.Add.argtypes = [c_longlong, c_longlong]
print "awesome.Add(12,99) = %d" % lib.Add(12,99)

lib.Cosine.argtypes = [c_double]
lib.Cosine.restype = c_double 
cos = lib.Cosine(1)
print "awesome.Cosine(1) = %f" % cos

class GoSlice(Structure):
    _fields_ = [("data", POINTER(c_void_p)), 
                ("len", c_longlong), ("cap", c_longlong)]

nums = GoSlice((c_void_p * 5)(74, 4, 122, 9, 12), 5, 5)
lib.Sort.argtypes = [GoSlice]
lib.Sort.restype = None
lib.Sort(nums)

class GoString(Structure):
    _fields_ = [("p", c_char_p), ("n", c_longlong)]

lib.Log.argtypes = [GoString]
msg = GoString(b"Hello Python!", 13)
lib.Log(msg) 
```

注意`lib`变量代表从共享目标文件中加载的符号。我们还定义了 Python 类`GoString`和`GoSlice`来映射到它们各自的 C 结构类型。执行 Python 代码时，它调用共享对象中的 Go 函数，产生以下输出:

```
$> python client.py
awesome.Add(12,99) = 111
awesome.Cosine(1) = 0.540302
awesome.Sort(74,4,122,9,12) = [ 4 9 12 74 122 ]
Hello Python! 
```

## 来自红宝石

从 Ruby 调用 Go 函数遵循与上面类似的模式。我们使用 [FFI gem](https://github.com/ffi/ffi) 来动态加载和调用 awesome.so 共享对象文件中导出的 Go 函数，如下面的代码片段所示。

文件 [client.rb](https://github.com/vladimirvivien/go-cshared-examples/blob/master/client.rb)

```
require 'ffi'

module Awesome
  extend FFI::Library

  ffi_lib './awesome.so'

  class GoSlice < FFI::Struct
    layout :data,  :pointer,
           :len,   :long_long,
           :cap,   :long_long
  end

  class GoString < FFI::Struct
    layout :p,     :pointer,
           :len,   :long_long
  end

  attach_function :Add, [:long_long, :long_long], :long_long
  attach_function :Cosine, [:double], :double
  attach_function :Sort, [GoSlice.by_value], :void
  attach_function :Log, [GoString.by_value], :int
end

print "awesome.Add(12, 99) = ",  Awesome.Add(12, 99), "\n"
print "awesome.Cosine(1) = ", Awesome.Cosine(1), "\n"

nums = [92,101,3,44,7]
ptr = FFI::MemoryPointer.new :long_long, nums.size
ptr.write_array_of_long_long  nums
slice = Awesome::GoSlice.new
slice[:data] = ptr
slice[:len] = nums.size
slice[:cap] = nums.size
Awesome.Sort(slice)

msg = "Hello Ruby!"
gostr = Awesome::GoString.new
gostr[:p] = FFI::MemoryPointer.from_string(msg)
gostr[:len] = msg.size
Awesome.Log(gostr) 
```

在 Ruby 中，我们必须扩展`FFI`模块来声明从共享库中加载的符号。我们使用 Ruby 类`GoSlice`和`GoString`来映射各自的 C 结构。当我们运行代码时，它调用导出的 Go 函数，如下所示:

```
$> ruby client.rb
awesome.Add(12, 99) = 111
awesome.Cosine(1) = 0.5403023058681398
awesome.Sort([92, 101, 3, 44, 7]) = [3, 7, 44, 92, 101]
Hello Ruby! 
```

## 从节点

对于 Node，我们使用一个名为 [node-ffi](https://github.com/node-ffi/node-ffi) 的外部函数库(和几个依赖包)来动态加载和调用 awesome.so 共享对象文件中导出的 Go 函数，如下面的代码片段所示:

文件 [client.js](https://github.com/vladimirvivien/go-cshared-examples/blob/master/client.js)

```
var ref = require("ref");
var ffi = require("ffi");
var Struct = require("ref-struct");
var ArrayType = require("ref-array");

var LongArray = ArrayType(ref.types.longlong);

var GoSlice = Struct({
  data: LongArray,
  len:  "longlong",
  cap: "longlong"
});

var GoString = Struct({
  p: "string",
  n: "longlong"
});

var awesome = ffi.Library("./awesome.so", {
  Add: ["longlong", ["longlong", "longlong"]],
  Cosine: ["double", ["double"]],
  Sort: ["void", [GoSlice]],
  Log: ["longlong", [GoString]]
});

console.log("awesome.Add(12, 99) = ", awesome.Add(12, 99));
console.log("awesome.Cosine(1) = ", awesome.Cosine(1));

nums = LongArray([12,54,0,423,9]);
var slice = new GoSlice();
slice["data"] = nums;
slice["len"] = 5;
slice["cap"] = 5;
awesome.Sort(slice);

str = new GoString();
str["p"] = "Hello Node!";
str["n"] = 11;
awesome.Log(str); 
```

Node 使用`ffi`对象来声明从共享库中加载的符号。我们还使用节点结构对象`GoSlice`和`GoString`来映射到它们各自的 C 结构。当我们运行代码时，它调用导出的 Go 函数，如下所示:

```
awesome.Add(12, 99) =  111
awesome.Cosine(1) =  0.5403023058681398
awesome.Sort([12,54,9,423,9] =  [ 0, 9, 12, 54, 423 ]
Hello Node! 
```

## 来自 Java

为了从 Java 调用导出的 Go 函数，我们使用 [Java 本地访问库](https://github.com/java-native-access/jna)或 JNA，如下面的代码片段所示(省略或缩写了一些语句):

文件[Client.java](https://github.com/vladimirvivien/go-cshared-examples/blob/master/Client.java)

```
import com.sun.jna.*;

public class Client {
  public interface Awesome extends Library {
    public class GoSlice extends Structure {
      ...
      public Pointer data;
      public long len;
      public long cap;
    }

    public class GoString extends Structure {
      ...
      public String p;
      public long n;
    }

    public long Add(long a, long b);
    public double Cosine(double val);
    public void Sort(GoSlice.ByValue vals);
    public long Log(GoString.ByValue str);
  }

  static public void main(String argv[]) {
    Awesome awesome = (Awesome) Native.loadLibrary(
      "./awesome.so", Awesome.class);

    System.out.printf(... awesome.Add(12, 99));
    System.out.printf(... awesome.Cosine(1.0));

    long[] nums = new long[]{53,11,5,2,88};
    Memory arr = new Memory(... Native.getNativeSize(Long.TYPE));
    Awesome.GoSlice.ByValue slice = new Awesome.GoSlice.ByValue();
    slice.data = arr;
    slice.len = nums.length;
    slice.cap = nums.length;
    awesome.Sort(slice);

    Awesome.GoString.ByValue str = new Awesome.GoString.ByValue();
    str.p = "Hello Java!";
    str.n = str.p.length();
    awesome.Log(str);
  }
} 
```

为了使用 JNA，我们定义 Java 接口`Awesome`来表示从 awesome.so 共享库文件加载的符号。我们还声明了类`GoSlice`和`GoString`来映射到它们各自的 C 结构表示。当我们编译并运行代码时，它调用导出的 Go 函数，如下所示:

```
$> javac -cp jna.jar Client.java
$> java -cp .:jna.jar Client
awesome.Add(12, 99) = 111
awesome.Cosine(1.0) = 0.5403023058681398
awesome.Sort(53,11,5,2,88) = [2 5 11 53 88 ]
Hello Java! 
```

## 结论

这篇文章展示了如何创建一个可以在其他语言中使用的 Go 库。通过将他们的 Go 包编译成 C 风格的共享库，Go 程序员可以很容易地使他们的项目与 C、Python、Ruby、Node、Java 等一起工作。使用共享对象二进制文件的进程内集成。所以，下次你在 Go 中创建杀手级 API 时，记得与非 Go 开发者共享。

在推特上关注弗拉基米尔[@ vladimirvien](https://twitter.com/VladimirVivien)！

如果你正在学习围棋，可以看看弗拉基米尔·维维安(Vladimir Vivien)的一本关于围棋的书，书名为《学习围棋编程*》，由 Packt 出版社出版。*

[![Learning Go Programming Book](img/359b4c24a1998e272d5b944ac5129ac0.png)T2】](https://www.packtpub.com/application-development/learning-go-programming)

这篇文章最初由作者 Vladimir Vivien 以 [*的名字发表在 Medium 上，从其他语言*](https://medium.com/learning-the-go-programming-language/calling-go-functions-from-other-languages-4c7d8bcc69bf#.bwzvuz988) 调用 Go 函数。