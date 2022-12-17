# 用 Angular 重构 RxJS 代码

> 原文：<https://dev.to/vknabel/refactoring-rxjs-code-in-angular-3pc4>

你使用遗留的 RxJS 代码吗？你曾经在你的应用中回顾过你最初的几个观察点吗？你学习阶段的一个 app 需要修复 bug 吗？你还在学习写反应式代码的最佳实践吗？这本指南是给你的。即使你和 Angular 没有任何关系，你也可能会觉得这个很有趣。我将向您展示如何改进您的反应流的方法，以便通过许多孤立但微小的步骤来理解它们的功能。其中一些可能会提供外部依赖，但我们将始终展示如何手动完成。

在每个步骤中，我们隔离/消除副作用，更明确地了解生命周期，并了解如何防止意外行为。如果您使用本指南来重构某些特定的代码，您应该按顺序执行所有的单个步骤，并在每次更改后重新运行所有的测试。如果你找不到显示的模式，你可以采取下一步。

> 单元测试:如果你没有任何测试，提前创建它们。每一步都是一个小的重构:你可能会打破一些东西。对于那些坐在不可测试的代码前的人，我理解你们的处境。所有“危险”的步骤都有标记。您需要进行更多的手动测试。至少在事后引入单元测试。依赖注入是你的朋友。

在开始的时候，我们从简单开始，把副作用从订阅中分离出来。我们没有直接传递回调，而是将它们包装到`do`操作符中。每当你读到一个`do`，你就知道:那是副作用。如果你想要一个稍微更有表现力的变体，试试`@molecule/do-next`、`@molecule/do-error`和`@molecule/do-complete`。

```
// previous code
myObservable$.subscribe(
  next => handleNext(next),
  error => handleError(error),
  () => handleCompletion(),
);

// refactored code
myObservable$.do(
  next => handleNext(next),
  error => handleError(error),
  () => handleCompletion(),
)
 .subscribe();

/* or with @molecule/do-next,
 * @molecule/do-error,
 * @molecule/do-complete
 */
myObservable$.do(
  next => handleNext(next),
  error => handleError(error),
  () => handleCompletion(),
)
 .subscribe(); 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那很简单。现在让我们简化所有的操作符。使用闭包块和显式 return 语句(`() => {}`)是一个好迹象，表明您试图在一个操作符中做多件事情。特别是如果我们从我们的操作符中提取所有副作用，我们知道:*所有*副作用都存在于`do*`和`finally`中！每一个不是你的`return`-语句和变量声明的语句，用于你的`return`都是副作用。如果操作者是`catch`，将逻辑隔离的副作用(见上文)移至`do(undefined, yourErrorHandler)` / `doError(yourErrorHandler)`。否则将它们提取到`do` / `doNext`。

```
// previous code
myObservable$.catch(error => {
    console.log(’Could not resolve myObservable$’, error);
    return Observable.empty();
})
// refactored code
myObservable$
     // or use .do(undefined, errorHandler)
  .doError(error => console.log(
        ’Could not resolve myObservable$’,
        error
    ))
  .catch(() => Observable.empty()); 
```

Enter fullscreen mode Exit fullscreen mode

为了进一步简化您的操作处理程序，您可以将整个块提取到您的类的一个新的`private`方法中。如果它甚至不依赖于`this`你实际上可以做到`static`。下面这个案例一开始可能看起来有点尴尬，但是它会让一切变得简单而愚蠢。你会看到，一个人不知道所有这些细节也能完全理解`previewOfFavorites$`。如果底层 API 发生变化，我们的公共方法和所有业务逻辑都不会在意。

```
// previous code
public get previewOfFavorites(): Observable<Favorite[]> {
    const previewSize$ = this.user.settings$.map(settings => settings.preview.size);
    const favorites$ = this.user.favorites$();
    return Observable.combineLatest(
        previewSize$,
        previewOfFavorites$,
        (size, favorites) => favorites.slice(0, 3)
    );
}
// refactored code
public get previewOfFavorites$(): Observable<Favorite[]> {
    return Observable.combineLatest(
        this.previewSize$,
        this.previewOfFavorites$,
        (size, favorites) => favorites.slice(0, 3)
    );
}

private get previewSize$(): Observable<number> {
    return this.user.settings$.map(settings => settings.preview.size);
}

private get favorites$(): Observable<Favorite[]> {
    return this.user.favorites$();
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们应该主要使用单一的`return`-语句作为处理程序和转换。在这种情况下，我们只使用闭包的简写符号。一个例外是对象文字，它需要用括号(`() => ({})`)括起来，或者如果您喜欢的话，保留显式返回语句。

```
// previous code
myObservable$.map(value => {
    return [value];
})
// refactored code
myObservable$.map(value => [value]); 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

这篇博文一直没有写完。一年多没碰了，剩下的音符我都不懂了。他们在这里:

*   通过实例变量所代表的可观测量替换实例变量

*   移除临时可观察值并调整`mergeMap` s

*   如果提取的主体以客体为参数，调整功能参数

*   用名为`*Action` ( `Observable.empty().finally`)的临时观察值触发动作

*   在当前操作符之前，否则在后面

*   保留阻止执行的功能

*   所有的主题都应该是私有的:添加访问器`processDidChange`、`processDidFail`、`processDidComplete`、`processObserver`(如果需要的话)