# ActivityTask，Android 上异步/等待的助手

> 原文：<https://dev.to/garuma/activitytask-an-helper-for-async-await-on-android-1ifi>

在上周的 I/O 大会上，我们碰巧参加了关于 Android 生命周期的[架构组件讲座(我推荐你观看)。虽然这里提出的解决方案确实很有趣，而且在某些情况下，映射到我们已经有的模式。NET，它肯定引起了我们的共鸣，这些 Android 生命周期的吹毛求疵如何使一个特定的 C#特性使用起来更麻烦:async/await。](https://events.google.com/io/schedule/?section=may-18&sid=006961f0-030f-4dca-8277-a479083c208d)

对于 async/await，与 Android 开发人员相关的两个主要问题是:

*   由于 Android 资源系统的工作原理，配置的改变(如屏幕旋转)将默认重新创建`Activity`实例
*   因为`await`不知道`Activity`的生命周期，所以它可能会在不期望的状态下执行延续，从而导致`IllegateStateException`

请注意，我们已经以 [ActivityController](https://forums.xamarin.com/discussion/84221/announcing-android-activity-controller-preview) 的形式引入了部分解决这些疑虑的东西，并为基于`StartActivityForResult`的工作流提供了方便的异步包装方法。

在这种情况下，基于我以前的一些有趣的事情，我发现有一种潜在的方法可以利用 C# 7 新的可定制的异步状态机驱动程序来减轻这两个问题，而不需要对代码做太多的修改。

输入 [ActivityTask](https://github.com/garuma/LibActivityTask) (也在 [NuGet](https://www.nuget.org/packages/Neteril.ActivityTask) 上)。

这个小库有两个主要的原语:

*   `ActivityScope`允许跟踪某个活动子类的最新实例，这样潜在的重新创建对您来说就是透明的
*   `ActivityTask`在异步方法中充当标准的`Task`返回值，但是定制状态机驱动程序(与`ActivityScope`合作)以使继续调度知道活动的生命周期。

在幕后，`ActivityScope`只需在`Application`级别注册一个监听器来监听全局活动生命周期事件。当它检测到它所跟踪的活动即将终止时，它会对其进行标记，这样当它被重新跟踪时，它就可以重新与它关联。因为它实现了对`Activity`的隐式转换操作符，所以您可以在任何需要活动实例的地方传递作用域，以确保您总是使用有效值。

至于`ActivityTask`的实现很无聊(它很大程度上取决于`TaskCompletionSource`)，有趣的部分是驱动异步状态机的`ActivityScopeMethodBuilder`。出于所有的意图和目的，它将表现得像`Task`的默认驱动程序。然而，由于额外的`ActivityScope`方法参数，它将额外确保任何延续仅在作用域所跟踪的活动当时处于可用状态时才被执行。如果没有，它将简单地保持继续排队，直到被跟踪的活动恢复。

为了了解所有这些是如何组合在一起的，下面是 GitHub 存储库中测试应用程序活动的代码:

```
[Activity(Label = "ActivityTaskTest", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity : Activity
{
    static bool launched = false;

    protected override async void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Set our view from the "main" layout resource
        SetContentView(Resource.Layout.Main);

        if (!launched)
        {
            launched = true;
            using (var scope = ActivityScope.Of(this))
                await DoAsyncStuff(scope);
        }
    }

    TextView MyLabel(Activity activity) => activity.FindViewById<TextView>(Resource.Id.myLabel);

    async ActivityTask DoAsyncStuff(ActivityScope scope)
    {
        await Task.Delay(3000); // Medium network call
        MyLabel(scope).Text = "Step 1";
        await Task.Delay(5000); // Big network call
        MyLabel(scope).Text = "Step 2";
    }
} 
```

这个例子模拟了第一次创建活动时启动一系列异步操作。不过，在此之前，它会创建一个`ActivityScope`来跟踪当前活动的生命周期并将其传递下去。在每个异步子步骤之间，activity 实例用于获取屏幕上的标签并更新其文本。

这个想法是在这些`Task.Delay`通话中触发一个破坏性事件。您可以测试的一些方法是旋转您的设备(从而终止并重新创建活动)或按下主屏幕按钮来暂停活动并在延迟到期后重新打开它。

例如，如果在显示“步骤 1”后旋转屏幕，您应该会看到标签的原始文本短暂地重新出现(因为布局是从零开始展开的)，并且在看到“步骤 2”后不久，将设置意味着异步方法使用正确的新活动实例来定位标签。

如果显示“步骤 1”后暂停活动，在第二个延迟到期后恢复活动将导致“步骤 2”立即显示，因为回调在恢复过程中执行，而不是在活动在后台运行。