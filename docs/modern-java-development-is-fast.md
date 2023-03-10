# 现代 Java 开发速度很快

> 原文：<https://dev.to/stealthmusic/modern-java-development-is-fast>

*原贴于[return.co.de](https://return.co.de/blog/articles/java-development-fast/)T3】*

Java 开发既快又有趣。信不信由你。

我在播客中听到了很多对 Java 的抨击，大部分是来自那些从未做过专业 Java 开发的开发人员，他们认为 Java 是蹩脚的 Java 浏览器插件或丑陋的跨平台桌面应用程序。

但这不是我要说的。我的意思是，现在你可以在很短的时间内用 Java 编写高质量的生产就绪代码。这里是我每天使用的工具和框架的概述。

下一部分是关于我的背景和我过去使用 Java 时遇到的问题。但是你可以跳过它，直接跳到《新的希望》。

# XML 地狱

几年前，我几乎完成了 Java。我用 Java ME 做了多年的开发(用 Java 1.3，一点也不好玩)，也用 Spring 2 做了 Java SE 的后端开发。此外，我还必须维护一些遗留系统的前端 struts。

```
<?xml version = "1.0" encoding = "UTF-8"?>

<beans xmlns = "http://www.springframework.org/schema/beans"
   xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation = "http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id = "helloWorld" class = "de.co.return.HelloWorld">
      <property name = "message" value = "Hello World!"/>
   </bean>
</beans> 
```

Enter fullscreen mode Exit fullscreen mode

一个真正的美人，不是吗？

我基本上厌倦了 Spring 2 和 Struts，因为它们的大量 XML 让重构变成了地狱，难以阅读。

所以我用普通的 Java SE 开始了下一个后端项目。我完全是测试驱动的。我非常快，使用 IntelliJ 生成所有的样板代码。我真的把 DI“事情”看得很重，过度设计了它。我几乎每个类都有一个接口，即使只有一个实现。我没有使用任何嘲讽框架，因为我也有过不好的经历——使用 EasyMock 甚至 PowerMock(不要使用它！)并通过在测试中实现接口来编写所有的模拟，如果我需要的话。我还有一个巨大的主引导类，它实际上创建了生产实例并进行实际的注入。那是一个 500 磅的怪物。

```
MyConfiguration config = loadConfiguration();
FirstClass firstClass = new FirstClass(config);
SecondClass secondClass = new SecondClass(config, "someParam");
ThirdClass thirdClass = new ThirdClass(firstClass, secondClass);

(...)

OneHundredTwelfthClass oneHundredTwelfthClass = (...) 
```

Enter fullscreen mode Exit fullscreen mode

由于非常好的测试覆盖率，这个应用程序在投入生产后的第一时间就开始工作了。唯一一个我没有覆盖的地方，因此有错误的是 SQL 查询，你猜对了，我是用普通的 JDBC 写的。

全部手动。这都是因为我在过去使用任何一种框架时都有过糟糕的经历。

我学到了很多东西，但我不想在我的余生都这样。所以我在寻找一些像 Erlang 后端开发人员这样的利基工作(我仍然喜欢那种语言)。事实上，我拒绝了一个相当不错的 Java 开发职位，因为他们在面试中告诉我他们在做 Spring。最后，我被一家以 Java 为背景的大公司录用了。

# 新的希望

我是一个新团队的成员，这个团队可以选择我们想要使用的技术。我们尝试了 plain Java、Camel、Java EE、vert.x，然后是 Spring 4 和 Spring Boot 以及其他一些让我大开眼界的便利设施。

## 春天——神奇的事情正在发生

### 弹簧芯

有了 [Spring](https://spring.io/) core，我显然能够用它的`@Autowire`魔力替换这样一个丑陋的 bootstrap 类，最重要的是，不需要任何 XML:

```
@Service
public class FooService {

    private final FooRepository repository;

    @Autowired
    public FooService(FooRepository repository) {
        this.repository = repository
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是 Spring 的核心:依赖注入。实际上是管家把你需要的所有东西都放在一个银盘子里。你只需要呼唤它。剧透:这在龙目岛变得更好，见下文。

除了需要多个实现的地方，我实际上完全放弃了“每个类的接口”模式。

### Spring Web MVC -让我休息一下

我不需要任何 web 服务器设置或配置，我可以很容易地编写一个`@Controller`来返回一个被序列化为 JSON 的 Java 类实例，不需要任何代码。

```
@Controller
@RequestMapping("/appointments")
public class AppointmentsController {

    private final AppointmentBook appointmentBook;

    @Autowired
    public AppointmentsController(AppointmentBook appointmentBook) {
        this.appointmentBook = appointmentBook;
    }

    @GetMapping
    public Map<String, Appointment> get() {
        return appointmentBook.getAppointmentsForToday();
    }

    @GetMapping("/{day}")
    public Map<String, Appointment> getForDay(@PathVariable @DateTimeFormat(iso=ISO.DATE) Date day, Model model) {
        return appointmentBook.getAppointmentsForDay(day);
    }

    @PostMapping
    public String add(@Valid Appointment appointment) {
        appointmentBook.addAppointment(appointment);
        return "redirect:/appointments";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码发布了一个新的 rest 端点`/appointments`和两个`GET`端点来检索所有约会和一天中的所有约会，还发布了一个`POST`端点来创建一个新约会。类`Appointment`实际上可能是一个被自动序列化为 JSON 的持久化实体。您将获得正确解码的路径变量，并将其输入到类型方法参数中。不需要额外的代码。

此处见[供参考。](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html)

### 春季数据

对于非常简单的 CRUD 接口，我可以使用 [Spring 数据](http://projects.spring.io/spring-data/)库甚至 [Spring 数据 REST](http://projects.spring.io/spring-data-rest/) 来直接为我的实体创建一个 REST 接口。

```
public interface OrderRepository extends CrudRepository<Order, Long> { } 
```

Enter fullscreen mode Exit fullscreen mode

> 对于这个存储库，Spring Data REST 在`/orders`公开了一个集合资源。该路径源自被管理的域类的非大写、复数、简单的类名。它还在 URI 模板`/orders/{id}`下为存储库管理的每个项目公开了一个项目资源。*

*见[此处](http://docs.spring.io/spring-data/rest/docs/current/reference/html/#repository-resources)供参考。

又一次，很多魔法，但是很酷。在扫描 Spring 托管类的类路径时，它会找到接口并动态创建存储库的实现，您实际上可以用它从数据库中获取实体(以及所有的 CRUD 操作),此外，它还会创建一个 REST 控制器，为该存储库的请求提供服务。

### 春季综合测试

使用 [Mockito](http://site.mockito.org/) 在测试中模仿对象的工作就像一个魔咒， [AssertJ](http://joel-costigliola.github.io/assertj/) 用于测试断言，甚至使用`@SpringBootTest`与真正的 HTTP 模仿服务器和内存数据库一起运行完整的应用程序测试，而无需任何外部资源。

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class MyTests {

    @MockBean
    private RemoteService remoteService;

    @Autowired
    private MyService service;

    @Autowired
    private MockRestServiceServer remoteServer;

    @Test
    public void exampleTest() {
        this.remoteServer.expect(requestTo("/greet/details"))
                .andRespond(withSuccess("hello", MediaType.TEXT_PLAIN));
        given(this.remoteService.someCall()).willReturn("world");

        String greeting = this.service.callRestService();

        assertThat(greeting).isEqualTo("hello world");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这段代码中发生了很多事情。首先，启动一个名为`MockRestServiceServer`的实际 Web 服务器，一旦请求`/greet/details`它就会响应。然后你的应用程序的服务`RemoteService`被模拟，它被假设为被测试的类`MyService`调用。当方法`someCall`被调用时，它将返回“世界”。最后，使用 [AssertJ](http://joel-costigliola.github.io/assertj/) 完成验证。

### 春天的安全

唯一的缺点是使用了 [Spring Security](https://projects.spring.io/spring-security/) ，它进行认证和授权。它提供了各种现成的实现以及编写自己的`AuthenticationProvider`的选项。不幸的是，依我看，它的设计很糟糕(从可用性的角度来看)，文档记录更糟糕。一旦你开始对它进行一点点的定制，它永远不会像你期望的那样工作，你最终会花费数小时甚至数天的时间通过试错来修复它。简单来说，春安大部分时间都很烂。我希望很快会有一些检修。

### Spring Boot

> Spring Boot 使得创建独立的、生产级的基于 Spring 的应用程序变得容易，你可以“直接运行”。我们对 Spring 平台和第三方库有自己的看法，所以你可以毫不费力地开始。大多数 Spring Boot 应用需要很少的弹簧配置。

*   此处见[供参考。](https://projects.spring.io/spring-boot/)

Spring boot 将所有的位和件放在一起，应用一个合理的默认配置和您的好去。

如果你想要一个非常有趣和非常好的现场编码示例，我强烈推荐 Josh Long 的视频[、Spring Boot 的“Bootiful”应用程序](https://www.youtube.com/watch?v=HCyYEVRZISk) -哇，太棒了！

## JPA / Hibernate

几年前我使用 Hibernate 时，也有我前面描述过的 XML 配置地狱。但是最近的 JPA，你只要做

```
@Entity
public class Customer {

  @Id
  @GeneratedValue(strategy = GenerationType.AUTO)
  private Long id;

  private String firstname;
  private String lastname;

  // … methods omitted or replaced by Lombok
} 
```

Enter fullscreen mode Exit fullscreen mode

此处见[供参考。](https://spring.io/blog/2011/02/10/getting-started-with-spring-data-jpa/)

与 Spring 数据仓库一起，您可以轻松地访问您的实体。这意味着，您只是简单地描述您正在处理的数据，而不必处理如何获得它们。

最好的事情是，你不需要写任何 SQL。Hibernate 非常擅长创建优化的查询。它将从数据库中提取数据，并将其映射到`@Entitiy`类上。如果你有一些难看的表或列的名字，你可以简单地注释这些字段。上面的类需要一个包含第 D 列`,`第一名`and`姓的表`CUSTOMER`。如果您需要访问一个遗留数据库，您可以定制您想要的一切，例如..

## 龙目岛——没有更多的吸杂者

当 Java 8 问世时，我对这种古老语言的发展印象深刻。然而，从我的角度来看，缺少了两件事:模式匹配和一种去除 getters 和 setters 的方法。

我们仍然没有模式匹配(比如在 Erlang 中)，但是——这真的很神奇——我们可以通过使用一个库来去掉 getters 和 setters。很多框架依赖于 getters 和 setter、Hibernate、Jackson 和 Spring 本身的存在。

当我第一次看到 [Lombok](https://projectlombok.org/) 的时候，我想这一定是某种代码生成魔法或者——甚至更糟——某种运行时反射技巧。另外，这个网站看起来像是 90 年代的。但是当我检查后，发现我完全错了。他们使用了一个`javac`特性，编译时注释。因此，在编译期间，Lombok 的代码被执行并生成额外的字节码，这不会产生额外的运行时开销(与手动编写 getters 和 setters 相比),也不会产生中间 Java 代码。

记住上面的`FooService`。使用`@AllArgsContructor`，Lombok 为所有成员生成一个构造函数。

`` Java
@ Service
@ allargs constructor
@ Sl4j
公共类 FooService {`

```
private final FooRepository repository;
private final MyProperties properties;

public getUri(String path) {
    log.debug("Building uri from base Url {} and path {}", properties.getSomeUrl(), path)
    return properties.getSomeUrl() + path;
} 
```

Enter fullscreen mode Exit fullscreen mode

}
`

 `使用`@Sl4j`，我们得到了一个免费的日志记录器(而不是做`Logger.getLogger(...)`)，还有更多好东西，比如生成`@ToString`和`@EqualsAndHashcode`。

有了[弹簧 4.3](https://spring.io/blog/2016/03/04/core-container-refinements-in-spring-framework-4-3) ，你甚至可以完全放下`@Autowire`。

`` Java
@ Configuration
@ Configuration properties(prefix = " my . app ")
@ Getter
@ Setter
公共类 MyProperties {`

```
private String someUrl;
private Integer someTimeout; 
```

Enter fullscreen mode Exit fullscreen mode

}
`

 `上面的代码可以用作 spring 属性来配置应用程序。你可能会说这个代码片段的注释比代码多，这是真的。但我还是更喜欢它，而不是自己编写所有的锅炉板代码。

它允许您编写如下所示的 YAML 文件，并将其放在 jar 旁边以更改配置:

``yaml
my.app:
some-url: http://foo.bar
some-timeout: 5000`` 

 ``是的，它会自动允许你使用正确的命名，比如`some-timeout`仍然知道如何映射到`someTimeout`。您将免费获得应用程序的类型安全配置。你甚至可以把`@Validated`和`@Max(1000)`加在一起。

## [胃](#maven)

Maven 是*库管理工具。好吧，Maven 是一个很大的 XML，好吧，但是它工作起来很有魅力。我和 Ant 合作过，得到同样的结果是如此痛苦。*

我期待着在一个 Android 项目中使用 Gradle，哦，我的天哪，这真的很糟糕。您需要编写可以做任何事情的可执行脚本(并且确实做了任何事情，尤其是在您不想做的情况下)。你永远不知道你调用的是一个方法还是它的配置。我最终花了几个小时甚至几天的时间来打印调试我的构建脚本，直到它达到了我想要的效果。

正因为有了 XML，Maven 才得到了很好的定义，而且你几乎不能以非预期的方式破坏东西。Maven 的库依赖做得对吗，特别是当你把它和其他加载代码、构建代码的“包管理器”( XCode、Erlang)或者像 NPM 和鲍尔这样的垃圾相比的时候。Maven 只是构建过程的一个组成部分，不需要像在`pip` (Python)中那样预先下载一个库，你总是会得到你正在构建的那个项目的那个库的精确版本。

## Java

如上所述，Java 8 附带了一些功能特性，Lambdas 非常方便，尽管它们不一定是功能性的。流一开始看起来很棒，但是大多数时候你会得到没有人(甚至一周后的你)能读懂的意大利面条代码。大多数情况下，最好使用一个旧的 for 循环。

但一般来说，它会尽可能多地编写功能代码(从 Java 的第一个版本开始就有可能了)。这意味着，一个方法应该总是返回值(不是 void ),并且应该总是为相同的参数返回相同的值。这需要

*   不使用系统调用(如`currentTimeMillis()`)
*   不与文件、数据库等交互。
*   使用类变量

显然，如果你 100%那样做，你的应用程序就完全没有价值，因为你既没有办法控制它，也没有办法得到任何输出。因此，我试图保持我的核心业务逻辑尽可能干净，并尽可能将副作用转移到外部。这对测试有很大好处。

## [这里](#ide)

我过去实际上翻译了很多 IDE，从 QBasic、Turbo Pascal、Visual Studio、Eclipse、NetBeans、XCode 甚至`vim`开始。但是我爱上了 IntelliJ IDEA(T2 ),这是我用过的最好的 IDE。它开始变得漂亮和快速，支持非常好和直观的键盘快捷键，你可以自动输入和自动过滤所有菜单，甚至是上下文菜单。它允许使用强大的重构并为您生成代码。I 支持非常好的调试，以及在线显示代码覆盖率。

你现在可能对你的 IDE 很满意，但是你至少应该尝试过 IDEA 一次，并且看一个关于这些特性的视频。

尤其是当我在一个应用项目中使用 XCode 时，与 IntelliJ 相比，我感觉像是在石器时代。不幸的是，即使是 JetBrains AppCode 也仍然需要 XCode 来处理一些事情。

## 这是生态系统

所以，这不是语言的问题。这与 IDE 或构建工具无关。正是所有的东西结合在一起，使得 Java 开发变得快速而有趣。