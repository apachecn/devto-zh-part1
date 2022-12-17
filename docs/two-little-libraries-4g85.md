# 两个小图书馆

> 原文：<https://dev.to/tom/two-little-libraries-4g85>

嘿。

我想告诉你我构建的两个小 JavaScript 库。

`if-expression`和`try-expression`做类似的事情，在 JavaScript 的`if`和`try`语句周围添加了一些功能美。

它们帮助你坚持使用`const`而不是`let`，并简化重构。

让我们来看看。

* * *

一分钟后，我将分别详细介绍这些库，但首先这里有一个完整的前/后示例。首先，您可能不用这些工具编写一些代码...

```
function doMagicalThings({ hocus, pocus } = {}) {
  let spell;
  try {
    spell = invokeTheDarkOnes(hocus);
  } catch (portalToTheUnderworldException) {
    spell = abracadabraAlacazam(pocus);
  }

  try {
    return castToString(spell); // See what I did there?
  } catch (unintendedConsequences) {
    return getErrorMessage(unintendedConsequences);
  } finally {
    cleanupOpenPortals();
  }
}

function getErrorMessage(error) {
  let errorMessage;
  if (error.code === 0) {
    errorMessage = "The giant spiders escaped.";
  } else if (error.code === 10) {
    errorMessage = "I dunno but I think kittens were involved.";
  } else {
    errorMessage = "Yikes. Run away?"
  }
  return errorMessage;
} 
```

Enter fullscreen mode Exit fullscreen mode

...同样的还有`if-expression`和`try-expression` :

```
import iff from "if-expression";
import tryy from "try-expression";

function doMagicalThings({ hocus, pocus } = {}) {
  const spell = tryy(
    () => invokeTheDarkOnes(hocus),
    (portalToTheUnderworldException) =>
      abracadabraAlacazam(pocus)
  );

  return tryy(
    () => castToString(spell),
    (unintendedConsequences) =>
      getErrorMessage(unintendedConsequences),
    () => cleanupOpenPortals()
  )
}

function getErrorMessage(error) {
  return iff(
    error.code === 0,
    () => "The giant spiders escaped.",

    error.code === 10,
    () => "I dunno but I think kittens were involved.",

    () => "Yikes. Run away?"
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

主要区别:

*   `iff`和`tryy` *总是*返回值
*   子句是(箭头)函数
*   不需要创建可变绑定(`let`，`var`)，因为您可以将值返回到`const`中

请继续阅读深入了解。

* * *

## `if-expression`

```
$ yarn add if-expression 
```

Enter fullscreen mode Exit fullscreen mode

`if-expression` — `iff`简称——使用起来非常简单。你可以在 GitHub 上找到代码[，这里有一个快速的概述。](https://github.com/tgvashworth/if-expression) 

```
const largerNum = iff(
  a > b,
  () => a,
  () => b
); 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数是*条件*。如果它评估为[真值](https://developer.mozilla.org/en/docs/Glossary/Truthy)，那么调用第二个参数——第一个*子句*。为了可读性，我在上面使用了箭头函数，但是你可以只传递一个函数:

```
return iff(
  featureFlag("fancy_new_thing"),
  useFancyNewThing,
  useUglyOldThing
); 
```

Enter fullscreen mode Exit fullscreen mode

如果条件为 false-y，则运行最后一个参数，即 *else* 子句。

它是可变的，所以它支持可变数量的参数，允许您提供多个*条件*和*子句*。条件和从句像 if-else 一样成对出现:

```
return iff(
  x < 0,
  () => "negative",

  x > 0,
  () => "positive",

  () => "zero"
); 
```

Enter fullscreen mode Exit fullscreen mode

最后一个参数总是 else *子句*。

在任何子句位置，如果需要，您可以只提供一个值:

```
return iff(
  x < 0,
  "negative",

  x > 0,
  "positive",

  "zero"
); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**Thunk up**:关于懒惰的一个注解，以及作为条件的功能...

在常规的 JavaScript 执行中，if-else 分支的条件是*延迟*评估的，也就是说，它们只在需要检查真值时才运行。

然而，因为`if-expression`是一个普通的 ol' JavaScript 函数，所以条件被*贪婪地*评估:在 `if-expression`有机会决定第一个条件是否真实之前，所有的条件都将被*评估。*

这在实践中意味着什么？

对于大多数情况，这并不重要:您不应该将副作用代码放在`if`子句中，性能影响可以忽略不计。

但是，如果懒惰对您很重要，那么将条件作为函数传递:

```
return iff(
  () => x < 0,
  () => "negative",
  () => "not negative"
); 
```

Enter fullscreen mode Exit fullscreen mode

*然而*，这意味着函数不能在没有显式转换为布尔值的情况下用作条件，这不同于 JavaScript 的内置`if`。在下面的例子中，`a.someMethod` *将被称为* :

```
return if(
  a.someMethod,
  doAThing,
  doAnotherThing
); 
```

Enter fullscreen mode Exit fullscreen mode

为了避免这种情况，您有两个选择。要么显式转换为布尔值...

```
Boolean(a.someMethod) 
```

Enter fullscreen mode Exit fullscreen mode

...或者从包装函数中返回方法:

```
() => a.someMethod 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## `try-expression`

```
$ yarn add try-expression 
```

Enter fullscreen mode Exit fullscreen mode

`try-expression`——简称为`tryy`——与`if-expression`非常相似，但是更容易创建`try-catch-finally`表达式。同样，GitHub 上有代码[。](https://github.com/tgvashworth/try-expression)

运行一些代码并捕捉任何错误，就像使用`try-catch` :
一样

```
return tryy(
  () => doRiskyThing(),
  error => {
    logError(error);
    return 'Sorry!';
  }
); 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数总是一个函数——一个`try`子句。如果抛出，则使用第二个参数——`catch`子句。

在上面的例子中，如果`doRiskyThing`抛出，这段代码将返回`'Sorry!'`。

如您所见，`catch`子句被传递了在`try`子句中抛出的错误。

像`if-expression`一样，如果有错误，可以只提供一个值:

```
return tryy(
  () => throwSomething(),
  { squibbles: 4 }
); 
```

Enter fullscreen mode Exit fullscreen mode

`tryy`还支持一个用于清理的`finally`子句，如`try-catch-finally` :

```
const result = tryy(
  ()  => ['Success', readFile()],
  err => ['Failure', err],
  ()  => closeFile()
); 
```

Enter fullscreen mode Exit fullscreen mode

注意，为了避免[混淆 JavaScript 行为](http://eslint.org/docs/rules/no-unsafe-finally)，从`finally`函数返回的任何内容都将被丢弃。

* * *

这里有一些你可以用这些库做的好事情...

这个功能已经完成了一半，但是意图很清楚:我们将从菜单中进行选择。为了说明这一点，我只使用了*和*一个 else 子句，该子句将*总是*运行。

```
function chooseSomeLunch(person, menu) {
  return if(
    () => "not sure yet"
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们扩展这段代码时，变化很小:

```
function chooseSomeLunch(person, menu) {
  return if(
    onADiet(person),
    () => menu.salad,

    () => "not sure yet"
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在下一个例子中，第一个子句变得有点长:

```
function shouldIEatThisCake() {
  return iff(
    nobodyIsWatching,
    () => {
      const isItLunchTime = consultTheAstrolabe();
      const caloriesBurned = activities.map(getCalories).reduce(add);
      // ... and so on and so forth ...
      return theFinalDecision;
    },

    () => false
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

它很容易被重构得更短、更易读:

```
function shouldIEatThisCake() {
  return iff(
    nobodyIsWatching,
    () => thinkRealHard(),

    () => false
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

甚至:

```
function shouldIEatThisCake() {
  return iff(
    nobodyIsWatching,
    thinkRealHard,
    false
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以轻松地构建一个“getter”函数，该函数尝试访问对象，但是如果该值不存在，它将返回一个默认值:

```
function getIn(getter, defaultValue, data) {
  return tryy(
    () => getter(data),
    () => defaultValue
  );
}

const data = {
  a: {
    b: {
      c: "c"
    }
  }
};

const c = getIn(data => data.a.b.c, "default c", data);
const r = getIn(data => data.p.q.r, "default r", data); 
```

Enter fullscreen mode Exit fullscreen mode