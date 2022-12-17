# 跨线程调用变得更加容易

> 原文：<https://dev.to/adamkdean/cross-thread-calls-made-easier-59m1>

早在 2010 年，我写了一篇关于[跨线程调用](http://www.adamkdean.co.uk/blog/read/2/cross-thread-calls-made-easy)的博文，展示了如何克服无效的跨线程操作，并从其他线程改变 windows 窗体。很简单，但是有点乱。

现在事情变得容易了，而且已经有一段时间了。不用声明委托并调用它们，现在可以使用泛型委托，省去为每个方法签名声明新委托的麻烦。

例如，以前您必须这样做:

```
private delegate void ObjectDelegate(object obj);

private void UpdateTextBox(object obj)
{
    if (InvokeRequired)
    {
        ObjectDelegate method = new ObjectDelegate(UpdateTextBox);
        Invoke(method, obj);
        return;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以这样做:

```
private void UpdateTextBox(string s)
{
    if (InvokeRequired)
    {
        Action<string> action = new Action<string>(UpdateTextBox);
        Invoke(action, s);
        return;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

另一件很棒的事情是，如果你必须传递多个对象，那么就像在尖括号内添加更多类型一样简单。也可以用`Action<T>`代替`var`来节省空间。比方说，你需要从另一个线程中更新一个标签，下面的方法很好:

```
private void UpdateLabel(Label label, string s)
{
    if (InvokeRequired)
    {
        var action = new Action<Label, string>(UpdateLabel);
        Invoke(action, label, s);
        return;
    }

    label.Text = s;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将为您节省大量时间，本质上与前面的方法相同。

想了解更多关于`Action<T> Delegate`的信息，请看[这篇 MSDN 的文章](http://msdn.microsoft.com/en-us/library/018hxwa8.aspx)。