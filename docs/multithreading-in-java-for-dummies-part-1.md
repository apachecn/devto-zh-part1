# Java 中的多线程技术(第 1 部分)

> 原文：<https://dev.to/raulavila/multithreading-in-java-for-dummies-part-1>

在本系列中，我们将回顾几个基本概念，以提高 Java 中的并发编程。我知道，互联网上充满了参考资料，教程，文章...关于题目，但是我在这里的意图是用一个不同的视角。我不会详细解释理论和/或使用过于复杂的例子，而是尝试使用一种实用的方法，从头开始开发一个简单的应用程序，它将在引入我们需要知道的不同概念和 API 的同时增长，以便在 Java 中实现并发应用程序。希望我能达到我的目标:)

## 应用:乒乓球

就这么简单。我们的 Java 应用程序将在标准输出中显示文本“ping”/“pong”，以及开始和结束游戏的页眉和页脚:

```
Game starting...!
ping
pong
ping
pong
//....
Game finished! 
```

Enter fullscreen mode Exit fullscreen mode

将有两个玩家(或演员)，他们将打印文本“ping”和“pong”。演员“平”将首先出场。

### 版本零:单线程

第一个版本会在单个执行线程中运行，所以这里不会有任何并发编程:)。我们将实现的第一个版本将在两个玩家都参与了固定次数后完成，比如说 10 次(在常量`MAX_TURNS`中配置)。

这是为我们的第一个版本实现类播放器的代码:

```
public class Player {

    private final String text;

    private int turns = Game.MAX_TURNS;

    private Player nextPlayer;

    public Player(String text) {
        this.text = text;
    }

    public void play() {
        if (!gameFinished()) {
            System.out.println(text);
            turns--;
            nextPlayer.play();
        }
    }

    private boolean gameFinished() {
        return turns == 0;
    }

    public void setNextPlayer(Player nextPlayer) {
        this.nextPlayer = nextPlayer;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

每个玩家打印文本，并要求另一个玩家玩，所以他们是交替的。连接两个玩家并开始游戏的类也很简单:

```
public class Game {

    public static final int MAX_TURNS = 10;

    public static void main(String[] args) {

        Player player1 = new Player("ping");
        Player player2 = new Player("pong");

        player1.setNextPlayer(player2);
        player2.setNextPlayer(player1);

        System.out.println("Game starting...!");

        player1.play();

        System.out.println("Game finished!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到,`Player`构造函数没有另一个玩家的参数。这是因为我们有一个循环引用，所以第二个播放器可能还没有实例化，我们必须通过 setter 注入它。

`Player`中的属性`text`被声明为`final`。如果我们知道类属性不会被修改，那么在并发应用程序中(实际上在所有应用程序中)将类属性声明为`final`是一个很好的实践。不仅我们的代码将更加可靠，这保证了我们的属性在线程之间的可见性，这一概念被称为“安全发布”。你可以在这里阅读关于安全出版[的精彩讨论。进一步来说，我们应该总是试图将我们的类设计成](http://stackoverflow.com/questions/801993/java-multi-threading-safe-publication)[不可变的](https://docs.oracle.com/javase/tutorial/essential/concurrency/imstrat.html)，即使在我们的例子中这是不可能的。

### 版本一:玩家如线程

让我们改进我们的应用程序，使其并发工作。我们将一次添加一个小的改进，发现我们最初的方法并不像预期的那样工作。

首先，让我们的`Player`类实现`Runnable`(更多信息[在这里](https://docs.oracle.com/javase/tutorial/essential/concurrency/runthread.html) ):

```
public class Player implements Runnable {

    private final String text;

    private int turns = Game.MAX_TURNS;

    private Player nextPlayer;

    private boolean mustPlay = false;

    public Player(String text) {
        this.text = text;
    }

    @Override
    public void run() {
        while(!gameFinished()) {

            while (!mustPlay); //Busy Waiting

            System.out.println(text);
            turns--;

            this.mustPlay = false;
            nextPlayer.mustPlay = true;

        }
    }

    private boolean gameFinished() {
        return turns == 0;
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

重要的方法是`run`，它包含一个循环，该循环迭代直到玩家的回合数结束。此外，在每次迭代之后，会有一个[忙着等待](https://en.wikipedia.org/wiki/Busy_waiting)，直到轮到玩家行动。当这种情况发生时，玩家打印文本，它将自己的`mustPlay`设置为`false`，并告诉其他玩家开始游戏。

这个版本与之前版本的一个很大的区别是，在第一个版本中，一个玩家告诉另一个玩家使用直接方法调用(即`play`)来玩，而在这里，它会修改一个标志的值(`mustPlay`)，每个玩家都会单独并不断地检查。

让我们看看我们的类`Game`现在会是什么样子:

```
public class Game {

    public static final int MAX_TURNS = 10;

    public static void main(String[] args) {

        Player player1 = new Player("ping");
        Player player2 = new Player("pong");

        player1.setNextPlayer(player2);
        player2.setNextPlayer(player1);

        System.out.println("Game starting...!");

        player1.setMustPlay(true);  //Plays first!!!

        Thread thread2 = new Thread(player2);
        thread2.start();
        Thread thread1 = new Thread(player1);
        thread1.start();

        System.out.println("Game finished!");
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

最大的区别是线程是分开启动的，我们只负责充分设置标志`mustPlay`。事实上，我故意首先启动了 player2 的线程，以确认即使在这种情况下，打印的第一条消息也是“ping”。

让我们看看当我们启动应用程序时会发生什么:

```
Game starting...!
ping
Game finished! 
```

Enter fullscreen mode Exit fullscreen mode

这不是我们所期望的...发生了什么事？我们的应用程序现在有三个线程:

*   主线程(`Game.main`)
*   玩家 1 的线程
*   玩家 2 的线程

问题是，主线程在其余线程启动后立即结束，因此，尽管其余线程继续执行并正确结束，但我们的 IDE 并没有反映这两个附加线程中生成的输出，这造成了双重混淆，因为我们看到了消息“游戏结束！”。为了避免这种情况，有一个简单的解决方案，使用方法`join` :

```
public class Game {

    public static final int MAX_TURNS = 10;

    public static void main(String[] args) {

        Player player1 = new Player("ping");
        Player player2 = new Player("pong");

        player1.setNextPlayer(player2);
        player2.setNextPlayer(player1);

        System.out.println("Game starting...!");

        player1.setMustPlay(true);

        Thread thread2 = new Thread(player2);
        thread2.start();
        Thread thread1 = new Thread(player1);
        thread1.start();

        //Wait until both threads finish
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

该方法将执行中线程的进度限定为调用方法`join`的线程的终结。`join`是一个阻塞方法(即阻塞调用它的线程)，阻塞方法可以被中断，所以它抛出 ckecked 异常`InterruptedException`。我们将在这篇文章的后面更多地讨论中断线程的复杂性。

因此，理论上我们的主线程现在将等待，直到两个玩家都用完了回合...或者也许不是？好吧，让我们运行这个应用程序几次，根据我们的运气，有可能一切都是正确的，但是如果我们运行几次，很有可能我们迟早会得到这样的输出:

```
Game starting...!
ping 
```

Enter fullscreen mode Exit fullscreen mode

我们的应用程序被阻止，根本没有进展！

发生了什么事？并发编程的主要问题之一是“可见性”。如果我们遵循由 [Java 内存模型](http://en.wikipedia.org/wiki/Java_memory_model)规定的一套准则，具体来说是由“发生之前”关系规定的，那么 Java 只能保证线程间属性的可见性。根据维基百科，在 Java 中，这种关系表示:

> 具体来说，在 Java 中，先发生后关系保证了语句 A 写入的内存对语句 B 是可见的，也就是说，语句 A 在语句 B 开始读取之前完成了写入

在我们的代码中，我们没有遵循任何确保线程间属性`mustPlay`修改可见性的约定。显然，自己玩家的`mustPlay`属性的修改对于玩家的线程是可见的，玩家不继续玩，但是，在我们这样做的时候，另一个线程(`nextPlayer.mustPlay = true`)的属性`mustPlay`的修改对于受影响的线程不是自动可见的，所以两个玩家都将`mustPlay`设置为 false，我们的应用程序被阻塞(又名[死锁](http://en.wikipedia.org/wiki/Deadlock))。

为了解决这个问题，我们必须引入`volatile`修改器。[这个修饰符](http://www.javamex.com/tutorials/synchronization_volatile.shtml)向 JVM 表明该属性可能在线程间共享，因此读操作不能以任何方式缓存，在任何情况下都要访问主内存。此外，写操作必须自动完成，并立即可见。

那么，我们的代码应该这样修改:

```
public class Player implements Runnable {
    //...
    private volatile boolean mustPlay = false;
    //....
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这款应用终于可以在每次执行时以确定的方式工作。并发应用程序中线程可见性的一个主要问题是它会随机失败，因此，如果我们不知道要遵循的准则，调试这些问题会非常复杂。

### 版本 2:无限游戏

我们将让两个玩家永远玩下去，而不是玩固定的回合数。或者更好地说，直到主线程想要。为了实现这一点，我们必须使用 Java 提供的一个特性来中断线程。让我们看看我们的`Game`类是什么样子的:

```
public class Game {

    public static void main(String[] args) {

        Player player1 = new Player("ping");
        Player player2 = new Player("pong");

        player1.setNextPlayer(player2);
        player2.setNextPlayer(player1);

        System.out.println("Game starting...!");

        player1.setMustPlay(true);

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

我们看到，一旦两个播放器线程都被启动，主类会休眠一段时间(2ms)，一旦它醒来(实际上它会返回到“运行”状态)，它会请求两个播放器线程都结束。

我重复一遍，**请求**。当在线程上调用方法`interrupt`时，唯一发生的事情是在该线程中将标志`interrupted`设置为真。自己的线程有责任在它认为必要时采取行动，执行清理任务，并完成。但是如果线程决定什么都不做，继续执行，这是完全有效的(当然，即使这不是很正确)。知道这个标志的值的方法是使用方法`Thread.interrupted()`，所以我们的类`Player`将变成:

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
        while(!Thread.interrupted()) {  //Was I interrupted?

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

我们检查标志`interrupted`的状态，如果为真，则结束，而不是在每次迭代后检查我们是否已经用完了回合。就这么简单。

#### 版本 2b:关于中断的更多信息

在完成这个系列的第一篇文章之前，让我们深入研究一下中断一个线程的含义。

在某些场合，我们已经看到了类`Thread` ( `join`，`sleep`)中的一些方法，...)扔`InterruptedException`。当一个线程在调用这些方法(例如`anotherThread.join()`)后被中断并处于阻塞状态时，就会发生这种情况。在这种情况下，所发生的是，方法(`join`，接下来的例子)，在线程中将标志`interrupted`设置为 false，并抛出`InterruptedException`。我并不热衷于检查异常，但我认为这是极少数使用检查异常的情况之一。

让我们稍微修改一下类`Player`，这样一旦轮到它播放，它就会在打印文本之前休眠 1 毫秒:

```
public class Player implements Runnable {
    //...
    @Override
    public void run() {
        while(!Thread.interrupted()) {
            while (!mustPlay);

            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace(); //1
            }

            System.out.println(text);

            this.mustPlay = false;
            nextPlayer.mustPlay = true;

        }
    }
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们一直用同一个版本的`Game`类，很有可能游戏会一直持续下去。为什么？因为如果中断发生在播放器线程休眠的时候，方法`sleep`会在抛出异常之前吞下“interrupted”状态，鉴于我们只是在//1 中打印错误，循环不会检测到中断的状态，会永远继续下去。

这个问题的解决方案是将线程状态重新设置为中断:

```
@Override
public void run() {
    while(!Thread.interrupted()) {
        while (!mustPlay);

        try {
            Thread.sleep(1);
        } catch (InterruptedException e) {
            //I was interrupted, propagate the state 
            Thread.currentThread().interrupt();
        }

        System.out.println(text);

        this.mustPlay = false;
        nextPlayer.mustPlay = true;

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

总的来说，我们在处理`InterruptedException`的时候必须非常小心。另一个推荐的策略是重新抛出异常，这意味着修改我们的`run`方法中的逻辑，所以它将在其他地方被处理。我们应该**永远不要**接受例外。

有许多改进要执行，应用程序远远不是最佳的(那可怕的忙碌等待...).在下一篇文章中，我们将使用锁和条件来优化 CPU 的使用。

(所有代码都可以在这个 [GitHub 库](https://github.com/raulavila/concurrent-pingpong/)中找到)