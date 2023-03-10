# 有角度的 HTTP 错误拦截器

> 原文：<https://dev.to/prestonjlamb/angular-http-error-interceptors-dpg>

Angular 4.3 引入了 HTTP 拦截器。这使您能够在一个地方处理每个传出的 http 调用和每个传入的响应。我在那里看到的大多数例子都涵盖了第一种情况，每个调用都有一个附加到调用上的身份验证令牌。我需要一种方法来处理和显示 HTTP 调用中的错误，并且不想在我进行的每个调用中都进行错误处理。这是一个如何在你的应用中实现这一点的简单例子。

正如我提到的，`HTTP_INTERCEPTORS`是在 Angular 4.3 中引入的。要使用它们，你需要使用`@angular/common/http`包装里的`HttpClient`，而不是`@angular/http`。要编写自定义拦截器，需要实现 HttpInterceptor，这需要一个拦截方法。正是在这种方法中，您可以捕捉错误并处理它们。下面是一个例子:

> _ **编辑:**第一个例子是针对 RxJS pre 6.0 版本。第二个例子是针对 RXJS 6.0+的。_

```
// RxJS < 6.0
return next.handle(req)
    .do((ev: HttpEvent) => {
        if (ev instanceof HttpResponse) {
            console.log('ev in the do: ', ev);
        }
    })
    .catch((response: any) => {
        if (response instanceof HttpErrorResponse) {
            console.log('response in the catch: ', response);
            toaster.error('Unexpected Error', response.message);
        }

        return Observable.throw(response);
    });

// RxJS 6.0+
return next.handle(req).pipe(
    tap(
        (incoming: any) => {},
        (error: HttpErrorResponse) => {
            // Handle Error
            return of(error);
        }
    )
) 
```

Enter fullscreen mode Exit fullscreen mode

这里的错误处理发生在 catch 方法中。稍后我将展示更多关于`toaster`变量的来源，但这就是应用程序如何全局显示错误。

为了让这个拦截器真正工作，您需要在模块的`providers`属性下的`app.module.ts`文件中提供它。像这样做:

```
...
{
    provide: HTTP_INTERCEPTORS,
    useClass: ErrorInterceptorService,
    multi: true
}
... 
```

Enter fullscreen mode Exit fullscreen mode

这是捕捉应用程序所有全局错误的基本方法。我现在将展示我所走路线的更多细节。当一个人回来时，我使用`angular2-toaster`来显示错误。我做了一个服务`ToasterNotificationService`，它包装了包的函数来显示错误。该服务需要注入到我们制作的拦截器服务中。我们需要显式地使用角度注入器来获得`ToasterNotificationService`的一个实例。这样做:

```
intercept (req: HttpRequest, next: HttpHandler): Observable> {
    const toaster = this.injector.get(ToasterNotificationService);

    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

另外，确保将自定义的`ToasterNotificationService`、`ToasterModule`(来自 angular2-toaster)和`ToasterService`包含到`app.module.ts`的提供者和模块声明中。

有了所有这些设置，您应该能够捕捉错误，然后使用 toaster 通知服务来显示该错误。angular2-toaster 已经被证明是一种简单的方法来显示这些错误，而不必重写大量的定制代码。你也可以做很多定制工作；看看他们的文件。

希望这些都能在某种程度上帮助你。但是为了让它变得更好，我做了一个小的 Angular 应用程序来展示这些工作。它就在 GitHub 上。 [这个 StackBlitz 项目](https://stackblitz.com/edit/global-error-handler)也有这样的例子。检查一下，如果有任何问题，请告诉我。你可以通过[推特](https://www.twitter.com/plambweb)或者[电子邮件](//mailto:preston@prestonlamb.com)联系我。感谢您花时间阅读这篇文章，希望它在某些方面有所帮助！