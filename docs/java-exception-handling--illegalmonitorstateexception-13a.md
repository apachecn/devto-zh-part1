# Java 异常处理–IllegalMonitorStateException

> 原文：<https://dev.to/airbrake/java-exception-handling--illegalmonitorstateexception-13a>

继续我们深入的 [**Java 异常处理**](https://airbrake.io/blog/java-exception-handling/the-java-exception-class-hierarchy) 系列，今天我们将进入**IllegalMonitorStateException**。当一个线程被指示等待一个指定线程不拥有所有权的对象的监视器时，抛出`IllegalMonitorStateException`。

在本文中，我们将更详细地探索`IllegalMonitorStateException`，从它在整个 [Java 异常层次结构](https://airbrake.io/blog/java-exception-handling/the-java-exception-class-hierarchy)中的位置开始。我们还将看一个功能代码示例，它说明了多线程和并发等待可能是如何实现的，以及如果这样的实现中有一些小缺陷，那么`IllegalMonitorStateExceptions`会如何出现。让我们开始吧！

## 技术破落

所有的 Java 错误都实现了 [`java.lang.Throwable`](https://airbrake.io/blog/java-exception-handling/the-java-exception-class-hierarchy) 接口，或者从其中的另一个继承类扩展而来。此错误的完整异常层次结构如下:

*   [T2`java.lang.Object`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html)
    *   [T2`java.lang.Throwable`](https://airbrake.io/blog/java-exception-handling/the-java-exception-class-hierarchy)
    *   [T2`java.lang.Exception`](https://docs.oracle.com/javase/8/docs/api/java/lang/Exception.html)
        *   [T2`java.lang.RuntimeException`](https://docs.oracle.com/javase/8/docs/api/java/lang/RuntimeException.html)
        *   `IllegalMonitorStateException`

## 完整代码示例

下面是我们将在本文中使用的完整代码示例。如果您想亲自试验代码，看看一切是如何工作的，可以复制并粘贴它。

```
// Main.java
package io.airbrake;

import io.airbrake.utility.Logging;

public class Main {

    static Main main = null;

    public static void main(String[] args)
    {
        main = new Main();
    }

    private Main()
    {
        Logging.lineSeparator("DUAL THREADING");
        DualThreadingTest();

        Logging.lineSeparator("DUAL THREADING w/ OWNERSHIP");
        DualThreadingWithOwnershipTest();
    }

    private void DualThreadingTest() {
        try {
            // Create manager.
            ThreadingManager manager = new ThreadingManager();

            // Create runners and add to manager.
            manager.addRunner(new Runner("primary"));
            manager.addRunner(new Runner("secondary"));

            // Create threads, set runners, and add to manager.
            manager.addThread(new Thread(manager.getRunner("primary"), "primary"));
            manager.addThread(new Thread(manager.getRunner("secondary"), "secondary"));

            // Start threads.
            manager.getThread("primary").start();
            manager.getThread("secondary").start();
        } catch (IllegalMonitorStateException exception) {
            // Output expected IllegalMonitorStateExceptions.
            Logging.log(exception);
        } catch (Exception exception) {
            // Output unexpected Exceptions.
            Logging.log(exception, false);
        }
    }

    private void DualThreadingWithOwnershipTest() {
        try
        {
            // Create manager.
            ThreadingManager manager = new ThreadingManager();

            // Create runners and add to manager.
            manager.addRunner(new Runner("primary"));
            manager.addRunner(new Runner("secondary"));

            // Create threads, set runners, and add to manager.
            manager.addThread(new Thread(manager.getRunner("primary"), "primary"));
            manager.addThread(new Thread(manager.getRunner("secondary"), "secondary"));

            // Set runner thread ownership.
            manager.getRunner("primary").setThread(manager.getThread("primary"));
            manager.getRunner("secondary").setThread(manager.getThread("secondary"));

            // Start threads.
            manager.getThread("primary").start();
            manager.getThread("secondary").start();
        } catch (IllegalMonitorStateException exception) {
            // Output expected IllegalMonitorStateExceptions.
            Logging.log(exception);
        } catch (Exception exception) {
            // Output unexpected Exceptions.
            Logging.log(exception, false);
        }
    }
}

// ThreadingManager.java
package io.airbrake;

import java.util.ArrayList;
import java.util.Objects;

public class ThreadingManager {
    private ArrayList<Runner> runners = new ArrayList<>();
    private ArrayList<Thread> threads = new ArrayList<>();

    ThreadingManager() { }

    void addRunner(Runner runner) {
        this.runners.add(runner);
    }

    void addThread(Thread thread) {
        threads.add(thread);
    }

    public Runner getRunner(int index) {
        return runners.get(index);
    }

    Runner getRunner(String name) {
        for (Runner runner : runners) {
            if (Objects.equals(runner.getName(), name)) {
                return runner;
            }
        }
        return null;
    }

    public ArrayList<Runner> getRunners() {
        return runners;
    }

    public Thread getThread(int index) {
        return threads.get(index);
    }

    Thread getThread(String name) {
        for (Thread thread : threads) {
            if (Objects.equals(thread.getName(), name)) {
                return thread;
            }
        }
        return null;
    }

    public ArrayList<Thread> getThreads() {
        return threads;
    }

    public void setRunners(ArrayList<Runner> runners) {
        this.runners = runners;
    }

    public void setThreads(ArrayList<Thread> threads) {
        this.threads = threads;
    }
}

// Runner.java
package io.airbrake;

import io.airbrake.utility.Logging;

public class Runner implements Runnable
{
    private String name;
    private Thread thread;

    Runner(String name) {
        setName(name);
    }

    Runner(String name, Thread thread) {
        setName(name);
    }

    String getName() {
        return this.name;
    }

    private Thread getThread() {
        return thread;
    }

    private void setName(String name) {
        this.name = name;
    }

    void setThread(Thread thread) {
        this.thread = thread;
    }

    public void run()
    {
        try
        {
            // Check for ownership thread.
            if (getThread() == null) {
                Logging.log(String.format("Waiting for thread %s in runner %s.", "Main", getName()));
                // If no thread, wait for main thread.
                Main.main.wait();
            } else {
                synchronized (getThread()) {
                    Logging.log(String.format("Waiting for thread %s in runner %s.", getThread().getName(), getName()));
                    // If thread, invoke wait().
                    getThread().wait();
                }
            }
        } catch (IllegalMonitorStateException exception) {
            // Output expected IllegalMonitorStateExceptions.
            Logging.log(exception);
        } catch (Exception exception) {
            // Output unexpected Exceptions.
            Logging.log(exception, false);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个代码示例也使用了 [`Logging.java`](https://github.com/GabeStah/Airbrake.io/blob/master/lib/java/src/io/airbrake/utility/Logging.java) 类，其源代码可以在 GitHub 上找到[。](https://github.com/GabeStah/Airbrake.io/blob/master/lib/java/src/io/airbrake/utility/Logging.java)

## 应该什么时候用？

正如官方文档所描述的，当一个线程试图等待一个对象的监视器，或者通知等待所述对象的监视器的其他线程，而那个线程不*拥有所讨论的*监视器时，就会发生`IllegalMonitorStateException`。换句话说，如果在一个对象上调用 [`Object.wait()`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#wait--) 方法，而这个对象不是由*当前*线程创建的，则会抛出一个`IllegalMonitorStateException`。线程可以通过以下方式成为对象监视器的所有者:

*   执行对象的同步实例方法。
*   执行在对象上同步的 synchronized 语句的代码块。
*   执行对象的同步静态方法，*如果*对象是`Class`类型。

通过一些代码示例，这更容易理解，但是在我们开始之前，有必要简单地指出，在现代 Java 中，使用`Object.wait()`和`notify()`方法通常不是执行多线程或并发编程的首选技术。如果你对大多数 Java 开发如何处理并发感兴趣，看看 [`java.util.concurrent`](https://docs.oracle.com/javase/8/docs/api/index.html?java/util/concurrent/package-summary.html) 包。

我们的代码示例的目标很简单:创建两个线程，并为每个线程分配它们自己的 [`Runnable`](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html) 对象，这是一个实现`run()`方法的接口。一个`Runnable`实例可以传递给一个新的`Thread`实例，`Runnable.run()`方法一旦启动就会被线程自动执行。

因此，我们从我们的`Runner`类开始，它实现了`Runnable`接口:

```
package io.airbrake;

import io.airbrake.utility.Logging;

public class Runner implements Runnable
{
    private String name;
    private Thread thread;

    Runner(String name) {
        setName(name);
    }

    Runner(String name, Thread thread) {
        setName(name);
    }

    String getName() {
        return this.name;
    }

    private Thread getThread() {
        return thread;
    }

    private void setName(String name) {
        this.name = name;
    }

    void setThread(Thread thread) {
        this.thread = thread;
    }

    public void run()
    {
        try
        {
            // Check for ownership thread.
            if (getThread() == null) {
                Logging.log(String.format("Waiting for thread %s in runner %s.", "Main", getName()));
                // If no thread, wait for main thread.
                Main.main.wait();
            } else {
                synchronized (getThread()) {
                    Logging.log(String.format("Waiting for thread %s in runner %s.", getThread().getName(), getName()));
                    // If thread, invoke wait().
                    getThread().wait();
                }
            }
        } catch (IllegalMonitorStateException exception) {
            // Output expected IllegalMonitorStateExceptions.
            Logging.log(exception);
        } catch (Exception exception) {
            // Output unexpected Exceptions.
            Logging.log(exception, false);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

除了几个 getters 和 setters，核心逻辑在`run()`方法中。它首先通过`getThread()`方法检查`Runner`的`Thread thread`属性是否存在。如果不是，它显式调用`Main.main.wait()`方法，否则它为这个`Runner`实例调用关联的`Thread`对象的`wait()`方法。

`ThreadingManager`类只是一个助手类，通过将`Runners`和`Threads`存储在私有的`ArrayList<Runner> runners`和`ArrayList<Thread> threads`属性中，使得生成和跟踪`Runners`和【】变得更加容易。我们还将使用许多助手方法，比如`getRunner(String name)`，来检索`Runners`和`Threads`的特定实例，以便稍后进行测试:

```
package io.airbrake;

import java.util.ArrayList;
import java.util.Objects;

public class ThreadingManager {
    private ArrayList<Runner> runners = new ArrayList<>();
    private ArrayList<Thread> threads = new ArrayList<>();

    ThreadingManager() { }

    void addRunner(Runner runner) {
        this.runners.add(runner);
    }

    void addThread(Thread thread) {
        threads.add(thread);
    }

    public Runner getRunner(int index) {
        return runners.get(index);
    }

    Runner getRunner(String name) {
        for (Runner runner : runners) {
            if (Objects.equals(runner.getName(), name)) {
                return runner;
            }
        }
        return null;
    }

    public ArrayList<Runner> getRunners() {
        return runners;
    }

    public Thread getThread(int index) {
        return threads.get(index);
    }

    Thread getThread(String name) {
        for (Thread thread : threads) {
            if (Objects.equals(thread.getName(), name)) {
                return thread;
            }
        }
        return null;
    }

    public ArrayList<Thread> getThreads() {
        return threads;
    }

    public void setRunners(ArrayList<Runner> runners) {
        this.runners = runners;
    }

    public void setThreads(ArrayList<Thread> threads) {
        this.threads = threads;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

说到测试，我们来看看我们的`Main`类和第一个测试方法，`DualThreadingTest()` :

```
public class Main {
    // ...

    private void DualThreadingTest() {
        try {
            // Create manager.
            ThreadingManager manager = new ThreadingManager();

            // Create runners and add to manager.
            manager.addRunner(new Runner("primary"));
            manager.addRunner(new Runner("secondary"));

            // Create threads, set runners, and add to manager.
            manager.addThread(new Thread(manager.getRunner("primary"), "primary"));
            manager.addThread(new Thread(manager.getRunner("secondary"), "secondary"));

            // Start threads.
            manager.getThread("primary").start();
            manager.getThread("secondary").start();
        } catch (IllegalMonitorStateException exception) {
            // Output expected IllegalMonitorStateExceptions.
            Logging.log(exception);
        } catch (Exception exception) {
            // Output unexpected Exceptions.
            Logging.log(exception, false);
        }
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们的测试从一个新的`ThreadingManager`实例开始，从中我们创建了两个新的`Runners`，分别命名为`primary`和`secondary`。然后，我们创建两个同名的新`Threads`，并从我们的`manager`实例中检索适当的`Runners`，以传递给那些`new Thread(...)`构造函数调用。如前所述，将一个`Runnable`实例(比如一个`Runner`对象)传递给一个`Thread`构造函数可以确保`Runner's` `run()`方法在线程开始执行时被执行。因此，我们的最后一步是`start()`两个线程。

执行`DualThreadingTest()`代码产生以下输出，其中包括抛出一些`IllegalMonitorStateExceptions` :

```
------------ DUAL THREADING ------------
Waiting for thread Main in runner primary.
Waiting for thread Main in runner secondary.
[EXPECTED] java.lang.IllegalMonitorStateException
[EXPECTED] java.lang.IllegalMonitorStateException 
```

Enter fullscreen mode Exit fullscreen mode

因为我们没有明确地告诉我们的`Runner`实例每个被分配了哪个特定的`Thread`实例，所以它们都试图调用`Main.main`对象的`wait()`方法，如下面来自`Runner.run()`方法:
的片段所示

```
// ...

// Check for ownership thread.
if (getThread() == null) {
    Logging.log(String.format("Waiting for thread %s in runner %s.", "Main", getName()));
    // If no thread, wait for main thread.
    Main.main.wait();
} else {
    synchronized (getThread()) {
        Logging.log(String.format("Waiting for thread %s in runner %s.", getThread().getName(), getName()));
        // If thread, invoke wait().
        getThread().wait();
    }
}

// ... 
```

Enter fullscreen mode Exit fullscreen mode

在最基本的层面上，Java 中的每个对象都有一个与之关联的内在锁。每当一个线程需要独占访问该对象时，它必须获得该对象固有锁的所有权。一旦独占访问要求完成，它就可以释放锁。在某些场景中，比如我们上面创建的场景，一个线程可能会尝试对一个没有独占锁的对象进行操作，这在某些情况下会导致`IllegalMonitorStateException`。

一种解决方案是使用内置的 [`synchronized`](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html) 语句，该语句指定一个对象，该对象将为语句中的代码块提供固有锁。你可以在上面我们的`Runner.run()`方法的`else { ... }`块中看到这样的例子。通过使用`synchronized (getThread())`语句，我们让`synchronized`块中的代码访问这个`Runner`实例的相关线程的锁(由`getThread()`获取)。值得注意的是，通常最好的编程实践是使用一个局部变量并通过`getThread()`检索一次`Thread`实例，然后在整个代码中重用它。然而，在这种情况下，在`synchronized`语句中使用局部变量可能很难控制，因为同步可能会与实际产生局部变量的代码之外的代码发生无序(更慢或更快)。因此，在这种情况下，每次都进行显式调用更安全。

为了测试这个行为，我们有`DualThreadingWithOwnershipTest()`方法，它类似于`DualThreadingTest()`，除了我们在为两个`Threads` :
调用`start()`之前，显式地将`Thread`实例分配给`Runner`实例

```
private void DualThreadingWithOwnershipTest() {
    try
    {
        // Create manager.
        ThreadingManager manager = new ThreadingManager();

        // Create runners and add to manager.
        manager.addRunner(new Runner("primary"));
        manager.addRunner(new Runner("secondary"));

        // Create threads, set runners, and add to manager.
        manager.addThread(new Thread(manager.getRunner("primary"), "primary"));
        manager.addThread(new Thread(manager.getRunner("secondary"), "secondary"));

        // Set runner thread ownership.
        manager.getRunner("primary").setThread(manager.getThread("primary"));
        manager.getRunner("secondary").setThread(manager.getThread("secondary"));

        // Start threads.
        manager.getThread("primary").start();
        manager.getThread("secondary").start();
    } catch (IllegalMonitorStateException exception) {
        // Output expected IllegalMonitorStateExceptions.
        Logging.log(exception);
    } catch (Exception exception) {
        // Output unexpected Exceptions.
        Logging.log(exception, false);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，这将在`Runner.run()`方法中为每个适当的线程调用`synchronized(...)`语句，确保我们的并发性按预期工作，并且不会在输出:
中抛出任何`inels`

```
----- DUAL THREADING w/ OWNERSHIP ------
Waiting for thread primary in runner primary.
Waiting for thread secondary in runner secondary. 
```

Enter fullscreen mode Exit fullscreen mode

[Airbrake-Java 库](https://airbrake.io/languages/java_bug_tracker?utm_source=blog&utm_medium=end-post&utm_campaign=airbrake-java)为所有基于 Java 的项目提供实时错误监控和自动异常报告。与 Airbrake 最先进的 web 仪表盘紧密集成，确保`Airbrake-Java`为您提供关于应用健康和错误率的全天候状态更新。`Airbrake-Java`轻松集成所有最新的 Java 框架和平台，如`Spring`、`Maven`、`log4j`、`Struts`、`Kotlin`、`Grails`、`Groovy`等等。此外，`Airbrake-Java`允许您轻松定制异常参数，并为您提供完整的、可配置的过滤功能，以便您只收集最重要的错误。

查看所有令人惊叹的特性 [Airbrake-Java](https://airbrake.io/languages/java_bug_tracker?utm_source=blog&utm_medium=end-post&utm_campaign=airbrake-java) 所提供的特性，亲自看看为什么这么多世界上最好的工程团队都在使用 Airbrake 来革新他们的异常处理实践！