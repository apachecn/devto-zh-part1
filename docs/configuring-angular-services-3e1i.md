# 配置角度服务

> 原文：<https://dev.to/prestonjlamb/configuring-angular-services-3e1i>

本周，我需要将一个角度模块转换成一个 npm 包，以便导入到多个项目中。最难的部分是在这个模块中，有一个需要配置的发送到 URL 的服务。幸运的是，Angular 提供了一种非常简单的方法来做到这一点，尽管它确实花了一些时间来学习如何做到这一点。希望在这篇博文结束时，你能在你的应用程序中做同样的事情。

你应该做的第一件事是在[角度文档](https://angular.io/guide/ngmodule#configure-core-services-with-coremoduleforroot)上阅读这方面的内容。它讲述了如何使用模块的`forRoot`方法来配置服务。要做到这一点，有 4 个步骤:

*   创建一个用于服务配置的配置类
*   在模块的`forRoot`方法中提供配置。
*   在服务本身中使用配置对象
*   导入模块时传入配置对象

## 制作一个配置类

这一部分非常简单，但却是必要的一步。在您导出的自己的文件中创建一个类，该类有一个构造函数和适合您的用例的正确数量的属性。它可以是单个属性，也可以是多个属性。任何数字都可以。

```
export class ServiceConfig {
    public myAttr: string;

    constructor(obj: any) {
        this.myAttr = obj.myAttr;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您将在模块文件和服务中使用这个类，所以准备好在这些地方导入它。

## 提供模块中的配置

例如，在模块文件`my-module.ts`中，您将需要一个`forRoot`方法来提供服务和配置，以便您可以在导入模块时传递它。您的模块将如下所示:

```
...

export class MyModule {
    static forRoot(config: ServiceConfig): ModuleWithProviders {
        return {
            ngModule: MyModule,
            providers: [
                { provide: ServiceConfig, useValue: config },
                MyService
            ]
        };
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使用服务中的配置对象

在模块中提供配置之后，我们还必须在服务中使用配置对象。此零件需要添加到您的服务中，`my-service.ts`。

```
...

private myAttr: string;

constructor(@Optional() config: ServiceConfig) {
    this.myAttr = config.myAttr;
} 
```

Enter fullscreen mode Exit fullscreen mode

您不一定需要在构造函数中的参数上有`@Optional()`装饰符，但是如果您需要的话，这是可能的。在构造函数内部，将局部变量设置为传入的值。

## 导入时传入配置

最后一步是在导入模块时传入 config 对象。假设您正在将`MyModule`导入到您的`AppModule`中。您的导入数组将包括这个(很明显，这只有一行):

```
imports: [
    ...,
    MyModule.forRoot({ myAttr: 'Here is the value I want to pass in'}),
    ...,
] 
```

Enter fullscreen mode Exit fullscreen mode

这将传入我们定义的 config 对象，然后您可以在您的服务中使用该值。

## 结论

总的来说，这很简单，但我还是花了一点时间让一切正常工作并弄清楚。希望这能帮助你，减少你起床跑步的时间。