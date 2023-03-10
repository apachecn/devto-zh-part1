# 片段与活动之间的交流

> 原文：<https://dev.to/onmyway133/communication-between-fragment-and-activity-o60>

原帖[https://github.com/onmyway133/blog/issues/108](https://github.com/onmyway133/blog/issues/108)

交流总是需要的，对吧😉假设我们有一个有几个 T1 的 T0。每个`Fragment`都有一个`startButton`告知入职流程已经结束，只有最后一个`Fragment`显示该按钮。

这里有几种方法可以做到这一点

## 1。事件总线🙄

我发现的几乎所有文章都提出了这个[https://github.com/greenrobot/EventBus](https://github.com/greenrobot/EventBus)，但是我个人不喜欢这个想法，因为组件是松散耦合的，每个组件和广播都可以监听来自单个组件的事件，这使得很难推断项目何时扩展

```
data class OnboardingFinishEvent() 
```

Enter fullscreen mode Exit fullscreen mode

```
class OnboardingActivity: AppCompatActivity() {
    override fun onStart() {
        super.onStart()

        EventBus.getDefault().register(this)
    }

    override fun onStop() {
        EventBus.getDefault().unregister(this)
        super.onStop()
    }

    @Subscribe(threadMode = ThreadMode.MAIN)
    fun onOnboardingFinishEvent(event: OnboardingFinishEvent) {
        // finish
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class OnboardingFragment: Fragment() {
    override fun onViewCreated(view: View?, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        startButton.onClick {
            EventBus.getDefault().post(OnboardingFinishEvent())
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

阅读更多

*   [https://gunhansancar . com/ease-communication-between-activities-fragments-services/](https://gunhansancar.com/ease-communication-between-activities-fragments-services/)

## 2。香精油🙄

这个[https://github.com/square/otto](https://github.com/square/otto)被弃用，取而代之的是 RxJava 和 RxAndroid

## 3\. RxJava 🙄

我们可以使用简单的`PublishSubject`来创建我们自己的[rx bus](https://android.jlelse.eu/super-simple-event-bus-with-rxjava-and-kotlin-f1f969b21003)T3】

```
import io.reactivex.Observable
import io.reactivex.subjects.PublishSubject

// Use object so we have a singleton instance
object RxBus {

    private val publisher = PublishSubject.create<Any>()

    fun publish(event: Any) {
        publisher.onNext(event)
    }

    // Listen should return an Observable and not the publisher
    // Using ofType we filter only events that match that class type
    fun <T> listen(eventType: Class<T>): Observable<T> = publisher.ofType(eventType)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// OnboardingFragment.kt
startButton.onClick {
    RxBus.publish(OnboardingFinishEvent())
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// OnboardingActivity.kt
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    RxBus.listen(OnboardingFinishEvent::class.java).subscribe({
        // finish
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

## 4。连接

这里建议[与其他碎片](https://developer.android.com/training/basics/fragments/communicating.html#DefineInterface)进行通信。基本上你定义了一个接口`OnboardingFragmentDelegate`,任何符合这个接口的人都可以通过事件的`Fragment`得到通知。这类似于 iOS 中的`Delegate`模式😉

```
interface OnboardingFragmentDelegate {
    fun onboardingFragmentDidClickStartButton(fragment: OnboardingFragment)
}

class OnboardingFragment: Fragment() {
    var delegate: OnboardingFragmentDelegate? = null

    override fun onAttach(context: Context?) {
        super.onAttach(context)

        if (context is OnboardingFragmentDelegate) {
            delegate = context
        }
    }

    override fun onViewCreated(view: View?, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        startButton.onClick {
            delegate?.onboardingFragmentDidClickStartButton(this)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class OnboardingActivity: AppCompatActivity(), OnboardingFragmentDelegate {
    override fun onboardingFragmentDidClickStartButton(fragment: OnboardingFragment) {
        onboardingService.hasShown = true
        startActivity<LoginActivity>()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 5。视图模型

我们可以从片段之间的[共享数据](https://developer.android.com/topic/libraries/architecture/viewmodel.html)来学习片段和活动之间的通信，通过使用一个活动范围内的共享`ViewModel`。这有点矫枉过正

```
class OnboardingSharedViewModel: ViewModel() {
    val finish = MutableLiveData<Unit>()
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class OnboardingActivity: AppCompatActivity(), OnboardingFragmentDelegate {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val viewModel = ViewModelProviders.of(this).get(OnboardingSharedViewModel::class.java)

        viewModel.finish.observe(this, Observer {
            startActivity<LoginActivity>()
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们需要调用`ViewModelProviders.of(activity)`来获得与`activity`T3 相同的`ViewModel`

```
class OnboardingFragment: Fragment() {
    override fun onViewCreated(view: View?, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        val viewModel = ViewModelProviders.of(activity).get(OnboardingSharedViewModel::class.java)
        startButton.onClick({
            viewModel.finish.value = Unit
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 7。希腊字母的第 11 个

在`Fragment`中创建一个 lambda，然后将其设置在`onAttachFragment`上。现在还不行，因为`onAttachFragment`中没有`OnboardingFragment`😢

```
class OnboardingFragment: Fragment() {
    var didClickStartButton: (() -> Unit)? = null

    override fun onViewCreated(view: View?, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        startButton.onClick {
            didClickStartButton?.invoke()
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class OnboardingActivity: AppCompatActivity() {
    override fun onAttachFragment(fragment: Fragment?) {
        super.onAttachFragment(fragment)

        (fragment as? OnboardingFragment).let {
            it?.didClickStartButton = {
                // finish
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 8。捆绑中的侦听器🙄

阅读更多

*   [https://medium . com/Groupon-eng/from-fragments-to-activity-the-lambda-way-32c 768 c 72 aa 9](https://medium.com/groupon-eng/from-fragments-to-activity-the-lambda-way-32c768c72aa9)