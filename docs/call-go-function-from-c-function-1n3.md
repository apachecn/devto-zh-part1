# 从 C 函数调用 Go 函数

> 原文：<https://dev.to/mattn/call-go-function-from-c-function-1n3>

Cgo 在 go 中嵌入 C 函数很有用。

```
package main

/*
void doSomething(int *p) {
  *p = 123;
}
*/
import "C"

func main() {
    var n C.int
    C.doSomething(&n)
    println(n)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个打印机 123 是从 c .开始设置的，并按预期工作，没有问题。所以第一眼看上去，你可能会认为你以后不会遇到任何问题。

请想象一下，当您想要调用采用回调函数和 user_data 指针的 API 时，会出现什么情况。

### 原料药

```
typedef void (*callback)(void *);
void register_callback(callback cb, void *user_data);
void wait_event(); 
```

Enter fullscreen mode Exit fullscreen mode

正如您所猜测的，`register_callback`注册了一个回调函数，`wait_event`等待某个事件，并在事件发生时调用回调函数。

### 去吧

```
func my_callback(v unsafe.Pointer) {
}

func main() {
    user_data := "my userdata"
    C.register_event(/* ? */, /* ? */)
    C.wait_event()
} 
```

Enter fullscreen mode Exit fullscreen mode

你想知道如何通过`my_callback`或`user_data`进入`register_event`吗？可惜 Go 中 my_callback 的类型和 C.callback 不一样，总之下面写不出来。

```
C.register_event(my_callback, &user_data) // compilation errors 
```

Enter fullscreen mode Exit fullscreen mode

要创建一个可以从 C 中调用的函数，需要 hack。

*   要从 C 中调用的导出 Go 函数
*   从 Go 传递有效指针

要导出 Go 函数，请将注释行`//export FuncName`放在函数上方。

```
//export hello
func hello() {
    // do something
} 
```

Enter fullscreen mode Exit fullscreen mode

哇，这太简单了。因此，可以使用回调代理。

```
package main

/*
#include <myapi.h>

void cb_proxy(void *v);

static void _register_callback(void *user_data) {
  register_callback(cb_proxy, user_data);
}
*/
import "C"
import (
    "fmt"
)

func my_callback(v string) {
    fmt.Println("hello", v)
}

func main() {
    C._register_callback(...)
    C.wait_event()
}

//export cb_proxy
func cb_proxy(v unsafe.Pointer) {
    // call my_callback
} 
```

Enter fullscreen mode Exit fullscreen mode

`_register_callback`是`register_callback`的 wapper 给`cb_proxy`打电话。`wait_event`将使用参数 user_data 在内部调用回调。user_data 应该从`main`传过来。然而，你会遇到另一个问题。

[Go 和 C 之间传递指针的规则](https://github.com/golang/proposal/blob/master/design/12416-cgo-pointers.md)

不幸的是，Go 不能传递在 Go into C 函数中分配的指针。

但这是可能的。参见[https://github.com/mattn/go-pointer](https://github.com/mattn/go-pointer)。这个包在 Go 和 c 之间交换指针

```
var s string
C.pass_pointer(pointer.Save(&s))
v := *(pointer.Restore(C.get_from_pointer()).(*string)) 
```

Enter fullscreen mode Exit fullscreen mode

作为技巧，go-pointer 分配 1 字节的虚拟内存用于传递给 c。即，这是与真实 Go 指针相关的地图的唯一键。`pointer.Save(&v)`将 Go 指针存储到映射中，并返回 C 指针，该指针被分配为哑指针。

```
package main

/*
#include <myapi.h>

void cb_proxy(void *v);

static void _register_callback(void *user_data) {
  register_callback(cb_proxy, user_data);
}
*/
import "C"
import (
    "fmt"
    "unsafe"

    "github.com/mattn/go-pointer"
)

type Callback struct {
    Func     func(string)
    UserData string
}

func my_callback(v string) {
    fmt.Println("hello", v)
}

func main() {
    C._register_callback(pointer.Save(&Callback{
        Func:     my_callback,
        UserData: "my-callback",
    }))
    C.wait_event()
}

//export cb_proxy
func cb_proxy(v unsafe.Pointer) {
    cb := pointer.Restore(v).(*Callback)
    cb.Func(cb.UserData)
} 
```

Enter fullscreen mode Exit fullscreen mode

`_register_callback`通过`cb_proxy`。而`cb_proxy`将指针恢复为`*Callback`。它用`Callback.UserData`称呼原来的`Callback.Func`。可以从 Go 传递回调函数给 C。

您可以在这段代码中尝试这种行为。

```
package main

/*
typedef void (*callback)(void *);

static callback _cb;
static void *_user_data;
static void register_callback(callback cb, void *user_data) {
    _cb = cb;
    _user_data = user_data;
}
static void wait_event() {
    _cb(_user_data);
}

void cb_proxy(void *v);

static void _register_callback(void *user_data) {
  register_callback(cb_proxy, user_data);
}
*/
import "C"
import (
    "fmt"
    "unsafe"

    "github.com/mattn/go-pointer"
)

type Callback struct {
    Func     func(string)
    UserData string
}

func my_callback(v string) {
    fmt.Println("hello", v)
}

func main() {
    C._register_callback(pointer.Save(&Callback{
        Func:     my_callback,
        UserData: "my-callback",
    }))
    C.wait_event()
}

//export cb_proxy
func cb_proxy(v unsafe.Pointer) {
    cb := pointer.Restore(v).(*Callback)
    cb.Func(cb.UserData)
} 
```

Enter fullscreen mode Exit fullscreen mode