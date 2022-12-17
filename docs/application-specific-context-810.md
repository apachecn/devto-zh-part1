# 特定于应用的上下文

> 原文：<https://dev.to/nasa9084/application-specific-context-810>

[![Application Specific Context](img/239b7a4e9318adf356b2fb22bcc564b7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4NGA80Jf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.web-apps.tech/conteimg/2017/11/gopher-1.png)

元ネタは[@莱斯特拉特](https://twitter.com/lestrrat) さんの「 [滥用类型别名来扩充上下文。语境](https://medium.com/@lestrrat/abusing-type-aliases-to-augment-context-context-a08a85692fa8)；

使用 golang 制作 HTTP 服务器时，使用以下这样的函数来定义路由。

```
http.HandleFunc(path string, handler func(w http.ResponseWriter, r *http.Request) 
```

Enter fullscreen mode Exit fullscreen mode

当然，我想既有使用`http.Handle`的情况，也有使用[gorilla/mux](https://github.com/gorilla/mux) 等程序库的情况。

这里重要的是`func(w http.ResponseWriter, r *http.Request)`这个自变量。

在许多情况下，APP 应用程序的处理程序会使用外部 APP 应用程序中间件，如数据库。

但是，在 golang 的 http APP 应用中，处理函数的形式规定为`func (w http.ResponseWriter, r *http.Request)`。 因为不能添加参数，所以必须用参数以外的方法传递数据库连接信息等。

到目前为止，在 golang 上进行 web APP 应用程序开发时经常使用的数据库连接的保持方法，是制作`db`程序包，并将其作为程序包变量持有的方法。 但是，[是尽量不想拥有全局变量的](https://dave.cheney.net/2017/06/11/go-without-package-scoped-variables)吧。

因此，可以使用从 Go 1.8 中追加的[`context`](https://golang.org/pkg/context)。 [`http.Request`](https://golang.org/pkg/net/http#Reqeuest)中加入了`context.Context`，可以用[`Request.Context()`](https://golang.org/pkg/net/http#Request.Context)设定 get，用[`Request.WithContext()`](https://golang.org/pkg/net/http#Request.WithContext)设定。

关于使`context.Context`具有值的方法，最先想到的是使用[`Context.WithValue()`](https://golang.org/pkg/context#Context.WithValue)的方法，但这需要在每次取得值时进行类型辅助，[不太好](https://medium.com/@lestrrat/alternative-to-using-context-value-f2efe6bd2788)。

为了消除这种情况，自己定义模具比较好吧。

```
package context // internal context subpackage

import (
    "context"
    "errors"
)

type withSomethingContext struct {
    context.Context
    something *Something
}

func WithSomething(ctx context.Context, something *Something) context.Context {
    return &withSomethingContext{
        Context: ctx,
        something: something,
    }
}

func Something(ctx context.Context) (*Something, error) {
    if sctx, ok := ctx.(*withSomethingContext); ok {
        if sctx.something != nil {
            return sctx.something, nil
        }
    }
    return nil, errors.New(`no asscosiated something`)
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这样定义，不需要每次都进行 type assertion，变得很清晰。

为了同时读取这个包装和`context`包装，需要更改其中一个读取名称。

例如，以下情况。

```
import (
    "context"
    mycontext "github.com/hoge/fuga/context" 
```

Enter fullscreen mode Exit fullscreen mode

另外，在源代码中也需要分别使用`context`和`mycontext`，很麻烦。

这个问题可以用 Go 1.9 引入的[Type Alias](https://golang.org/doc/go1.9#language) 写得很漂亮。

```
import "context"

type Context = context.Context 
```

Enter fullscreen mode Exit fullscreen mode

这样写的话，标准软件包的`context.Context`和该 APP 应用中的`context.Context`将被视为相同的东西处理。

因此，只需要导入一个包就可以了。

最终的`context`子包的代码应该如下所示。

```
package context // internal context subpackage

import (
    "context"
    "errors"
)

type Context = context.Context
/*
** some more definition
*/

type withSomethingContext struct {
    Context
    something *Something
}

func WithSomething(ctx Context, something *Something) Context {
    return &withSomethingContext{
        Context: ctx,
        something: something,
    }
}

func Something(ctx Context) (*Something, error) {
    if sctx, ok := ctx.(*withSomethingContext); ok {
        if sctx.something != nil {
            return sctx.something, nil
        }
    }
    return nil, errors.New(`no asscosiated something`)
} 
```

Enter fullscreen mode Exit fullscreen mode

实际上，要与标准软件包的`context`等同使用，需要重新定义其他定义，或者定义多个`withXXXContext`时需要递归读取值的处理，但基本上使用该形式很方便。

这样定义`context`，如下使用。

```
func withSomethingMiddleware(h http.Handler) http.Handler {
    return http.Handler(func(w http.ResponseWriter, r *http.Request) {
        something := &Something{}
        r = r.WithContext(context.WithSomething(r.Context(), something))
        h.ServeHTTP(w, r)
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

关于`http.Handler`的 middleware，下次有机会再聊。