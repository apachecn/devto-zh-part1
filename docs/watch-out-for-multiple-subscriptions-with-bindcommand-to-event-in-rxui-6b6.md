# 使用 RxUI 中的 BindCommand to event 注意多个订阅

> 原文：<https://dev.to/jonstodle/watch-out-for-multiple-subscriptions-with-bindcommand-to-event-in-rxui-6b6>

当在 ReactiveUI 中使用方便的绑定方法时，您可能会发现自己使用了非常方便的重载`BindCommand()`，它允许您将命令绑定到事件:

```
this.BindCommand(ViewModel, vm => vm.MyCommand, v => v.TextBox, nameof(TextBox.GotFocus)); 
```

Enter fullscreen mode Exit fullscreen mode

在你的视图中，你甚至可以把它放在一个`WhenActivated()`中:

```
this.WhenActivated((CompositeDisposable d) =>  
{
    this.BindCommand(ViewModel, vm => vm.MyCommand, v => v.TextBox, nameof(TextBox.GotFocus));
}); 
```

Enter fullscreen mode Exit fullscreen mode

你**需要**记住的是把`BindCommand()`和`WhenActivated()`里面给你的一次性用品一起处理掉。

与常规的命令绑定不同，常规的命令绑定只是将命令绑定到控件的命令属性...

```
this.BindCommand(ViewModel, vm => vm.MyCommand, v => v.MyButton); 
```

Enter fullscreen mode Exit fullscreen mode

命令到事件绑定将事件侦听器添加到事件，并在事件触发时执行命令。如果您不处理此绑定，`BindCommand()`将在导航回该视图时添加一个新的事件监听器。这意味着您必须事件侦听器，两者都执行命令，导致命令的两次执行。

这当然会在您每次导航回视图并重新激活它时重复。解决方案是在使用`WhenActivated()`时遵循最佳实践，并处理绑定:

```
this.BindCommand(ViewModel, vm => vm.MyCommand, v => v.MyButton)  
    .DisposeWith(d); 
```

Enter fullscreen mode Exit fullscreen mode

编码快乐！

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/multiple-subscriptions-with-bindcommand-to-event-in-rxui/)T3】*