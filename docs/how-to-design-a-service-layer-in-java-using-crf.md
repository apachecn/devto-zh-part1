# 如何使用 CRF 在 Java 中设计服务层

> 原文：<https://dev.to/eduardsi/how-to-design-a-service-layer-in-java-using-crf>

## [T1】简介](#intro)

作为我的 [Effective Java](http://devchampions.com/training/java/) 课程的一部分，我已经展示了如何使用我称之为 **CRF** 的方法为 Java 应用程序设计一个简单而灵活的服务层。大多数与会者认为这个想法新鲜而令人兴奋，所以我决定与更多的观众分享。

## 服务层？！

让我们简单讨论一下服务层应该做什么。服务层的目的是:

1)定义一个**用例**。换句话说，当你看服务层时，它应该回答这个问题——系统**能够做什么**。

2)定义一个**合同**。换句话说，当您查看特定的用例/服务时，应该清楚需要什么数据输入，什么是可能的输出，以及抛出了什么异常。

3)定义一个**事务边界**。一个用例要么成功，要么失败。如果成功，关联的数据将被持久化。如果失败，更改将被回滚。这很重要，因为不同的用户可以并行运行许多用例。比方说，如果我(Eduards)和你(读者)试图获得同一个飞机座位，有两个用例(SecureASeat)并行运行。为了避免重复预订([您好美联航](http://www.express.co.uk/travel/articles/790214/United-Airlines-passenger-dragged-overbooked-flight))，如果您先预订了座位，我的预订尝试必须失败，与我的预订尝试相关的所有更改(如果有)必须回滚。

关于服务层还有很多需要了解的，但这足以奠定基础。

现在，让我们看看构建服务层的最常见方式。

## 流程服务层(头巾风格)

服务层最常见的风格是使用过程。

这里有一个允许篡改会员权限的服务:

```
interface MemberPermissionService {
  void grantPermission(String memberId, String permissionId);
  void revokePermission(String memberId, String permissionId);
  PermissionsDto listPermissions(String memberId);
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们[编程到一个接口](https://en.wikipedia.org/wiki/Interface-based_programming)，这里也有实现:

```
@Service
class MemberPermissionServiceImpl implements MemberPermissionService {
  @Override
  public void grantPermission(String memberId, String permissionId) { ... }

  @Override
  void revokePermission(String memberId, String permissionId) { ... }

  @Override
  public PermissionsDto listPermissions(String memberId) { ... }
} 
```

Enter fullscreen mode Exit fullscreen mode

有时候根本不用接口。不管是不是，都不能改变这个想法。

前面提到的风格是有效的，但是，作为每个设计决策，它也有一系列的缺点。

### 不透明

假设我们正在查看服务所在的包:

```
net.blog.sizovs.services.MemberPermissionService
net.blog.sizovs.services.ModerationService
net.blog.sizovs.services.MemberService 
```

Enter fullscreen mode Exit fullscreen mode

查看软件包，我们无法对系统正在做什么有一个坚实的了解。我们看到系统可以用会员权限做*一些事情*，用适度做*一些事情*，用会员做*一些事情*。

为了理解系统到底能做什么，我们必须打开服务，看看它们包含什么过程。换句话说，服务层[不会对系统能够做什么大喊](https://8thlight.com/blog/uncle-bob/2011/09/30/Screaming-Architecture.html)。

### 肥胖

所有与成员权限相关的用例都成为`MemberPermissionService`接口的方法。用例越多，方法就越多。

一个接口发布的方法越多，传入耦合就越高。

一个接口承诺做的越多，实现就越丰富(`MemberPermissionServiceImpl`)。

很有可能，`MemberPermissionServiceImpl`也将遭受高传出耦合——它将有许多*传出*依赖。

大量的依赖性降低了可测试性。

就像滚落山下的雪球。人们只是不断地向同一个界面添加用例，因为界面已经成为合适用例的容器。听起来很熟悉？

### 永远封锁

在我们的例子中，服务调用是阻塞的。技术上，我们可以让一些服务返回`CompletableFuture`或者`Observable`。

很可能，我们最终会为每个执行模型提供一个单独的方法。像这样:

```
interface MemberPermissionService {
  PermissionsDto listPermissions(String memberId);
  CompletableFuture<PermissionsDto> listPermissionAsync(String memberId);
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

一些团队更喜欢创建一个单独的界面:

```
interface MemberPermissionServiceAsync {
  CompletableFuture<PermissionsDto> listPermissionAsync(String memberId);
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这与其说是观念上的差异，不如说是品味的问题。

记住规则——**封装变化的**？服务本质上是相同的，但是执行模型不同。我们能做得更好吗？我们当然可以。请继续阅读。

> **补充说明**:如果我们让所有服务从第一天开始返回`CompletableFuture`以支持阻塞和非阻塞执行，会怎么样？是的，我们可以！然而，还有其他的执行模型，比如“发射并忘记”，当你将工作推入消息队列，并且工作在后台运行。`CompletableFuture`在这里帮不上忙。

### 没有单一入口点

由于服务没有单一的入口点，因此记录所有的服务调用、限制它们、实现统一的事务管理策略或单一的验证点变得非常困难。

该问题的传统解决方案是创建一堆注释，并通过 AOP 对代码进行检测:

```
interface MemberPermissionService {
  @WrapInTransaction
  @Log
  @Validate
  @Throttle(max = 100)
  void grantPermission(String memberId, String permissionId);
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

该解决方案可以达到目的，但是有很大的局限性:

*   如果我们想要基于一个动态参数而不是硬编码的值`100`来限制调用，该怎么办？对于注释，你不能这样做。
*   我们如何确保`@Throttle`注释处理器在`@WrapInTransaction`之前运行，以避免创建不必要的事务？很有可能，我们将不得不在`@WrapInTransaction`和`@Throttle`处理器之间引入不可见的依赖。不太好。

一定有更好的办法。请继续阅读。

## 满足命令/反应/漏斗(CRF)

CRF 是一种面向[命令的](https://martinfowler.com/bliki/CommandOrientedInterface.html)服务层设计方法，由 3 个主要组件组成——**命令**、**反应**和**漏斗**。

### 一个命令

一个命令定义了一个用例，一个用户可以执行的动作。例如-`GrantPermission`、`RevokePermission`和`ListPermissions`将是我们的命令。

从技术上讲，命令实现了一个标记接口。由于命令可以返回*响应*，`R`泛型为命令定义了一个响应类型:

```
interface Command<T extends Command.R> {
  interface R {
    class Void implements R {
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

### 一种反应

反应定义了系统对特定命令的反应。反应是我们编写服务代码的地方。每一个命令都必须有一个反应。`GrantPermissionReaction`、`RevokePermissionReaction`和`ListPermissionsReaction`是我们的反应。

从技术上讲，反应源自单一界面。该接口是通用的，定义了命令类型和响应类型。

我们还添加了一个默认方法`commandType()`，它通过泛型解析匹配的命令类型。 [TypeToken](https://google.github.io/guava/releases/19.0/api/docs/com/google/common/reflect/TypeToken.html) 来自番石榴库:

```
interface Reaction<C extends Command<R>, R extends Command.R> {
  R react(C $);

  default TypeToken<C> commandType() {
    return new TypeToken<C>(getClass()) {};
  }    
} 
```

Enter fullscreen mode Exit fullscreen mode

### 一个漏斗

所有的命令都通过一个漏斗。漏斗定义了**命令将如何**执行——阻塞、非阻塞或发射或遗忘。当然，它也包含了运行所有命令的代码(例如日志)。

最简单的系统有一个单一的同步漏斗。

以下是不同漏斗的界面示例。实现将在后面进行:

#### 同步漏斗

```
interface Now {
  public <R extends Command.R, C extends Command<R>> R execute(C command);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 异步漏斗

```
interface Future {
  public <R extends Command.R, C extends Command<R>> CompletableFuture<R> schedule(C command);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 反应漏斗

```
interface Rx {
  public <R extends Command.R, C extends Command<R>>  Observable<R> observe(C command);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 耐用的一次性漏斗

```
interface Durable {
  <C extends Command> void enqueue(C command);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 把所有的东西放在一起

让我们从一个不返回结果的`GrantPermission`命令开始:

```
class GrantPermission implements Command<Command.R.Void> {

  private final String memberId, permissionId;

  public GrantPermission(String memberId, String permissionId) { ... }

  public String memberId() {
    return memberId;
  }

  public String permissionId() {
    return permissionId;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们实现一个反应。`Members`和`Permission`是我们领域模型的一部分。反应是一个 Spring 管理的组件，所以`Permissions`和`Members`依赖项被注入。

```
@Component
class GrantPermissionReaction implements Reaction<GrantPermission, Command.R.Void> {

  private final Members members;
  private final Permissions permissions;

  public GrantPermissionReaction(Permissions permissions, Members members) { 
    ... 
  }

  @Override
  public Command.R.Void react(GrantPermissionCommand $) {
    Member member = members.byId($.memberId());
    Permission permission = permissions.byId($.permissionId());
    member.grantPermission(permission);
    return new Command.R.Void();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还必须实现同步漏斗。当命令通过漏斗(称为`Now`)传递时，漏斗将命令路由到适当的反应。从 Spring 应用程序上下文解析反应:

```
interface Now {
  public <R extends Command.R, C extends Command<R>> R execute(C command);
}

@Component
class SimpleNow implements Now {

  private final ListableBeanFactory beanFactory;

  public Now(ListableBeanFactory beanFactory) {
    this.beanFactory = beanFactory;
  }

  public <R extends Command.R, C extends Command<R>> R execute(C command) {
    Class<? extends Command> commandType = command.getClass();
    Reaction<C, R> reaction = reactions()
                .stream()
                .filter(r -> r.commandType().isSupertypeOf(commandType))
                .findFirst()
                .orElseThrow(() -> new IllegalStateException("No reaction found for " + commandType));

    return reaction.react(command);
  }

  private Collection<Reaction> reactions() {
    return beanFactory.getBeansOfType(Reaction.class).values();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，前面提到的例子要求 Spring 在每次命令通过漏斗时提供所有的反应。这可能会影响性能。让我们添加[咖啡因](https://github.com/ben-manes/caffeine)缓存，以确保漏斗记住哪个反应对应于一个命令——

```
@Component
class SimpleNow implements Now {

  private final LoadingCache<Type, Reaction> reactions;

  public Now(ListableBeanFactory beanFactory) {
    this.reactions = Caffeine.newBuilder()
            .build(commandType -> reactions(beanFactory)
             .stream()
             .filter(reaction -> reaction.commandType().isSupertypeOf(commandType))
             .findFirst()
             .orElseThrow(() -> new IllegalStateException("No reaction found for " + commandType);
  }

  public <R extends Command.R, C extends Command<R>> R execute(C command) {
    Reaction reaction = reactions.get(command.getClass());
    return (R) reaction.react(command);
  }

  private Collection<Reaction> reactions(ListableBeanFactory beanFactory) {
    return beanFactory.getBeansOfType(Reaction.class).values();
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

答对了。由于我们已经将所有组件粘合在一起，现在我们可以看看客户端代码:

```
class Application {

  @Autowired
  Now now;

  public void run(String memberId, String permissionId) {
    GrantPermission grantPermission = new GrantPermission(memberId, permissionId);
    now.execute(grantPermission);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

...如果使用异步漏斗，它将看起来像这样-

```
class Application {

  @Autowired
  Future future;

  public void run(String memberId, String permissionId) {
    GrantPermission grantPermission = new GrantPermission(memberId, permissionId);
    future.schedule(grantPermission)
      .thenAccept(System.out::println)
      .thenAccept(...);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

...如果使用 RxJava 漏斗，看起来会是这样的——

```
class Application {

  @Autowired
  Rx rx;

  public void run(String memberId, String permissionId) {
    GrantPermission grantPermission = new GrantPermission(memberId, permissionId);   
    rx.observe(grantPermission).subscribe(System.out::println);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

很神奇，不是吗？，客户可以根据其要求选择合适的漏斗。

### 命令上类固醇

让我们给`Command`接口添加几个默认方法，以使客户端代码更加性感:

```
interface Command<T extends Command.R> {

  default T execute(Now now) {
    return now.execute(this);
  }

  default CompletableFuture<T> schedule(Future future) {
    return future.schedule(this);
  }

  default Observable<T> observe(Rx rx) {
    return rx.observe(this);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以以一种自然的方式调用命令:

```
grantPermission.execute(now);

grantPermission.schedule(future);

grantPermission.observe(rx); 
```

Enter fullscreen mode Exit fullscreen mode

## 命令作文

命令很棒，因为它们可以被组合(修饰)。为了演示这个想法，让我们实现*重试*机制。如果命令因异常而失败，系统将在出现异常之前重试几次。

我们创建了一个特殊的`Try`命令，它可以包装任何命令:

```
class Try<C extends Command<R>, R extends Command.R> implements Command<R> {

  private final C origin;
  private long times = 3;

  public Try(C origin) {
    this.origin = origin;
  }

  public Command<R> origin() {
    return origin;
  }

  public Try<C, R> times(long times) {
    this.times = times;
    return this;
  }

  public long times() {
    return times;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创造一个反应。为了实现重试，我们使用了 [SimpleRetryPolicy](http://docs.spring.io/spring-retry/docs/1.1.2.RELEASE/apidocs/org/springframework/retry/policy/SimpleRetryPolicy.html) 类，它是 [Sprint Retry](https://github.com/spring-projects/spring-retry) 项目的一部分。

```
class TryReaction<C extends Command<R>, R extends Command.R>
                                          implements Reaction<Try<C, R>, R> {
  private final Router router;

  @Override
  public R react(Try<R> $) {
    SimpleRetryPolicy retryPolicy = new SimpleRetryPolicy();
    retryPolicy.setMaxAttempts($.times());
    RetryTemplate template = new RetryTemplate();
    template.setRetryPolicy(retryPolicy);
        return template.execute(context -> {
            Command<R> origin = $.origin();
            Reaction<Command<R>, R> reaction = router.route(origin);
            return reaction.react(origin);
        });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`TryReaction`依赖于一个叫做`Router`的新类——一个封装命令路由逻辑到反应的类，它以前是`SimpleNow`漏斗的一部分:

```
@Component
class Router {

  private final LoadingCache<Type, Reaction> reactions;

  public Rounter(ListableBeanFactory beanFactory) {
    reactions = Caffeine.newBuilder()
                  .build(commandType -> reactions(beanFactory)
                      .stream()
                      .filter(reaction -> reaction.commandType().isSupertypeOf(commandType))
                      .findFirst()
                      .orElseThrow(() -> new IllegalStateException("No reaction found for " + commandType)));
    }

  private Collection<Reaction> reactions(ListableBeanFactory beanFactory) {
    return beanFactory.getBeansOfType(Reaction.class).values();
  }

  public <C extends Command<R>, R extends Command.R> Reaction<C, R> route(C command) {
    return reactions.get(command.getClass());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是客户端代码的样子:

```
new Try<>(
  new GrantPermission(...))
      .times(5)
      .execute(now); 
```

Enter fullscreen mode Exit fullscreen mode

我知道你很惊讶。我也是。

## 漏点类固醇

让我们丰富一下我们的漏斗，以便在一个事务中记录和执行所有命令。

让我们改变一下我们的`SimpleNow`漏斗。我们将称它为`PipedNow`以纪念尼克斯管道:

```
interface Now {
  public <R extends Command.R, C extends Command<R>> R execute(C command);
}

@Component
class PipedNow implements Now {
  public <R extends Command.R, C extends Command<R>> R execute(C command) {
    Now pipe = new Loggable(
                     new Transactional(
                            new Reacting()));
    return pipe.execute(command);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Loggable`、`Transactional`都只是普普通通的老装修工。没有魔法:

```
class Loggable implements Now {

  private final Logger log = LoggerFactory.getLogger(Loggable.class);

  private final Now origin;

  public Loggable(Now origin) {
    this.origin = origin;
  }

  @Override
  public <R extends Command.R, C extends Command<R>> R execute(C command) {
    log.info(">>> {}", command.toString());
    R response = origin.execute(command);
    log.info("<<< {}", response.toString());
    return response;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class Transactional implements Now {

  private final Now origin;
  private final TransactionTemplate tx;

  public Transactional(Now origin) {
    this.origin = origin;
    this.tx = new TransactionTemplate(txManager);
  }

  @Override
  public <R extends Command.R, C extends Command<R>> R execute(C command) {
    return tx.execute(txStatus -> origin.execute(command));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
class Reacting implements Now {

  @Override
  public <R extends Command.R, C extends Command<R>> R execute(C command) {
    Reaction<C, R> reaction = router.route(command);
    return reaction.react(command);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

添加新的装饰者是非常容易的。它最好的部分是装饰者的执行顺序在代码中是显式可见的。没有魔法。

## 好了，我们得到了什么好处？

*   每个用例都写在一个单独的类中——更少的依赖性，可测试性得到提高。
*   每个用例都经过一个单一的入口点——一个*漏斗*。这对于集中验证、日志记录、事务管理、节流非常方便，提供了[关联标识符](http://www.enterpriseintegrationpatterns.com/patterns/messaging/CorrelationIdentifier.html)。没有 AOP 魔法。
*   客户端可以通过切换漏斗来选择命令执行模式——同步、异步、反应式、计划式。您甚至可以创建一个模拟漏斗进行测试。
*   服务层强调应用程序能够做什么:

```
net.blog.sizovs.services.GrantPermission

net.blog.sizovs.services.RevokePermission

net.blog.sizovs.services.ListPermissions 
```

Enter fullscreen mode Exit fullscreen mode

*   您可以向现有命令和响应类型添加新参数，而无需更改服务签名。
*   我们几乎免费获得了指挥、反应和漏斗方面的灵活性。随着系统的发展，我们可以向服务层添加更多的功能，而无需进行大的更改。

## 最后的话

CRF 是过程服务层的一个很好的替代方案。我在我构建的大多数企业应用程序中使用它，事实证明它非常有效。

你可以用 CRF 做更多的事情。例如，可以通过根据命令名和参数合成的键保存结果来缓存命令响应。或者，您可以拥有同一个反应的多个版本，并根据上下文选择执行哪个版本。太神奇了！

工作代码示例可以在我的 GitHub 上找到[。如果你有兴趣学习更多关于这种方法的知识——加入我的](https://github.com/eduardsi/crf-demo)[有效 Java](http://devchampions.com/training/java/) 课程。