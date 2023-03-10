# JVM overloads——kot Lin/Java interrop 的重要注释

> 原文：<https://dev.to/adammc331/jvmoverloads---an-essential-annotation-for-kotlinjava-interrop>

当我第一次开始写 Kotlin 时，我最喜欢的一个好处是[默认参数](https://kotlinlang.org/docs/reference/functions.html#default-arguments)。这是一种创建接受默认值的函数(或构造函数)的方法。这样，该方法的用户只需给你你所需要的。然而，这种用默认值重载函数的能力并不支持 1-1 到 Java，如果你打算在你的应用中使用两种语言，你需要确保包含`@JvmOverloads`注释。这篇文章解释了 interrop 的问题以及注释是如何解决的。

## Java 中重载的构造函数

在展示它们如何在 Kotlin 中工作之前，这里是你通常如何在 Java 中编写重载构造函数:

```
public class Task {
    private String description;
    private Date date;
    private boolean isCompleted;

    public Task() {
        this("");
    }

    public Task(String description) {
        this(description, new Date());
    }

    public Task(String description, Date date) {
        this(description, date, false);
    }

    public Task(String description, Date date, boolean isCompleted) {
        this.description = description;
        this.date = date;
        this.isCompleted = isCompleted;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## kot Lin 中的默认参数

下面是 Kotlin 中该类的定义，或者我是这样认为的:

```
// In Task.kt
data class Task(var description: String? = "", var date: Date? = Date(), var isCompleted: Boolean = false)

// In MainActivity.kt
override fun onCreate(savedInstanceState: Bundle?) {
    // All of these statements are valid
    val task1 = Task()
    val task2 = Task("Test")
    val task3 = Task("Test", Date())
    val task4 = Task("Test", Date(), false)
} 
```

Enter fullscreen mode Exit fullscreen mode

在不损失任何灵活性的情况下，我们很快就会意识到使用默认参数节省了多少时间和代码。或者我们有吗？

## Java Interrop 的坎儿

在用默认参数写完上面的 Kotlin 类后，我想在 Java 中使用同样的想法，但不幸的是没有成功:

```
Task task1 = new Task(); // Valid - Java allows default constructor
Task task2 = new Task("Test"); // Compiler error - no constructor 
Task task3 = new Task("Test", new Date()); // Compiler error - no constructor
Task task4 = new Task("Test", new Date(), false); // Valid 
```

Enter fullscreen mode Exit fullscreen mode

我立刻就糊涂了。它在 Kotlin 中有效，但在 Java 中无效，我知道有 100%的 interrop，所以我开始研究其中的区别。它出奇的简单——kot Lin 有自己的构造函数，可以独立于 Java，随心所欲地处理默认参数等规则。然而，如果我们想要完整的 Java interrop，我们需要创建所有必要的重载。

## JvmOverloads 注释

谢天谢地，解决方案只是一个简单的注释。 [@JvmOverloads](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-overloads/) 告诉 Kotlin 编译器为构造函数或函数生成一个重载，不管提供多少默认参数。加上它，我们的数据类将如下所示:

```
data class Task @JvmOverloads constructor(var description: String? = "", var date: Date? = Date(), var isCompleted: Boolean = false) 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何时候我们在 Java 中使用这个类，我们都可以使用我们认为必要的任何重载版本的构造函数，并且仍然只需要一行 Kotlin 代码来定义对象。