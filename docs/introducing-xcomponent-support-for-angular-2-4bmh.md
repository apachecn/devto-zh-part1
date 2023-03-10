# 介绍 xcomponent 对 angular 2+的支持

> 原文：<https://dev.to/harouny/introducing-xcomponent-support-for-angular-2-4bmh>

关于 xcomponent 框架本身或者如何创建组件的更多信息，请查看[这篇文章](https://medium.com/@bluepnume/introducing-xcomponent-seamless-cross-domain-web-components-from-paypal-c0144f3e82bf)作者[丹尼尔·布雷恩](https://medium.com/u/97f5eca783db)

#### Angular2 驱动

有了 xcomponent 3 . 0 . 0 版，利用新的 angular2 驱动程序将 x component 嵌入 angular 应用程序比以往任何时候都更容易。使用驱动程序将使 xcomponent 能够与特定于角度的概念(如输入绑定和变更检测周期)集成，从而带来更好、更可预测的体验。

#### 用法举例

假设在 angular 应用程序中，我们希望使用一个登录组件，该组件被声明为:

*(login.js)*

```
window.MyLoginXComponent = xcomponent.create({
 tag: 'my-login-component',
 url: 'http://www.component.com/login.htm',
 props: {
 prefilledEmail: {
 type: 'string',
 required: false
 },
 onLogin: {
 type: 'function',
 required: true
 }
 }
}); 
```

第一步是确保组件脚本加载到当前页面:

*【index.html】*

```
<script src="http://www.component.com/login.js"\></script\> 
```

然后，我们需要注册 angular2 驱动程序，并获取包含该组件的角度模块的引用:

*(我的-登录-x 组件-模块. ts)*

```
import * as ngCore from '@angular/core';
declare const MyLoginXComponent:any;
export const MyLoginXComponentModule = MyLoginXComponent.driver('angular2', ngCore); 
```

然后将该模块导入到要使用该组件的任何模块中:

( *app-module.ts* )

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app-component';
import { MyLoginXComponentModule } from './my-login-xcomponent-module';

@NgModule({

imports: [BrowserModule, MyLoginXComponentModule],
 declarations: [AppComponent],
 bootstrap: [AppComponent]
})

export class AppModule {
 constructor () {
 }
} 
```

现在，我们可以像使用应用程序中的任何其他组件一样使用该组件:

*【app-component.html】*

```
<my-login-component [props]="{ onLogin: onLogin, prefilledEmail: prefilledEmail }"/> 
```

*(app-component.ts)*

```
import { Component } from '@angular/core';

@Component({
 selector: 'my-app',
 templateUrl:'./app-component.html',
})
export class AppComponent {
 prefilledEmail:string;
 email:string;
 constructor() {
 this.prefilledEmail = 'foo@bar.com';
 this.onLogin = this.onLogin.bind(this);
 }
 public onLogin (email){
 this.email = email;
 }
} 
```

完整的示例代码可在以下网址找到:

*   [打字稿版本](https://github.com/krakenjs/xcomponent/tree/master/demo/frameworks/angular2_TypeScript)
*   [Javascript/UMD 版本](https://github.com/krakenjs/xcomponent/tree/master/demo/frameworks/angular2)

#### 注意事项:

*   为了注册 angular2 驱动程序，我们需要传递由" *@angular/core"* 模块导出的所有函数。这是为了确保如果我们后来决定使用另一个 angular 核心函数，你不必更新你的代码。您可以使用“*import * as”*技术访问具有所有导出函数的对象。

```
import * as ngCore from '@angular/core'; 
```

*   或者，你仍然可以只传递 angular2 驱动程序正在使用的函数

```
import { Component, NgModule, ElementRef, NgZone } from '@angular/core';

declare const MyLoginXComponent:any;
export const MyLoginXComponentModule = MyLoginXComponent.driver('angular2', { Component, NgModule, ElementRef, NgZone } ); 
```

*   包装角度组件的约定与创建 xcomponent 或手动呈现 x component 的约定相同；一个" *props"* 输入绑定，我们可以在其中添加底层 xcomponent 所需的属性和函数。
*   因为函数将在另一个上下文和潜在的另一个域中从 xcomponent 调用，我们需要确保我们"*将"*函数绑定回"*" this "*(类)，因为默认情况下它将绑定到 xcomponent 实例本身。这将确保我们可以安全地使用"*this "*inside "*onLogin "*函数来引用我们期望的组件类。

```
this.onLogin = this.onLogin.bind(this); 
```

我们对这一特性感到非常兴奋，因为它允许与 react、angularJs 和 glimmer 等其他驱动程序一起无缝集成以不同技术编写并托管在不同域上的组件。

如果您有任何问题，请随时联系我们。请将任何问题报告给 github 上的 [xcomponent repository。](https://github.com/krakenjs/xcomponent/issues)