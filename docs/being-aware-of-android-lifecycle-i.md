# 了解 Android 生命周期-I

> 原文：<https://dev.to/subbramanil/being-aware-of-android-lifecycle-i>

对于任何一个安卓开发者职位来说，面试中的第一个问题都是关于 T2 的生命周期。我自己也向几个受访者问过同样的问题，并且对生命周期的[定义了如指掌。](https://developer.android.com/guide/components/activities/activity-lifecycle.html)

很长一段时间，我认为我完全理解了生命周期(*直到上周*)。我们在一个大量使用*片段*而很少*活动*的应用中工作。上周，我们注意到一些奇怪的问题开始出现。花了一些时间后，我们发现编写程序的开发人员对生命周期做了一些假设，并且没有处理好事件。

作为一名开发人员，我的第一反应是查看 Android 文档，阅读一些博客来更新我的理解。然而，我意识到，在未来的另一个时间，我将不得不做同样的事情，如果我没有通过动手练习弄脏我的手，并真正理解 android 的生命周期。

我计划在周末花些时间研究一些关于 Android 活动和片段的常见场景，并记录我的理解。

**我的基本想法**是尝试编写一个示例应用程序来理解以下案例。

*   单一活动
*   具有一个片段的单个活动(静态附加到活动布局/使用“ **SupportFragmentManager** ”动态附加)
*   具有多个片段的单个活动
*   两项活动
*   两个活动，每个活动有一个片段

> **注意:**这将是一个了解 android 生命周期的多部分系列。我将创建一个公共存储库来共享代码。欢迎任何建议或反馈。
> 
> 我给自己设置了一些限制，让自己专注于理解生命周期，而不是偏离到其他细节。
> 
> 1.  在输入代码上花费最少的精力。尽可能多地使用模板。(Android Studio 提供了一些不错的样本)。
> 2.  保持用户界面最小，足以显示差异。不要花力气去设计或使它在美学上令人愉悦。专注于编写足够多的调试语句来理解正在发生的事情。
> 3.  试着从每个例子中理解尽可能多的东西。不要在没有理解的情况下从一个跳到另一个。这包括从调试语句中做笔记，阅读 android 文档，当然还有阅读网上智者的博客。**额外的技术限制:**即使世界已经转移到 **Android O** 上，API **23** (又名 *Lollipop* )的最小支持，我们的应用程序运行在基于 Kitkat 的定制 Android 平台上。
> 4.  所以我将 Android SDK API 的最低级别保持在 **19** (又名 *Kitkat* )。
> 5.  我将坚持使用 **SupportFragmentManager** 而不是 **FragmentManager** 。也许我会在后面再发表一篇文章来比较这两者。

### 让我们把手弄脏吧

我创建了一个新的 android 应用项目，' **LifeCyclesApp** '(不能用其他方式命名)。

#### 应用类

```
public class LifeCyclesApp extends Application {

    private static final String TAG = LifeCyclesApp.class.getSimpleName();

    //region Life-cycle apps
    @Override
    public void onCreate() {
        Log.d(TAG, "onCreate()");
        super.onCreate();
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        Log.d(TAG, "onConfigurationChanged()");
        super.onConfigurationChanged(newConfig);
    }

    @Override
    public void onTerminate() {
        Log.d(TAG, "onTerminate()");
        super.onTerminate();
    }
    //endregion
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 主要活动类

```
 public class MainActivity extends AppCompatActivity {

    private static final String TAG = MainActivity.class.getSimpleName();

    //region Life-cycle events
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        Log.d(TAG, "onCreate()");
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    protected void onStart() {
        Log.d(TAG, "onStart() ");
        super.onStart();
    }

    @Override
    protected void onResume() {
        Log.d(TAG, "onResume() ");
        super.onResume();
    }

    @Override
    protected void onPause() {
        Log.d(TAG, "onPause() ");
        super.onPause();
    }

    @Override
    protected void onStop() {
        Log.d(TAG, "onStop() ");
        super.onStop();
    }

    @Override
    protected void onDestroy() {
        Log.d(TAG, "onDestroy() ");
        super.onDestroy();
    }
    //endregion
} 
```

Enter fullscreen mode Exit fullscreen mode

用户界面非常简单。它什么都没有。(lol)

[![App Screenshot](img/90aa3399f25be7a38e61192ba201aa06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b6vq7snf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/my-blogresources/screenshots/r2d22.3.11subbu06042017003228.png)

#### App 运行流程:

1.  启动应用程序
2.  显示主要活动。
3.  从命令行启动“设置”应用程序。
4.  按“返回”退出设置，返回到生命周期应用程序
5.  按 back 退出应用程序。

```
 LifeCyclesApp  D  onCreate()
           MainActivity  D  onCreate()
                         D  onStart()
                         D  onResume()
...
                         D  onPause()
    Process com.android.settings created for activity com.android.settings/.Settings
           MainActivity  D  onStop()
...           
                         D  onStart()
                         D  onResume()
...                         
                         D  onPause()
                         D  onStop()
                         D  onDestroy() 
```

Enter fullscreen mode Exit fullscreen mode

是的，通过查看日志，这很有意义。但是等等， *LifeCyclesApp* 类的 **onTerminate()** 方法怎么了？它没有出现在日志里。我确实退出了应用程序。可能是我再等一会儿，安卓过一会儿就把 app 干掉了。

等待...

没有。

**onTerminate()** 尚未调用。

再多走一步确保 app 被停掉怎么样？我进入*设置- >应用*，试图‘强制停止’应用。

不要。。从未调用过 **onTerminate()** 。

我查看了 **onTerminate()** 方法的 *javadoc* 文档，发现确实如此。

```
public class Application extends ContextWrapper ... {
    ...

    /**
     * This method is for use in emulated process environments.  It will
     * never be called on a production Android device, where processes are
     * removed by simply killing them; no user code (including this callback)
     * is executed when doing so.
     */
    @CallSuper
    public void onTerminate() {
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我也核实了上面的说法。

```
1|root@xxx:/ # ps | grep life 
u0_a46    9368  2243  878808 26364 ffffffff 401de834 S com.chaturaloka.lifecycles
root@xxx:/ # kill 9368 
```

Enter fullscreen mode Exit fullscreen mode

```
 LifeCyclesApp  D  onCreate()
           MainActivity  D  onCreate()
                         D  onStart()
                         D  onResume()

        Process com.chaturaloka.lifecycles (PID: 9368) ended 
```

Enter fullscreen mode Exit fullscreen mode

甚至杀死 app 进程都没有调用 **onTerminate()** 方法。然而有趣的是，我们看到 **onStop()** 和**on destroy()**main activity 的方法在终止 app 进程时并没有被调用。然而，如果你稍微注意一下 android [活动生命周期](https://developer.android.com/guide/componenimg/activity_lifecycle.png)图，这就是活动的预期行为。

> 您可能会想，我本可以在一开始就检查文档。耶！我同意。然而，我很高兴我从理论和实践上了解了生命周期。

现在进入**单人活动一个片段的下一个冒险！！**