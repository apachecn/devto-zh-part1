# Android 数据绑定第 2 部分:观察者模式

> 原文：<https://dev.to/brightdevs/android-data-binding-part-2-observer-pattern-to-the-bone-5319>

在[上一部分](https://dev.to%20post_url%202015-07-20-android-data-binding-part-1-why-it-is-important%20)中，我描述了我们在 Android 上开发应用时必须面对的典型问题。我还强调了当正确使用[数据绑定 API](https://developer.android.com/tools/data-binding/guide.html) 时，其中一些问题可能会得到缓解。是时候深入了解这个有前途的 API 如何工作的更多细节了。

## 观察者模式

我们在今天的 API 中发现的许多解决方案的根源是设计模式。在讨论的 Android API 的情况下，它是应用于骨骼的[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)。事实上，这种特殊的模式如此普遍和强大，以至于一些语言和运行时(C#与。NET、Objective-C 以及 iOS 和 Mac 上的 Swift)为它提供了整洁的支持。您可能想知道为什么它在 Android 数据绑定的上下文中很重要？答案很简单却容易被忽略——内存管理。下图描述了 Android 数据绑定上下文中观察者模式元素的依赖关系:
[![Android data binding diagram](img/e182c0322c0349baa2f0f66e0570b706.png)img/android_data_binding_diagram.png) 
在右边，我们有*可观察的*部分，产生关于变化的通知——一个实现`android.databinding.Observable`接口的对象。在观察者模式*中，事件生产者*可以有许多*事件监听器* - `OnPropertyChangedCallback`实现。Android 数据绑定提供了*事件监听器*的实现，负责属性值的更改以及集合元素的更改。

注意，`Observable`对象对具体的`OnPropertyChangedCallback`实现一无所知([依赖倒置原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)处于最佳状态)。然而，在运行时，`Observable`对象将保存对许多`OnPropertyChangedCallback`实例的引用。如果你沿着图往下看，你会发现为了让 Android 数据绑定库提供的`OnPropertyChangedCallback`实现更新视图组件的状态，需要一个对它的引用。这意味着尽管视图模型在编译时对视图组件一无所知，但它们会在运行时引用它们。

## Android 数据绑定库如何辅助内存管理？

如上所述，在一个简单的实现中，我们将拥有一个轻量级的、可测试的视图模型，保留昂贵的视图小部件——从而使它变得很重。在 Android 环境中，这意味着即使一个`Activity`被销毁，我们也可以让其他对象仍然保留它的`Views`，试图更新它们并防止它们被垃圾收集。不太好，是吧？

如果你仔细观察 Android 数据绑定是如何实现的，你会立即发现它的`OnPropertyChangedCallback`只包含对视图的[弱引用](http://developer.android.com/reference/java/lang/ref/WeakReference.html)。
`WeakPropertyListener`、`WeakListListener`、`WeakMapListener`、`ViewDataBinding.WeakListener`确保`Observable`对象没有保留视图。这意味着开发者不需要手动*停止活动`onDestroy`或片段`onDestroyView`方法中的*数据绑定。如果你使用 RxJava，你可能知道这个额外的步骤是乏味的，需要大量的注意力和样板代码。你可以在 GitHub 上找到更多关于这个问题的信息: [1](https://github.com/ReactiveX/RxJava/issues/386) ， [2](https://github.com/ReactiveX/RxAndroid/issues/12) 。

因为 Android 数据绑定库需要额外的步骤来确保视图模型不会导致视图被保留，所以我们可以将视图模型的生命周期从活动或片段的生命周期中分离出来。我们现在有一个简单的方法，即通过单一视图模型进行简单的内存缓存。更重要的是，在寻找内存泄漏原因时，我们至少可以划掉一个地方。

*本文与[我的个人博客](http://miensol.pl/android/2015/07/27/android-data-binding-part-2-observer-pattern-to-the-bone.html)* 交叉发布