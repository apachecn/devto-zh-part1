# Angular 中窗口小部件的输入集模式

> 原文：<https://dev.to/michaeljota/input-set-pattern-for-widgets-in-angular>

## [T1】简介](#intro)

如果你读了我的上一篇文章，你应该知道什么是 Widget，如果你还不知道，现在就做吧，我会*等待*。

`await Reader.read('` [小部件和组件](https://dev.to/michaeljota/widgets-and-components-for-angular-2-and-up) `');`

现在你知道什么是小部件了。我试图在第一次实现时保持简单，但我真的开始改变主意了。问题是，React 是一个纯 JavaScript 框架，通过 JSX，我们可以做普通 HTML 模板做不到的事情，然而，Typescript 允许我们做 JavaScript 做不到的事情，至少，它似乎允许这样做。像*私有*属性。记住这一点，我将尝试解释为什么应该在小部件中使用私有属性，以及如何以更好的方式管理数据输入。

## 形势

根据上一篇文章中的反应，当`props`被发送到`component`时，大多数时候我们都是按照破坏性的模式得到它，允许做像
这样的事情

```
interface IUser {
  firstname: string;
  lastname: string;
  age: number;
}

export function usersList({users = []} : { IUser[] }) {
  return (<ul>
    users.map(user => userDetails(user));
  </ul>)
}

function userDetails({firstname, lastname, age}: IUser) {
    return (<li><span>{firstname} {lastname}</span>have {age} years old</li>);
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为这看起来不错，因为它让我们清楚地知道我们想展示什么，以及如何展示。

在 Angular 中，我们只是`@Input`我们想从外部获得的数据。

```
// user.ts
interface IUser {
  firstname: string;
  lastname: string;
  age: number;
}

// user-list.widget.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'my-user-list',
  template: `
  <ul>
    <li *ngFor="let user of users">
      <my-user-details [user]=user><my-user-details>
    </li>
  </ul>
  `
})
export class UserListWidget {
  @Input()
  public users: IUser[];
}

// user-details.widget.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'my-user-details',
  template: 
    `<span>{{user.firstname}} {{user.lastname}}</span> has {{user.age}} years old`,
})
export class UserDetailsWidget {
  @Input()
  public user: IUser;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是正确的，因为小部件应该显示数据，而且它们应该*只*关心如何显示数据。但是我们需要一种更好的方法来组织如何在模板中正确显示数据。

我喜欢 React 在这方面的做法。别误会，我喜欢棱角分明。但是 JSX 允许我们做一些比点属性更好的事情，破坏性的赋值。

## 解决方案建议

既然 React 比我们更有优势，我们作为 Angular 开发者需要提出一个解决方案，使用 Typescript、 *private* properties 带来的特性。我知道 Typescript 是编译成 JavaScript 的，JavaScript 是没有这种东西的，但是，我们会照做不误。
此外，我们将使用 ES6 的`set`功能。这允许我们拥有可设置但不可读取属性。

### 介绍`@input set`

```
import { Component, Input } from '@angular/core';

@Component({
  selector: 'my-user-details',
  template: 
    `<span>{{firstname}} {{lastname}}</span> has {{age}} years old`,
})
export class UserDetailsWidget {
  private _user: IUser;

  @Input()
  public set user(value: User): void {
    this._user = value;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

好的。我们知道我们可以设置私有属性，但是这并不允许我们在模板中显示属性。从技术上讲，JavaScript 只有属性，所以我们可以绑定到私有属性，但是我们应该*永远不要*将私有属性绑定到模板，因为 AOT 编译器会抛出。

### 介绍`template get`

```
@Component({
  selector: 'my-user-details',
  template: 
  `<span>{{firstname}} {{lastname}}</span> has {{age}} years old`,
})
export class UserDetailsWidget {
  private _user: IUser;
  @Input()
  public set user(value: IUser) {
    this._user = value;
  }

  public get firstname(): string {
    return this._user.firstname;
  }
  public get lastname(): string {
    return this._user.lastname;
  }
  public get age(): age {
    return this._user.age;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这实际上像预期的那样工作。允许更好地控制在模板中显示的数据，以及如何公开它。此外，它甚至允许我们做某些事情，以更好的方式维护代码。

```
@Component({
  selector: 'my-user-details',
  template: 
  `<span>{{fullname}}</span> has {{age}} years old`,
})
export class UserDetailsWidget {
  private _user: IUser;
  @Input()
  public set user(value: IUser) {
    this._user = value;
  }

  public get fullname(): string {
    return `${this._user.firstname}  ${this._user.lastname}`;
  }
  public get age(): number {
    return this._user.age;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，这里显示的有一定的问题，但是我会在其他的帖子里解释关于这些问题的一切。

## 举例

为此，我做了一个暴露用例的小插件。[这里](http://plnkr.co/edit/qQMk8kCH0wWp3yznE6MX?p=preview)

## 谢谢

一如既往地感谢你读了这么长时间。我希望你喜欢它，我真的很期待在评论区看到你的想法。