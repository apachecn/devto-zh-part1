# 角度窗口小部件中图元属性的只读输入

> 原文：<https://dev.to/michaeljota/readonly-inputs-for-primitives-properties-in-widgets-for-angular>

嗨！又是我。今天我发布了我的 Pomodoro 应用程序的第一个稳定版本。我用角 4 和电子做的。在我开发它的时候，我试着使用 TDD，使用 ngrx/Store，分离组件[和部件](https://dev.to/michaeljota/widgets-and-components-for-angular-2-and-up)，使用[、输入集和模板获取来管理数据。](https://dev.to/michaeljota/input-set-pattern-for-widgets-in-angular)

但我不得不说，我失败了，我彻底失败了。首先，我开始意识到为什么 TDD 是一种设计模式，如果你没有定义你想做什么，你就不能首先测试。我尝试使用存储，但最终我从一个组件管理所有状态。但是我确实在可能的地方使用了小部件来显示内容。嗯，这实际上是我自己做的第一个应用程序，所以，不要吝啬。我从 1.2 开始使用 AngularJS，从 RC5 开始使用 Angular，但我从来没有设计过应用程序的功能，业务逻辑是我工作过的公司的一部分。

然而，我最失败的地方是使用@Input-set/template-get duo。我的意思是，我可以使用它们，但我主要管理原语值，所以为什么要麻烦呢？不过，这种方法的一个优点是，你可以把值当作不变量，因为你把它设置在某个地方，然后把它返回到另一个地方，这样就不会有副作用了。

那么如何处理原语，而不编写不必要的代码行，并且仍然保持不变性呢？嗯，Typescript 2 带来了一个很好的特性来处理这个`readonly modifier`。你可以在这里阅读更多关于 T2 的信息。它不同于 const，因为它在编译时而不是运行时进行赋值检查。也就是说，即使您将属性设置为`readonly`，在运行时 in 也可以根据需要被多次写入。但是这种方法允许我们确定属性将只由输入的值写入，并且我们不打算修改它。

```
export enum Cicle {
  // Cicle values
}

@Component({
  // Component definitions
})
export class StartButtonWidget {
  @Input()
  public readonly cicle: Cicle;
  @Output()
  public onClick: EventEmitter<Cicle> = new EventEmitter<Cicle>();

  public get label(): string {
    // Returns a strings from the value of the cicle
  }

  public emit(): void {
    this.onClick.emit(this.cicle);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，为我们的输入原语使用 readonly 属性允许我们确定属性是如何以及何时改变的。但是，我仍然认为这有一个缺点。如果您需要输入一个值，并显示另一个值，您可能会意外地以错误的方式访问该属性。我不是指你，而是指你所在的团队。有了`@input-set/template-get`,当你写的时候，你的意图是清楚的，但是有了这个，你必须确定原语将会以你期望的方式被使用。我想，文档可以做到这一点。

这只是在 Typescript 和 Angular framework 中处理属性的另一种方法，我相信你还有更多方法，我希望在下面的评论中读到它们。

一如既往，感谢阅读，我希望你喜欢。