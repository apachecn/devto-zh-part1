# 围棋的方法是柯里函数

> 原文：<https://dev.to/mattn/gos-method-is-curried-funtion-c3f>

Go 的方法在其他语言中并不相同。在 C++中，`this`不应该为 null。

```
class Foo {
  public:
    void doSomething() {}
};

int
main() {
  Foo *foo = nullptr;
  foo->doSomething();
} 
```

Enter fullscreen mode Exit fullscreen mode

Go 的方法不需要实例化接收者。

```
package main

import (
    "fmt"
)

type Foo struct {
}

func (f *Foo) doSomething() {
    fmt.Println(f)
}

func main() {
    var foo *Foo
    foo.doSomething()
} 
```

Enter fullscreen mode Exit fullscreen mode

并且该方法保存接收器值。这被称为[方法值](https://golang.org/ref/spec#Method_values)。

```
package main

import (
    "fmt"
)

type Foo struct {
    Value string
}

func (f *Foo) WhatsUp() {
    fmt.Println(f.Value)
}

func doSomething(f func()) {
    f()
}

func main() {
    foo := &Foo{Value: "Hello, World"}
    doSomething(foo.WhatsUp)
} 
```

Enter fullscreen mode Exit fullscreen mode

用接收器评估`WhatsUp`。

```
package main

import (
    "fmt"
)

type Foo int

func (f Foo) doSomething() {
    fmt.Println(f)
}

func main() {
    Foo.doSomething(123)
} 
```

Enter fullscreen mode Exit fullscreen mode

所以这段代码是有效的。`Foo.doSomething`是函数对象。它可以用 let 函数对象来调用一个变量。函数类型应该接受第一个参数，即接收方的类型。

```
package main

import (
    "fmt"
)

type Foo int

func (f Foo) doSomething() {
    fmt.Println(f)
}

func main() {
    var fn func(Foo)

    fn = Foo.doSomething
    fn(123)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个习语对于调用`runtime.SetFinalizer`到`Close`某些必须关闭的资源是有用的。

```
package main

import (
    "log"
    "os"
    "runtime"
    "myresource"
)

func main() {
    f := myresource.OpenSomethingMustBeClosed()
    if err != nil {
        log.Fatal(err)
    }
    runtime.SetFinalizer(f, (*myresource.Writer).Close)
} 
```

Enter fullscreen mode Exit fullscreen mode