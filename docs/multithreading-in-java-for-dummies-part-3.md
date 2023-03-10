# Java 中的多线程技术(第 3 部分)

> 原文：<https://dev.to/raulavila/multithreading-in-java-for-dummies-part-3>

这将是本系列关于多线程的最后一篇文章。在第二篇文章中，我们让我们的乒乓球游戏处于一个很好的位置，线程(玩家)在等待轮到他们时被阻塞，并且有可能根据需要增加玩家的数量。然而，有些东西并不是很优雅，类`Game`负责创建、启动和结束线程，并与它们同步以避免主线程在玩家之前结束。我们来回顾一下:

```
//...
Thread thread2 = new Thread(player2);
thread2.start();
Thread thread1 = new Thread(player1);
thread1.start();

//Let the players play!
try {
   Thread.sleep(2);
} catch (InterruptedException e) {
   e.printStackTrace();
}

//Tell the players to stop
thread1.interrupt();
thread2.interrupt();

//Wait until players finish
try {
   thread1.join();
   thread2.join();
} catch (InterruptedException e) {
   e.printStackTrace();
}
//... 
```

Enter fullscreen mode Exit fullscreen mode

#### 线程管理/线程池

除了乏味之外，如果我们不方便地封装线程的创建，我们的代码将不会非常有凝聚力，因为我们将游戏本身的逻辑耦合到并发管理。此外，创建线程是很昂贵的，在复杂的应用程序中，这在性能方面会有很大的代价。

Java 并发 API 导出了一系列的类和接口，允许我们高度灵活地封装线程创建管理:`Executor`框架。它的三个主要元素是:

*   `Executor`:单一方法的接口，`execute(Runnable)`。这个框架的想法是我们处理任务而不是线程，所以我们要求`Executor`的实例在可能的时候运行任务(它是`Runnable`的实例)
*   `ExecutorService`:接口扩展`Executor`，它发布了一系列更高级的方法，来控制要完成的工作的整个生命周期(`shutdown`、`awaitTermination`，并运行`Callable`类型的任务，这只是一个返回值的`Runnable`([更多信息在此](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Callable.html))。在[官方文档](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ExecutorService.html)中，你可以读到更多关于这个接口的所有可能性
*   前面两个组件是接口，如果我们愿意，我们可以创建自己的实现。然而，最常见的用例已经在 JDK 实现。为了使用这些实现，我们需要使用由`Executors`公开的静态工厂方法请求一个实例

一般来说，术语“线程池”是指我们用来处理线程的`Executor` / `ExecutorService`的实现。通过调用`Executors`公开的静态方法可以获得的最常见的类型有:

*   单线程执行器(`newSingleThreadExecutor`):包含一个单线程，使用不多
*   固定线程池(`newFixedThreadPool`):它包含恒定数量的“活动”线程，这些线程等待接收任务来运行
*   缓存线程池(`newCachedThreadPoool`):它维护一个线程池，该线程池可以根据需求增长或收缩
*   调度线程池(`newScheduledThreadPool`):用于调度任务的执行。它返回一个 [ScheduledExecutorService](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ScheduledExecutorService.html) 的实例，因为`ExecutorService`没有公开合适的方法来调度未来的任务，只是为了尽快执行它们

### 乒乓，版本 5:线程池

在不修改类`Player`的情况下，我们可以修改类`Game`来使用线程池，而不是自己负责创建、启动和停止线程的繁琐任务。让我们来看看:

```
public class Game {

    public static void main(String[] args) {
        Lock lock = new ReentrantLock();

        Player player1 = new Player("ping", lock);
        Player player2 = new Player("pong", lock);

        player1.setNextPlayer(player2);
        player2.setNextPlayer(player1);

        System.out.println("Game starting...!");

        player1.setPlay(true);

        ExecutorService executor = Executors.newFixedThreadPool(2);

        executor.execute(player1);
        executor.execute(player2);

        sleep(2);

        executor.shutdownNow();

        System.out.println("Game finished!");
    }

    public static void sleep(long ms) {
        try {
            Thread.sleep(ms);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用一个包含两个线程的池(每个玩家一个线程)，我们向它发送要运行的任务。我们让主线程休眠 2ms，这样两个玩家都可以玩一会儿，我们调用方法`shutdownNow()`，这相当于像以前版本那样中断线程，但是把所有逻辑封装在池中。有必要调用`shutdownNow`而不是`shutdown`，因为后者会等到正在运行的任务完成，返回一个包含未决任务的列表。我们的玩家无限期地玩，直到他们被打断，所以如果我们试图用`shutdown`结束，应用程序将永远不会结束！

嗯，如果我们运行这个版本几次，我们会看到它有时像预期的那样工作，而其他时候它会显示这样的输出:

```
Game starting...!
ping
pong
//...
Game finished!
pong 
```

Enter fullscreen mode Exit fullscreen mode

发生了什么事？在请求两个线程都中断之后，主线程有可能在其余线程之前完成。这就是为什么文本“游戏结束！”出现在最后一个“乒乓”转弯之前。探索`ExecutorService` API，我们可以看到一个叫做`awaitTermination`的方法。这个方法阻塞调用它的线程，直到池中的所有任务完成，或者直到参数传递的超时过期:

```
//...
ExecutorService executor = Executors.newFixedThreadPool(2);

executor.execute(player1);
executor.execute(player2);

sleep(2);

executor.shutdownNow();

try {
    executor.awaitTermination(5, TimeUnit.SECONDS);
} catch (InterruptedException e) {
    System.out.println("Main thread interrupted while waiting for players to finish");
}

System.out.println("Game finished!");
//... 
```

Enter fullscreen mode Exit fullscreen mode

我们最终得到了预期的输出，游戏的行为和预期的一样，有了一个更干净、可读性更好的主类。我们完成了吗？还没有！

#### 壁垒

进入/退出屏障是同步机制，便于线程组(进入屏障)的同时执行，或者等待整个线程组结束。

我们以前在本帖中见过退出壁垒的概念，用的是`awaitTermination`。这种方法允许我们创建一个退出屏障，但它迫使我们设置一个超时(可以是几个小时，但这是一个超时！).我们希望建立一个没有超时的退出壁垒。

为了理解什么是进入壁垒，我们将向`Game` :
添加一条指令

```
//...
executor.execute(player1);
sleep(1000);
executor.execute(player2);
//... 
```

Enter fullscreen mode Exit fullscreen mode

在让第二个玩家开始游戏之前，我们让主线程休眠一秒钟。尽管这个结果在这里很难重现，因为它与应用程序的时间有关，但还是会发生这样的事情:

```
Game starting...!
ping
// Waiting 1 second
pong 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，玩家“乒”在玩游戏，但是在一秒钟内没有任何人可以一起玩！所以游戏“暂停”了一秒钟，可能是几分钟(主线程启动第二个线程/玩家的时间)。这种情况并不理想，因为我们正在启动一个并发进程，在所有线程准备好之前，需要存在几个线程。为了避免这种情况，我们需要一个进入壁垒。

并发 API 中有几个类可以用作屏障，但最简单的一个，也是我们将用作进入和退出屏障的一个是`CountdownLatch`。这个类的用法可以总结为三点:

1.  我们用初始化为 **N** 的计数器创建一个屏障
2.  依赖于屏障必须继续的线程将调用`await()`，并且将被阻塞，直到计数器达到零。还有一个带超时的`await`方法
3.  当必要条件满足时，能够影响屏障打开的参与者将调用`countDown()`。一般来说，必须满足 **N** 的条件才能打开屏障

### 版本 6:进入/退出壁垒

在这个新版本中，我们必须修改`Game`和`Player`。让我们看看他们会是什么样子:

```
public class Player implements Runnable {

    private final String text;

    private final Lock lock;
    private final Condition myTurn;

    private final CountDownLatch entryBarrier;
    private final CountDownLatch exitBarrier;

    private Condition nextTurn;

    private Player nextPlayer;

    private volatile boolean play = false;

    public Player(String text,
                  Lock lock,
                  CountDownLatch entryBarrier,
                  CountDownLatch exitBarrier) {
        this.text = text;
        this.lock = lock;
        this.myTurn = lock.newCondition();

        this.entryBarrier = entryBarrier;
        this.exitBarrier = exitBarrier;
    }

    @Override
    public void run() {
        if(entryBarrierOpen())
            play();
    }

    public boolean entryBarrierOpen() {
        try {
            entryBarrier.await();
            return true;
        } catch (InterruptedException e) {
            System.out.println("Player "+text+
                                " was interrupted before starting Game!");
            return false;
        }
    }

    private void play() {
        while (!Thread.interrupted()) {
            lock.lock();

            try {
                while (!play)
                    myTurn.awaitUninterruptibly();

                System.out.println(text);

                this.play = false;
                nextPlayer.play = true;

                nextTurn.signal();
            } finally {
                lock.unlock();
            }
        }

        exitBarrier.countDown();
    }

    public void setNextPlayer(Player nextPlayer) {
        this.nextPlayer = nextPlayer;
        this.nextTurn = nextPlayer.myTurn;
    }

    public void setPlay(boolean play) {
        this.play = play;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个类直到入口屏障打开才开始，当它被中断结束时，它调用出口屏障上的`countDown`，这将是`Game`如何知道两个玩家都完成了。

在你继续阅读之前，想一想我们必须初始化`entryBarrier`和`exitBarrier`中的计数器的值...

```
public class Game {

    public static void main(String[] args) {
        CountDownLatch entryBarrier = new CountDownLatch(1);
        CountDownLatch exitBarrier = new CountDownLatch(2);

        Lock lock = new ReentrantLock();

        Player player1 = new Player("ping", lock, entryBarrier, exitBarrier);
        Player player2 = new Player("pong", lock, entryBarrier, exitBarrier);

        player1.setNextPlayer(player2);
        player2.setNextPlayer(player1);

        System.out.println("Game starting...!");

        player1.setPlay(true);

        ExecutorService executor = Executors.newFixedThreadPool(2);

        executor.execute(player1);

        sleep(1000);

        executor.execute(player2);

        entryBarrier.countDown();

        sleep(2);

        executor.shutdownNow();

        try {
            exitBarrier.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Game finished!");
    }

    public static void sleep(long ms) {
        try {
            Thread.sleep(ms);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

事实上，入口屏障的计数器设置为 1，因为一旦主线程将所有任务传递给线程池，它就会被打开，而出口屏障的计数器设置为 2，这是在主线程可以前进之前必须完成执行的参与者的数量。

通过这种方式，我们的应用程序中的时间是我们所期望的，尽管我们的代码有点复杂。问题是并发本身相当复杂，所以很难完美地封装所有使用的机制。

在结束这篇文章之前，我想提一下，退出障碍已经被添加到这个版本中，带有说教的目的。等待一组线程结束的最佳机制是通过`awaitTermination`，使用一个合理的超时，所以如果我们达到这个超时，这将是因为在我们等待它终止的任务之一中发生了故障。我在 GitHub 中添加了一个[版本 7](https://github.com/raulavila/concurrent-pingpong/tree/master/src/main/java/com/raulavila/pingpong/version7) ，它使用了一个进入壁垒，并使用`awaitTermination`作为退出壁垒。这个版本可以被认为是应用程序的最佳版本。

我希望这一系列文章有助于澄清用 Java 实现的并发应用程序中使用的许多概念。如果你想了解更多，关于这个主题的最好的书是 Brian Goetz 的[Java Concurrency In Practice](https://www.amazon.com/Java-Concurrency-Practice-Brian-Goetz/dp/0321349601/ref=sr_1_1)。

(所有代码都可以在这个 [GitHub 库](https://github.com/raulavila/concurrent-pingpong/)中找到)