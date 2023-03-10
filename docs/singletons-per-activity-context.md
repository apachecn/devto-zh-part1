# 每个活动上下文的单例

> 原文：<https://dev.to/subbramanil/singletons-per-activity-context>

# 单胎

单体设计模式是迄今为止最常见的设计模式，也是任何人在考虑设计模式时第一个遇到的模式。它是如此常用，以至于有人在睡觉的时候也能写出这个模式。

## Android Studio 默认模板

```
class Test {
    private static final Test ourInstance = new Test();

    static Test getInstance() {
        return ourInstance;
    }

    private Test() {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是我们团队通常使用的模板。

## 我们的模板

```
 class Test {
    private static final Test ourInstance;

    static Test getInstance() {
        if(ourInstance == null){
            ourInstance = new Test();
        }
        return ourInstance;
    }

    private Test() {
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我曾面临这样一种情况，我被要求为每个活动创建一个单例，或者创建一个包含活动上下文实例的单例类。通常当你试图在任何 Java 类中声明像' *Activity* 或' *Context* '这样的对象作为静态字段时，Android Studio 会通过静态对象警告内存泄漏。

```
Do not place Android context classes in static fields; this is a memory leak. 
```

Enter fullscreen mode Exit fullscreen mode

为了避免内存泄漏，

1.  不存储上下文(这是显而易见的！不是吗)。

2.  使用 *WeakReference* 存储上下文。

```
private final WeakReference<Activity> mActivityContextRef;

public static void newInstance(Activity activity) {
    if (ourInstance == null) {
        ourInstance = new Test(activity);
    }
}

private Test(Activity activity) {
    mActivityContextRef = new WeakReference<>(activity);
} 
```

Enter fullscreen mode Exit fullscreen mode

> **一些关于弱引用的最佳文章:**
> 
> 1.  [罗曼·盖伊的文章](http://www.curious-creature.com/2008/12/18/avoid-memory-leaks-on-android/)
> 2.  [来自谷歌开发者专家的文章-1](https://medium.com/google-developer-experts/finally-understanding-how-references-work-in-android-and-java-26a0d9c92f83)
> 3.  [来自谷歌开发者专家的文章-2](https://medium.com/google-developer-experts/weakreference-in-android-dd1e66b9be9d)

回到我之前描述的场景，我必须为每个活动创建一个**单件。结合我们的 singleton 模板和 *WeakReference* 的概念，我想到了这个。** 

```
public static void newInstance(Activity activity) {
        if (ourInstance == null) {
            ourInstance = new Test(activity);
        } else {
            if (activity.equals(ourInstance.mActivityContextRef.get())) {
                LogUtils.debug(TAG, "newInstance: Same Activity; No need to create new instance");
            } else {
                LogUtils.debug(TAG, "newInstance: New Activity; Create new instance");
                ourInstance = null;
                ourInstance = new Test(activity);
            }
        }
    }

    private Test(Activity activity) {
        mActivityContextRef = new WeakReference<>(activity);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案帮助我们解决了面临的问题。

我并不认为这是完美的解决方案，事实上这是一个谨慎的忠告当我和我的一个高级同事谈论上述 *Singleton per Activity* 时，他问我**为什么有人会在 Singleton POJO 类**中需要 *Activity* 上下文。这让我开始思考，我意识到这个类处理了太多的职责，并且使用 MVP 设计模式进行了重构。我会在另一篇文章中写。