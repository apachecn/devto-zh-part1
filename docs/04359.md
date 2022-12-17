# 队列和有状态服务

> 原文：<https://dev.to/cr0wst/queues-and-stateful-services-4c72>

*编辑:我原本以为 Laravel 的服务容器默认绑定为 singletons。情况似乎并非如此。我在适当的地方做了更新。我还加了一节静力学！*

编辑:当我说 singleton 时，我特指一个被实例化过一次，然后被服务容器重用的类。我指的不是使用静态创建的单例模式。

Laravel 提供队列来推迟发送电子邮件和批处理等耗时的任务。要了解如何使用队列，可以参考 Laravel 文档:Queues 。

## 队列工作者

Laravel 提供了一个队列工作器，可以用来处理添加到队列中的新作业。

您可能没有意识到，在排队作业中使用有状态服务可能会遇到问题。来自 [Laravel 文档:队列](https://laravel.com/docs/5.5/queues):

> 请记住，队列工作器是长寿命的进程，将启动的应用程序状态存储在内存中。因此，他们不会注意到你的代码库在启动后的变化。

虽然这很好地通知了用户，如果他们对应用程序进行任何更改，他们应该重新启动队列工作器，但这并没有指出将应用程序保持在引导状态意味着每个作业都将使用相同的状态。如果您的服务有任何类型的状态，并且您将它们作为单例绑定，这可能会导致问题。

首先，让我们看看是什么导致应用程序的启动状态存储在内存中。

### 队列工作者命令

`php artisan queue:work`命令启动队列工作器，并将继续运行，直到您手动取消该进程或关闭您的终端。

`WorkCommand`有一个注入的`Worker`对象，用于处理作业。此时，应用程序已经启动。

### 职工

工作线程本身可以被告知运行一次，在这种情况下，它将处理单个作业，或者在守护程序模式下，它将继续运行，直到被取消，除非队列被暂停，或者达到了内存限制。

守护模式被定义为`Illuminate\Queue\Worker`中的`daemon`函数。在这个`daemon`函数中我们可以看到:

```
public function daemon($connectionName, $queue, WorkerOptions $options)
{
    $this->listenForSignals();

    $lastRestart = $this->getTimestampOfLastRestart();

    while (true) {
    // ... stuff
    }
} 
```

`while`循环正是将应用程序引导到内存中的原因。它将保持线程处理，直到它被告知以编程方式取消，被用户手动取消，或者遇到某种需要它取消的情况。

## 我为什么需要担心这个？

Laravel 为我们提供的另一个东西是它的服务容器，它允许依赖项被注入，而不是硬编码到需要它们的类中。依赖注入的另一大好处是，依赖可以被实例化一次，然后在每个消费者之间共享。您可以在[Laravel Documentation:Service Container](https://laravel.com/docs/5.5/container)找到关于服务容器的更多信息。

无论您的服务是否已经绑定到容器，或者它们是通过反射发现的，容器都会跟踪已经实例化的依赖项。而且，如果您将它们绑定为单例，它将为您的每个作业提供相同的实例。

通常，在 web 应用程序中处理请求的过程中，应用程序容器会为每个通过的请求启动。但是，我们已经看到，在队列工作器运行的整个过程中，应用程序都驻留在内存中。这意味着对于每个有依赖关系的作业，相同的对象将被注入到每个作业中。这就是为什么在处理单例时，无状态服务如此重要。

### 无状态 vs 有状态服务

我将在以后的文章中更详细地介绍，但是让我们看一个小例子。不要担心服务试图完成什么。

#### 有状态服务

```
class ServiceA()
{
    private $someDependency;

    private $listOfNames;

    public function __construct(SomeDependency $someDependency)
    {
        $this->someDependency = $someDependency;
    }

    public function getListOfNames()
    {
        // Lazy Loading Technique
        if (empty($this->listOfNames)) {
            $this->listOfNames = SomeHelper::buildListOfNames();
        }

        return $this->listOfNames;
    }
} 
```

这个服务对`SomeDependency`有一个单独的依赖，并且有一个返回`$listOfNames`数组中的值的方法。注意，数组是*延迟加载的*。数组的值是通过调用`SomeHelper::buildListOfNames()`来设置的，并且只有在第一次调用该方法时才会被设置。

拥有`$listOfNames`变量给出了`ServiceA`状态或有状态性。

#### 无状态服务

```
class ServiceB()
{
    private $someDependency;

    public function __construct(SomeDependency $someDependency)
    {
        $this->someDependency = $someDependency;
    }

    public function getListOfNames()
    {
        return SomeHelper::buildListOfNames();
    }
} 
```

此服务的状态已被删除。它总是返回由另一个类提供的新的名字列表。

注意，只要`SomeDependency`也是无状态的，对`$someDependency`的依赖就不提供状态。

### 有状态服务的后果

考虑具有以下处理方法的作业:

```
public function handle(ServiceA $service)
{
    $listOfNames = $service->getListOfNames();
    doSomething($listOfNames);
} 
```

当这个任务被执行时，`ServiceA`类将被注入。将调用`getListOfNames()`方法。第一次执行该作业时，可能是当队列第一次开始工作时，`ServiceA`上的`$listOfNames`数组将被初始化。

然而，对于*,运行*的每个作业的`$listOfNames`数组将始终保持不变，因为`ServiceA`的实例只会在队列工作器第一次启动时创建，因为我们是作为单例绑定的。

如果`SomeHelper::buildListOfNames()`获取数据的数据库中发生了变化，那么新的列表将不会构建，直到 worker 重新启动。

下次开始编写有状态服务时，请记住这一点，尤其是在处理队列时。

## 但是等等，我没有用单线，我安全吗？

默认情况下，Laravel 会从容器中发出一个新的实例，除非你明确要求单例绑定。然而，这并不意味着你是安全的！还有另一种方法让您的服务拥有状态，考虑下面对`ServiceA` :
的修改

```
class ServiceA()
{
    private $someDependency;

    public static $listOfNames = [];

    public function __construct(SomeDependency $someDependency)
    {
        $this->someDependency = $someDependency;
    }

    public static function getListOfNames()
    {
        // Lazy Loading Technique
        if (empty(self::listOfNames)) {
            self::listOfNames = SomeHelper::buildListOfNames();
        }

        return self::listOfNames;
    }
} 
```

注意`$listOfNames`和`getListOfNames`是如何静态的。这意味着方法和变量都不存在于类实例中，它们不是*实例变量*。相反，PHP(基于我对 Java 的一个假设)将变量限定在类中，而不是实例中。这意味着引用该变量的任何代码都将共享同一个变量引用。有些人把静态称为全局变量。

在这个例子中，第一次调用`getListOfNames`时，静态变量`$listOfNames`将被填充。对这个方法的任何后续调用都将引用同一个变量*，并且不允许它被再次加载。由于工作线程保持 PHP 线程打开，PHP 无法像处理 HTTP 请求那样清除内存。这意味着该变量被保存到每个运行的作业中，直到线程被关闭并且内存被清空。*

这是一个类似于单例的问题，因为这就是静态的本质。在执行期间保持的单个引用。

另一件要记住的事！:)