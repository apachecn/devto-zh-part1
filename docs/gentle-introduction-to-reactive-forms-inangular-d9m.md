# 温和地介绍反应形式的角度

> 原文：<https://dev.to/abelagoi/gentle-introduction-to-reactive-forms-inangular-d9m>

反应式表单是我们在代码的组件类部分中编写逻辑、验证和控制的表单，与模板驱动表单不同，模板驱动表单在模板中完成控制。反应式表单非常灵活，可用于处理任何复杂表单场景。我们编写更多的组件代码和更少的 html 代码，这使得单元测试更容易。

### 启用反应式表单

要使用反应式表单，我们需要在我们的应用程序模块中从`@angular/forms`
显式导入`{FormsModule, ReactiveFormsModule}`

```
import { ReactiveFormsModule } from '@angular/forms';
@Component({...})
export class App { }
@NgModule({ 
    declarations: [App], 
    imports: [BrowserModule, ReactiveFormsModule], 
    bootstrap: [App]
})
export class AppModule {}
platformBrowserDynamic().bootstrapModule(AppModule); 
```

### 表单控件和表单组

你需要理解`FormControl`和`FormGroup`才能创建有角度的形式，因此我们将在下面讨论`FormControl`和`FormGroup`。

`FormControl`是一个类，它支持单个表单控件，跟踪值和验证状态，同时提供了一系列公共 API 方法。下面是一个`FormControl`
的基本例子

```
'firstname': new FormControl(''); 
```

`FormGroup`是一组 FormControl 实例，跟踪该组的值和验证状态，还提供公共 API。下面是`FormGroup`
的一个基本例子

```
this.myGroup = new FormGroup({ 
    'firstname': new FormControl(''), 
    'password': new FormControl('')
}); 
```

上面已经向我们展示了如何创建一个`FormControl`和一个`FormGroup`的实例，我们可以在我们的模板中使用它们，如下:

```
<form [formGroup]="myGroup"> 
    Firstname: <input type="text" formControlName="firstname"> 
    Password: <input type="password" formControlName="password">
</form> 
```

注意`<form [formGroup]="myGroup">`,`formGroup`必须等于用于初始化组件`myGroup`内`FormGroup`的名称。`formControlName`也必须等于用于初始化组件类中`FormControl`的名称。下面是 app.component.ts.
中的代码结构

```
//app.component.ts
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormControl } from "@angular/forms";

@Component({ 
    selector: 'app-root', 
    templateUrl: './app.component.html', 
    styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit { 
    title = 'reactive forms'; myGroup: FormGroup;
    constructor() {}

    ngOnInit() { 
        this.myGroup = new FormGroup({ 
            'firstname': new FormControl(''), 
            'password': new FormControl('') }); 
        }
    } 
```

下面的模板代码

```
<form [formGroup]="myGroup"> 
    <div class="form-group"> 
        <label for="firstname" class="label-control">Firstname:</label> 
        <input type="text" formControlName="firstname" class="form-control">        </div>

    <div class="form-group">
        <label for="password" class="label-control">Password:</label> 
        <input type="password" formControlName="password" class="form-control">     </div>
</form> 
```

### 嵌套表单组

有时，在构建复杂的表单时。如果我们需要使一个特定的值成为其他输入的父值，那么我们就可以在父值下访问这些输入。在下面的例子中，`account`将是`firstname`和`password`的父代，这意味着我们将能够访问账户下的名字和密码。

```
@Component({...})
export class SignupFormComponent { 
    form = new FormGroup({ 
        'email': new FormControl(''), 
        'account': new FormGroup({ 
            'firstname': new FromControl(''), 
            'password': new FromControl(''), 
        })
    });
} 
```

我们可以在模板中表示嵌套的表单组，如下图

```
<form [formGroup]="form"> 
    <div formGroupName="account">
        <div class="form-group"> 
            <label for="firstname">Firstname: </label> 
            <input formControlName="firstname" id="firstname" 
                type="text" class="form-control" > 
        </div>

        <div class="form-group"> 
            <label for="password">Password: </label> 
            <input formControlName="password" id="password" 
                    type="password" class="form-control" > 
        </div>
    </div>
</form> 
```

注意,`formGroupName`等于`account`, T1 是分配给组件中 FormGroup 实例的名称。`formGroupName`充当名字和密码表单控件的父控件。

### 用表单生成器简化

我们可以不直接使用`FormGroup`和`FormControl`，而是使用一个神奇的 API 来为我们做所有的事情。所以不需要进口`FormGroup`和`FormControl`，我们只需要进口`FormBuilder`和

```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder } from "@angular/forms";

@Component({ 
    selector: 'app-root', 
    templateUrl: './app.component.html', 
    styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit { 
    title = 'reactive forms'; 
    myGroup: FormGroup;

    constructor(private formBuilder: FormBuilder) {}

    ngOnInit() { 
        this.myGroup = this.formBuilder.group({ 
            'firstname': this.formBuilder.control(''), 
            'password': this.formBuilder.control('') 
        }); 
    }
} 
```

### 被动提交

为了在用户提交表单时获取数据，我们将把`(ngSubmit)="handleSubmit()"`应用到`<form>`中，后者将链接到我们组件中的一个方法来处理提交的数据

```
<form [formGroup]="myGroup" (ngSubmit)="handleSubmit()">
    <div class="form-group"> 
        <label for="firstname" class="label-control">Firstname:</label 
        <input type="text" formControlName="firstname" class="form-control">        </div>

    <div class="form-group"> 
        <label for="password" class="label-control">Password:</label> 
        <input type="password" formControlName="password" class="form-control">     </div>

    <button class="btn btn-primary">submit</button>
</form> 
```

我们可以访问组件中的表单数据，如下所示:

```
handleSubmit() { 
    console.log(this.form.value); 
} 
```

### 添加验证

为了给反应式表单添加验证，我们需要从`@angular/forms`导入`Validators`类，并将`validation(s)`作为第二个参数传递给我们的`FormControl`实例。

```
import { FormBuilder, Validators } from '@angular/form';
@Component({...})
export class SignupFormComponent implement OnInit {

    constructor(private formBuilder: FormBuilder) {}

    ngOnInit() { 
        this.myGroup = this.formBuilder.group({ 
            'firstname': this.formBuilder.control('', Validators.required),                 'password': this.formBuilder.control('', Validators.required) 
        }); 
    }

} 
```

当我们需要使用多个验证器时，我们将第二个参数设为如下所示的数组:

```
'firstname': ['', [Validators.required, Validators.minLength(2)]] 
```

### 显示验证错误

我们可以通过监听`FormControl`中的`errors`对象向模板添加验证错误消息。感谢 typescript getter，我用它让模板更干净

```
import { Component, OnInit } from '@angular/core';
import { FormGroup, FormBuilder, Validators } from "@angular/forms";

@Component({ 
    selector: 'app-root', 
    templateUrl: './app.component.html', 
    styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit { 
    title = 'reactive forms'; 
    myGroup: FormGroup;

    constructor(private formBuilder: FormBuilder) {} 
    ngOnInit() { 
        this.myGroup = this.formBuilder.group({ 
            'firstname': this.formBuilder.control('',Validators.required),                  'password': this.formBuilder.control('', Validators.required) 
        }); 
    }

    get firstname() { 
        return this.myGroup.get('firstname'); //notice this 
    }

    get password() { 
        return this.myGroup.get('password'); //and this too 
    }
} 
```

我们可以在模板
中显示如下验证错误

```
<form [formGroup]="myGroup" (ngSubmit)="handleSubmit()"> 
    <div class="form-group"> 
        <label for="firstname" class="label-control">Firstname:</label> 
        <input type="text" formControlName="firstname" class="form-control" >

        <div *ngIf="firstname.invalid && firstname.touched"> 
            <div *ngIf="firstname.errors.required"> Firstname is required </div>         </div> 
    </div>

    <div class="form-group"> 
        <label for="password" class="label-control">Password:</label> 
        <input type="password" formControlName="password" class="form-control" >

        <div *ngIf="password.invalid && password.touched"> 
            <div *ngIf="password.errors.required"> Password is required </div>          </div> 
    </div>

    <button class="btn btn-primary">submit</button>
</form> 
```

### 禁用提交按钮

当表单无效时，我们可以禁用提交按钮，方法是挂钩到表单组有效状态，如下所示，注意`myGroup`是表单控件。

```
<button class="btn btn-primary" [disabled]="! myGroup.valid"> submit</button> 
```

除了像自定义验证和异步验证这样的反应式表单，还有很多文章可以帮助它们。

很高兴与你分享这篇文章。如果你喜欢这篇文章，请鼓掌表示支持👏*。感谢您的宝贵时间，请务必关注我或在*下方留下您的评论👇