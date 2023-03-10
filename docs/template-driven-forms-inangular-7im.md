# Angular 中的模板驱动表单

> 原文：<https://dev.to/abelagoi/template-driven-forms-inangular-7im>

模板驱动表单是我们在代码的模板部分(html 代码)编写逻辑、验证、控制等的表单。模板负责设置表单、验证、控制、分组等。模板驱动的表单适用于简单的场景，使用[(NgModel)]语法的双向数据绑定，更容易使用，尽管单元测试可能是一个挑战。

### 启用模板驱动表单？

要使用模板驱动的表单，我们需要在我们的应用程序模块中从`@angular/forms`
显式导入`{FormsModule}`

```
import { FormsModule } from '@angular/forms'; //note this

@Component({...})export class App { }

@NgModule({ 
    declarations: [App], 
    imports: [BrowserModule, FormsModule], //note this too 
    bootstrap: [App]
})
export class AppModule {}
platformBrowserDynamic().bootstrapModule(AppModule); 
```

### ngModel:

我们需要表单输入中的`ngModel`，输入也必须命名为:

```
<input type="text" ngModel name="firstName"> 
```

有些情况下，我们需要将事件侦听器传递给输入字段，或者将输入的值传递给组件，我们需要为输入分配一个模板变量来完成这些工作。

```
<input type="text" ngModel name="firstName" #firstName="ngModel" (change)="doSomething(firstName)"> 
```

ngModel 是`FormControl`的一个实例，它有很多控件，包括脏的、无效的、错误的、原始的、接触过的、未接触过的、值等等。FormControl 类用于跟踪输入的状态变化。

### 的形状:

ngForm 是 FormGroup 的一个实例。FormGroup 表示 FormControl 的组，每个表单都是一个 FormGroup，因为它至少有一个 FormControl，让我们可以访问可以绑定到组件中的方法的`(ngSubmit)`。

```
<form #f="ngForm" (ngSubmit)="submit(f)"> 
    <input type="text" ngModel 
           name="firstName" #firstName="ngModel" 
           (change)="doSomething(firstName)" 
    >
</form> 
```

`FormGroup`也有很多类似于`FormControl`的属性，比如 dirty、errors、invalid、parent 等。

### [T1】ngModelGroup:](#ngmodelgroup)

有时，在构建复杂的表单时。当我们需要使一个特定的对象成为一些其他输入的父对象时，可能会出现这种需求，因此我们可以在父对象下访问那些输入，因此需要`ngModelGroup`。我们可以在 person 对象下访问名字和姓氏。

```
<form #f="ngForm" (ngSubmit)="submit(f)"> 
    <div ngModelGroup="person"> 
        <input type="text" ngModel 
               name="firstName" #firstName="ngModel
               (change)="doSomething(firstName)" 
        >
        <input type="text" ngModel 
           name="lastName" #lastName="ngModel" 
           (change)="doAnotherthing(lastName)" 
        > 
    </div>
</form> 
```

### 处理选择选项？

为了处理选择选项，我们需要使用 ngModel，并像前面一样命名选择选项。如果它来自组件/后端
,我们就使用*ngFor 来遍历选项

```
//assuming we have an array of objects like below in our componentexport class MyFormComponent { 
    myOptions = [
        {id: 1, name: "abel"}, 
        {id: 2, name: "agoi"}, 
        {id: 3, name: "adeyemi"},
    ];
}

//below is how we will use the above in our template
//my-form-template.html
<select ngModel name="person"> 
    <option *ngFor="let myOption in myOptions" [value]="myOption.id"> 
        {{ myOption.name }} 
    </option>
</select>

//not coming from database
<select ngModel name="person"> 
    <option value="1">abel</option> 
    <option value="2">agoi</option> 
    <option value="3">adeyemi</option>
</select> 
```

### 处理复选框？

为了处理复选框，我们需要使用 ngModel，并像前面一样命名 select 选项。

```
<label> 
    <input type="checkbox" ngModel name="like"> Like this article?
</label> 
```

由于复选框没有 value 属性，所以当您单击复选框时，会将值 true 分配给 like，但当您取消选中时，将应用 false。

### 处理单选按钮？

为了处理单选按钮，我们需要使用 ngModel，并像前面一样命名 select 选项。

```
<label> 
    <input type="radio" ngModel name="clap" value="2"> 2 claps?
</label>
<label> 
    <input type="radio" ngModel name="clap" value="3"> 3 claps?
</label> 
```

选择以上任何一个单选按钮都会将 ngModel 的值更新为所选的值。我们也可以动态加载单选按钮，如下:

```
<label *ngFor="let myOption in myOptions"> 
    <input type="radio" ngModel name="clap" [value]="myOption.id">
    {{myOption.name}}
</label> 
```

* * *

### 添加验证

我们将研究添加 html 验证，并在验证失败时正确显示适当的错误。我们要挂钩的关键是表单控件或表单组的`touch`和`valid`状态

**必填:**用于强制用户输入必须填写。

```
<input type="text" ngModel name="firstName" required> 
```

如果验证失败，我们可以使用`FormControl`的 valid 属性来显示适当的消息，我们还需要确保在得出验证失败的结论之前表单已经被触摸过，因此需要`touch`

```
<input type="text" ngModel name="firstName" #firstName="ngModel" required>

<div *ngIf="firstName.touch && ! firstName.valid"> 
    Firstname is required
</div> 
```

错误消息`Firstname is required`仅在输入被触摸且未被填充时显示。我们必须始终指定表单控制变量`#firstName="ngModel"`来显示验证消息。

**Maxlength、Minlength 和 Pattern:**`Maxlength`用于强制输入可以接受的最大字符数。`Minlength`用于强制输入可以接受的最小字符数。模式属性主要用于表单输入正则表达式验证。当我们对一个输入进行多重验证时，我们可以像下面这样逐个检查错误:

```
<input type="text" ngModel name="firstName" #firstName="ngModel" minlength="3" maxlength="10" required>

<div *ngIf="firstName.touch && ! firstName.valid"> 
    <div *ngIf="firstName.errors.required"> Firstname is required </div 
    <div *ngIf="firstName.errors.minlength"> 
        minimum length is {{ firstName.errors.minlength.requiredLength }} 
    </div> 
    <div *ngIf="firstName.errors.maxlength"> 
        maximum length is {{ firstName.errors.maxlength.requiredLength }} 
    </div>
</div> 
```

**ng-invalid，ng-dirty，ng-touched:** angular 在输入无效，dirty，touched 时自动将这些类附加到我们的输入中。当验证失败或不满足要求时添加`ng-invalid`，当用户与输入交互时添加`ng-dirty`，当用户失去焦点时添加`ng-touched`。我们可以通过使用这些动态类来增加表单的可用性，如下所示:

```
<input type="text" ngModel name="firstName" 
       #firstName="ngModel" minlength="3" maxlength="10" required 
       class="form-control"
> 
```

假设我们在表单输入中添加了一个类`.form-control`,充分了解 angular 会在应该添加的时候添加`.ng-touched, .ng-dirty, .ng-touched`,我们可以将这些类添加到我们的样式中来增加表单交互。

```
.form-control.ng-touched.ng-invalid { 
    border: 2px solid red;
} 
```

[![](img/bb946b94f10e6cc81e7e2eb68226ad0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Jz__J2g---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AE-zIm5g7TB61OXmEgTsQag.png)

**禁用提交按钮:**我们可以在 angular 中轻松禁用表单提交按钮或任何输入。在我们的例子中，当整个表单无效时，我将禁用提交按钮。

```
<form #f="ngForm" (ngSubmit)="submit(f)"> 
    <input type="text" ngModel name="firstName" 
           #firstName="ngModel" (change)="doSomething(firstName)"
    > 
    <input type="text" ngModel name="lastName" 
           #lastName="ngModel" (change)="doAnotherthing(lastName)" 
    >
    <button [disabled]="!f.valid">Submit</button>
</form> 
```

我们将`[disabled]`赋给了`!f.valid`,`#f`是赋给表单的变量名。

这里做的验证只是内置的 html 5 验证，我们可以建立自己的自定义验证。还有另一种方法来建立形式的角度称为反应形式。我会写得太早。

我很高兴与你分享我对模板驱动表单的了解。如果你喜欢这篇文章，请鼓掌表示支持👏*。感谢您的宝贵时间，请务必关注我或在*下方留下您的评论👇