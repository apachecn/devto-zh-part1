# Java 中的多线程技术(第 2 部分)

> 原文：<https://dev.to/raulavila/multithreading-in-java-for-dummies-part-2>

在本系列的第一篇文章中，我们看到了一些使用实用方法的基本多线程概念。考虑到乒乓球游戏的实现，我们将继续引入新的改进，我们将使用这些改进来解释在用 Java 实现并发应用程序时每个人都应该知道的一些额外的概念。

这个帖子的起点将是我们在第一个帖子中讨论的最后一个版本之一:

```
public class Player implements Runnable {

    private final String text;

    private Player nextPlayer;

    private volatile boolean mustPlay = false;

    public Player(String text) {
        this.text = text;
    }

    @Override
    public void run() {
        while(!Thread.interrupted()) {
            while (!mustPlay);

            System.out.println(text);

            this.mustPlay = false;
            nextPlayer.mustPlay = true;

        }
    }

    public void setNextPlayer(Player nextPlayer) {
        this.nextPlayer = nextPlayer;
    }

    public void setMustPlay(boolean mustPlay) {
        this.mustPlay = mustPlay;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个版本其实挺恐怖的。在我们的代码中，我们永远无法证明这样做是正确的:

```
while(!mustPlay); 
```

Enter fullscreen mode Exit fullscreen mode

#### 忙着等待

这个语句是[忙等待](https://en.wikipedia.org/wiki/Busy_waiting)的一个例子，它只不过是对一个条件的无限检查，避免应用程序的进度，直到条件为真。这种方法的问题是我们的线程正在使 CPU 过载，因为[线程调度器](http://www.javatpoint.com/thread-scheduler-in-java)没有检测到任何阻止这种线程执行的东西，所以总会有资源保持线程处于“运行”状态(你可以在这里找到一个很好的线程状态图[)。结果是过度和不合理地使用资源。](http://www.uml-diagrams.org/examples/java-6-thread-state-machine-diagram-example.html)

我给你讲一个关于这个的趣事。当我实现本系列的示例代码时，我让我的 IDE 打开，让应用程序运行(当然，还有忙碌的等待)。结果是，我通常持续 6 到 8 小时的电池在不到 2 小时内就耗尽了。让我们想想这种错误的设计在严肃的企业应用中的后果！

#### 锁定

摆脱这种忙碌等待的最简单方法是使用锁。简而言之，锁定是一种机制，当存在状态可以被不同线程共享和修改的资源时，它允许我们在并发应用中实现排除策略。

这种可以被多个线程修改的状态必须使用[临界区](https://en.wikipedia.org/wiki/Critical_section)来保护。Java 提供了不同的机制来实现关键部分，我们将在本文中看到最重要的机制。

### 版本 3:固有锁定

Java 中最古老的创建临界区的机制被称为[固有锁](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)，或者监控锁。在 Java 中创建的每个对象都有一个相关的锁(固有锁或监控锁)，可以通过使用关键字`synchronized` :
在我们的线程中用于排除目标

```
//...
Object myObject = new Object();
//...
synchronized(myObject) {
    //critical section
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用`Object`的一个实例作为锁，这样每个想要访问临界区的线程都必须获得锁，这就是我们在`synchronized`语句中试图做的。如果锁是可用的，则线程获得它，并且它对任何其他线程都不可用。如果一个新线程试图获得锁，它将失败，并且它的状态将被线程调度器设置为“阻塞”。

互联网上到处都是关于使用`synchronized`的例子，所以我不会在这里涉及到很多关于最佳实践的细节。我只补充几点考虑:

*   在`this` ( `synchronized(this)`)进行同步是很常见的，所以自己的实例使用自己作为锁来保护它的客户端免受并发问题的影响。然而，如果我们这样做，我们必须非常小心，因为我们的客户端可能在同一个实例中同步，导致[死锁](https://docs.oracle.com/javase/tutorial/essential/concurrency/deadlock.html)
*   更好的做法是使用私有锁(就像我们在上面的代码片段中使用的那样)。这样我们就不会向外界公开所使用的锁定机制，因为它被封装在自己的类中
*   `synchronized`除了排外还有另一个目标，那就是*能见度*。同样，关键字`volatile`确保了被修改变量的即时可见性，`synchronized`确保了被用作锁的对象的状态的可见性(因此范围更大)。这种可见性由 [Java 内存模型](https://en.wikipedia.org/wiki/Java_memory_model)保证

#### 等待机制

如果我们只使用锁定机制，我们将无法完全消除应用程序中的繁忙等待。我们需要别的东西，这就是所谓的等待机制。

每个对象都公开一个方法，`wait()`。当这个方法被一个线程调用时，它使线程调度器挂起它，改变它的状态为“等待”，即:

```
//the thread state at this point is Running
i++
lock.wait(); // => thread state changes to Waiting 
```

Enter fullscreen mode Exit fullscreen mode

这个例子有点奇怪，因为我们不应该这样调用`wait`。实施等待机制时合适的习语是:

```
synchronized (lock) {
    try {
        while (!condition)
            lock.wait();

        //Execute code after waiting for condition

    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    } 
```

Enter fullscreen mode Exit fullscreen mode

在代码中，我们看到:

1.  需要获取我们想要调用的对象的锁`wait`
2.  这种等待意味着我们在等待“某样东西”。某事是一个条件(条件谓词)，它可以在必须等待之前为真。因此，我们在调用`wait`之前检查条件
3.  等待是在循环中完成的，而不是在 if 语句中完成的，这有几个原因。最重要的一种被称为“虚假唤醒”。从它的名字很容易推断出它是什么，有时一个线程从“等待”状态唤醒，而没有任何人要求它这样做，所以可能发生的情况是条件还不成立，它必须再次等待
4.  最后但并非最不重要的是，`wait`抛出`InterruptedException`，我们在本系列的第一部分中已经讨论过了

看到这一点，我们有一个线程改变到“等待”状态，期待一个条件为真，但有人应该通知一个或多个线程应该醒来...嗯，这是通过方法`notify`和`notifyAll`完成的，正如您可能已经推断出的，这要求一个或所有等待锁的线程醒来并检查状态。习语是:

```
synchronized(lock) {
    //....
    condition = true;
    lock.notifyAll(); //or lock.notify();
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们需要拥有锁来调用对象上的方法。[关于`notify`和`notifyAll`的用法，](http://stackoverflow.com/questions/37026/java-notify-vs-notifyall-all-over-again)写了很多文章，这取决于每个应用程序的意图。确切地说，`notifyAll`的用法是循环等待条件为真的原因之一，有时当条件为真时，所有等待线程中只有一个线程可以继续。

让我们最后看看在应用了我们所看到的所有概念之后，我们的乒乓应用程序会是什么样子:

```
public class Player implements Runnable {

    private final String text;

    private final Object lock;

    private Player nextPlayer;

    private volatile boolean play = false;

    public Player(String text,
                  Object lock) {
        this.text = text;
        this.lock = lock;
    }

    @Override
    public void run() {
        while(!Thread.interrupted()) {
            synchronized (lock) {
                try {
                    while (!play)
                        lock.wait();

                    System.out.println(text);

                    this.play = false;
                    nextPlayer.play = true;

                    lock.notifyAll();

                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }
        }
    }

    public void setNextPlayer(Player nextPlayer) {
        this.nextPlayer = nextPlayer;
    }

    public void setPlay(boolean play) {
        this.play = play;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个应用程序中的锁可以被认为是游戏中的球，在每个回合中只能由一个玩家拥有。我们还看到，在标准输出中打印文本后，玩家通知另一个玩家他可以玩了。我用过`notifyAll`，但也可能是`notify`。

主类与本系列第一篇文章的最后一个版本没有太大的不同:

```
public class Game {

    public static void main(String[] args) {

        Object lock = new Object();

        Player player1 = new Player("ping", lock);
        Player player2 = new Player("pong", lock);

        player1.setNextPlayer(player2);
        player2.setNextPlayer(player1);

        System.out.println("Game starting...!");

        player1.setPlay(true);

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

        System.out.println("Game finished!");
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 版本 4:显式锁和条件

Java 在其并发 API 中公开了一个接口`Lock`，它允许我们实现我们在使用固有锁之前看到的相同的排除机制，但是通过不同的方法。

`Lock`的主要实现是`ReentrantLock`。它有这个名字是因为 Java 中的锁是可重入的。这意味着，一旦一个线程获得了一个锁，如果同一个线程试图获得同一个锁，它就会成功。我们将使用这个 API 实现上面看到的相同示例。

#### 临界截面

```
Lock lock = new ReentrantLock();
//...
lock.lock();
try {
    //critical section...
} finally {
    lock.unlock();
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，我们只需要记住，我们必须调用`finally`子句中的方法`unlock`，以确保即使在出错的情况下锁也被释放。

就我个人而言，我不会说这个机制比`synchronized`提供的更好，因为后者更紧凑。使用`Lock`的最大优势是它提供了一系列方法来实现更复杂的锁定策略，比如:

*   我们试图获取锁，但是如果不成功，线程不会被阻塞
*   公平:我们可以创建一个“公平”的锁。默认情况下，Java 中的锁是不公平的，所以可以选择一个等待的线程来获取锁，即使它是最后一个到达的线程。使用公平锁，将实现 FIFO 锁定

我建议你看一下 [API](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/locks/ReentrantLock.html) ，了解更多信息。

#### 等待机制

这些机制的实现是使用类[条件](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/locks/Condition.html)来完成的。必须从`Lock` :
创建一个`Condition`实例

```
Condition condition = lock.newCondition(); 
```

Enter fullscreen mode Exit fullscreen mode

类`Condition`公开了两个方法`await()`和`signal()`，它们相当于内在锁中的`wait()`和`notify()`。此外，我们可以使用以下方法:

*   `await(long time, TimeUnit unit)`:等待条件最长时间
*   `awaitUninterruptibly()`:不可中断版本的`await()`。这意味着，如果暂停等待条件的线程被中断，这个方法不会抛出众所周知的`InterruptedException`。激活它的唯一方式是通过`signal()` / `signalAll()`(虚假唤醒除外)

一般来说，对于等待机制，我会说`Condition`的使用提供了一系列非常有趣的特性。此外，它允许我们创建与同一个锁相关联的不同条件，这对于内在锁是不可能的。

让我们看看添加了`Lock`和`Condition` API:
之后我们的应用程序的方面

```
public class Player implements Runnable {

    private final String text;

    private final Lock lock;
    private final Condition myTurn;
    private Condition nextTurn;

    private Player nextPlayer;

    private volatile boolean play = false;

    public Player(String text,
                  Lock lock) {
        this.text = text;
        this.lock = lock;
        this.myTurn = lock.newCondition();
    }

    @Override
    public void run() {
        while(!Thread.interrupted()) {
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

我们可以看到，`Condition`的使用使得代码可读性更好。我们使用了方法`awaitUninterruptibly`，这确保了当主线程中断线程时，两个玩家都玩最后一轮(注意:这种方法有一个问题，将在评论部分讨论)。

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

        System.out.println("Game finished!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 奖励，缩放至 N 名玩家

让我们看看将游戏扩展到许多玩家是多么容易，所以他们以特定的顺序在他们之间传球(这不再是乒乓球了:)。应用程序的输出应该是这样的:

```
Game starting...!
player0
player1
player2
player3
player4
player5
...
Game finished! 
```

Enter fullscreen mode Exit fullscreen mode

原来我们根本不需要修改类`Player`！事实上，每个玩家都必须知道游戏中的下一个玩家，所以唯一的改变是在`Game` :
类中进行

```
public class GameScale {

    public static final int NUM_PLAYERS = 6;

    public static void main(String[] args) {
        Lock lock = new ReentrantLock();

        int length = NUM_PLAYERS;

        Player[] players = new Player[length];

        for (int i=0; i < length; i++) {
            Player player = new Player("player"+i, lock);
            players[i] = player;
        }

        for (int i=0; i < length - 1; i++) {
            players[i].setNextPlayer(players[i+1]);
        }
        players[length - 1].setNextPlayer(players[0]);

        System.out.println("Game starting...!");

        players[0].setPlay(true);

        //Threads creation
        Thread[] threads = new Thread[length];
        for (int i=0; i < length; i++) {
            Thread thread = new Thread(players[i]);
            threads[i] = thread;
            thread.start();
        }

        //Let the players play!
        try {
            Thread.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        //Tell the players to stop
        for (Thread thread : threads) {
            thread.interrupt();
        }

        //Don't progress main thread until all players have finished
        try {
            for (Thread thread : threads) {
                thread.join();
            }
        }  catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Game finished!");
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

代码有点复杂，但我认为很容易理解。改变这个常数，我们就可以随心所欲地缩放游戏，并发性将确保游戏按正确的顺序进行。

在本系列的最后一篇文章中，我们将关注线程的创建和管理，这样类`Game`就不会像现在这样神秘了。

(所有代码都可以在这个 [GitHub 库](https://github.com/raulavila/concurrent-pingpong/)中找到)