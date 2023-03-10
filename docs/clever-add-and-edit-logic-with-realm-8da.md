# 用 Realm 聪明地添加和编辑逻辑

> 原文：<https://dev.to/jonstodle/clever-add-and-edit-logic-with-realm-8da>

大约半年前，我爱上了[领域](https://realm.io/)。这是基于 SQLite 的数据库的替代方案。使用方便，设置快捷，快速，跨平台。与 SQLite 不同，它不是一个关系数据库，而是一个对象数据库。你将普通的旧对象“直接”存储在数据库中，而不是通过一个 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) 层或某种序列化。

因为 Realm 的工作方式不同于 Core Data、Couchbase 或类似的东西，所以你必须以不同的方式思考如何做某些事情。在观看 Xamarin Show 上的 Realm 演示时，我发现了一个在数据库中编辑对象的好技巧。

# 领域中编辑对象的问题

通常，您将从数据库加载记录，更改它，然后将更新的记录写入数据库。如果要放弃更改，可以跳过在数据库中存储更新记录的步骤。

这在境界上有点难度。当您从领域数据库加载记录时，您实际上是在加载一个对象。如果您想对该对象进行更改，您必须将这些更改放入一个事务中。通常你会这样更新:

```
myRealm.Write(() =>  
{
    // Make changes to my object here
}); 
```

Enter fullscreen mode Exit fullscreen mode

这在进行数据绑定时变得非常不切实际。您必须为 Realm 对象支持的属性定制 setter 逻辑，或者对该对象进行某种浅层复制，以获得可以直接编辑的版本。

# 编辑对象的境界方式

还有另一种改变对象的方法:你可以调用`myRealm.BeginWrite()`，它将返回一个`Transaction`。现在，您可以对领域数据库中的对象进行更改。当你完成后，你可以通过调用`myTransaction.Commit()`来提交你所做的更改。如果您想放弃(或回滚)更改，您可以调用`myTransaction.Dispose()`。

巧妙之处在于:如果在导航到页面时创建一个事务，可以直接在对象上进行绑定，通过调用`Commit()`保存更改，或者通过调用`Dispose()`放弃。

这里有一个简单的例子。给定以下类:

```
public class Data : RealmObject  
{
    [PrimaryKey] public string Id { get; set; } = Guid.NewGuid().ToString();
    public string Text { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Data`类用于存储一个简单的`string`。`Id`财产将拥有一个唯一的标识符，我们可以很容易地找到记录。Realm 将在编译期间使类实现`INotifyPropertyChanged`，使`Text`属性可绑定。

然后我们有一个简单的 XAML 页面:

```
<?xml version="1.0" encoding="utf-8" ?>  
<ContentPage   
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"  
    x:Class="App1.EditPage">

    <StackLayout>
        <Entry Text="{Binding Model.Text}"/>

        <Button Text="Save" Clicked="Button_Clicked"/>
    </StackLayout>

</ContentPage> 
```

Enter fullscreen mode Exit fullscreen mode

它包含一个`Entry`字段和一个`Button`来保存输入的文本。在页面的代码隐藏中，我们有以下代码:

```
public partial class EditPage : ContentPage  
{
    public EditPage (string id = "") // Make it possible to pass in the ID of an existing object
    {
        InitializeComponent ();
        BindingContext = this; // Make binding to itself possible

        _realm = Realm.GetInstance(); // Get an instance of the Realm database
        _transaction = _realm.BeginWrite(); // Create a transaction

        Model = _realm.Find<Data>(id) ?? _realm.Add(new Data()); // "Find" returns null if it can't find the object with the given ID

        this.Disappearing += EditPage_Disappearing;
    }

    public Data Model { get; set; } // The property to bind to

    private async void Button_Clicked(object sender, EventArgs e)
    {
        _transaction.Commit(); // Commit/save the changes when button is clicked
        await Navigation.PopAsync();
    }

    private void EditPage_Disappearing(object sender, EventArgs e)
    {
        _transaction.Dispose(); // Close the transaction when navigating away
    }

    private Realm _realm;
    private Transaction _transaction;
} 
```

Enter fullscreen mode Exit fullscreen mode

从头开始:您可以将现有记录的 ID 传递给构造函数。这将加载具有给定 ID 的记录。如果给出了一个无效的 id(例如一个空字符串)，`_realm.Find()`将返回 null。在这种情况下，我们将创建一个新的数据对象，并将其添加到 Realm。`_realm.Add()`返回新添加的对象。

我们还跟踪由`_realm.BeginWrite()`返回的`Transaction`。我们稍后在两个地方使用它:

第一个地方是在`Button_Clicked`中，我们处理点击保存按钮。我们调用`_transaction.Commit()`，它将保存我们对领域数据库所做的所有更改。这包括添加或删除的对象，以及对现有对象的更改。

第二个地方是在我们调用`_transaction.Dispose()`的`EditPage_Disappearing`方法中。这确保了我们完成交易。如果您不关闭事务，当您稍后尝试创建新事务时，Realm 将抛出异常。

如果您调用了`_transaction.Dispose()`而没有先调用`_transaction.Commit()`，您所做的所有更改都将被丢弃。

使用这种技术，您可以利用数据绑定并直接在 objec 上进行更改，同时仍然保持放弃所有更改的能力。

编码快乐！

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/clever-add-and-edit-logic-with-realm/)T3】*