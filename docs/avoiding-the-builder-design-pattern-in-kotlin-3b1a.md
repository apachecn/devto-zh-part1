# 在 Kotlin 中避免构建器设计模式

> 原文：<https://dev.to/chrisvasqm/avoiding-the-builder-design-pattern-in-kotlin-3b1a>

构建器设计模式为我们提供了一系列的方法，这些方法可以帮助我们让你的类的*消费者*更好地理解正在发生的事情。

但是它被一些开发者认为是一种反模式。

# 为什么要使用 Builder 设计模式？

通过用越来越多的参数重载它们，这是使用多个`constructors`的替代方法。

## 有哪些优点？

*   有一些工具允许您获得参数名称的提示，如果它们的名称能提供信息，这可能会有所帮助，但情况并非总是如此。比如:在 GitHub 或其他 IDE/编辑器上阅读代码。
*   在初始化对象时，您不需要拥有将它传递给对象所需的所有数据。

## 有哪些弊端？

*   您可能最终拥有要求其他方法以特定顺序运行的方法；否则，*消费者*将会遇到问题，如果你实现错误的话。
*   根据实现的不同，方法链可能会变得很长。
*   *消费者*可能会忘记用`build()`方法完成陈述，而得不到他们期望的结果。
*   使用更多内存资源。

# 怎样才能避免？

## 默认参数

[Kotlin](https://kotlinlang.org) 支持*默认参数*(其他语言如 C#和 JavaScript 中也有)。

```
fun pow(base: Int, power: Int = 2): Int {
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这可以作为方法重载的替代方法，因为这个方法的*消费者*可以像这样使用它:

```
pow(2) // outputs 4
pow(2, 3) // outputs 8 
```

Enter fullscreen mode Exit fullscreen mode

这可以使我们的生活变得更容易，只需要维护一个单一的方法。

## 命名论据

这使得我们的*消费者*不仅可以准确地输入他们想要分配给一个精确参数的参数，而且我们还可以按照我们想要的任何方式对它们进行重新排序。这在处理“遗留代码”时会很方便，因为这些代码需要很多参数，很难理解。

```
pow(base = 4, power = 3) // outputs 64
pow(power = 3, base = 4) // also outputs 64 
```

Enter fullscreen mode Exit fullscreen mode

## 能不能把它们结合起来？

当然，我亲爱的华生！

```
pow(base = 3) // outputs 9 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们将`base`参数作为`named argument`进行传递，而`power`在我们的方法签名中使用了`default parameter`值。

现在我们知道如何做到这一点，我们可以使用它们来避免 Kotlin 中的生成器设计模式。

首先，让我们通过创建一个汉堡的简化版本`class` :
来检查我们必须用 Java 编写的代码

```
// Hamburger.java

public class HamburgerJava {
    private final boolean hasKetchup;
    private final boolean hasTomatoes;
    private final int meats;

    private HamburgerJava(Builder builder) {
        hasKetchup = builder.hasKetchup;
        hasTomatoes = builder.hasTomatoes;
        meats = builder.meats;
    }

    public static class Builder {
        private boolean hasKetchup;
        private boolean hasTomatoes;
        private int meats;

        public Builder(int meats) {
            if (meats > 3)
                throw new IllegalArgumentException("Cannot order hamburger with more than 3 meats");
            if (meats < 1)
                throw new IllegalArgumentException("A hamburger must have at least 1 meat.");

            this.meats = meats;
        }

        public Builder addKetchup(boolean hasKetchup) {
            this.hasKetchup = hasKetchup;
            return this;
        }

        public Builder addTomatoes(boolean hasTomatoes) {
            this.hasTomatoes = hasTomatoes;
            return this;
        }

        public HamburgerJava build() {
            return new HamburgerJava(this);
        }

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们看看如何使用我们的*汉堡 Java* `class` :

```
HamburgerJava doubleMeatWithEverything = new HamburgerJava.Builder(2)
                .addKetchup(true)
                .addTomatoes(true)
                .build(); 
```

Enter fullscreen mode Exit fullscreen mode

酷，也许你们有些人已经习惯了。

现在让我们看看我们的汉堡`class` :
中的 Kotlin 实现

```
// Hamburger.kt

class Hamburger(
        val meats: Int,
        val hasKetchup: Boolean = false, 
        val hasTomatoes: Boolean = false
) 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看当我们尝试使用它时它是什么样子:

```
val doubleMeatWithEverything = Hamburger(
            meats = 2,
            hasKetchup = true,
            hasTomatoes = true
    ) 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`named arguments`和`default parameters`,我们可以避免不知道传递给每个参数什么值的问题，并且只需要维护一个构造函数。

这种方法的唯一缺点是，我们失去了在对象创建后向其传递值的能力。

我认为这并不常见，是吗？🤔