# 在 Java 中控制时间

> 原文：<https://dev.to/rnowif/controlling-the-time-in-java-43kh>

时间是个棘手的东西，它总是在变。例如，在测试时，将这样的移动部件放入代码库会非常烦人。在本文中，我们将看到如何在 Java 中控制时间。

让我们举一个披萨外卖服务的简单例子。它的政策是比萨饼应该在 10 分钟内送到。当客户要求送货时，系统会根据此政策自动计算送货时间。

```
public class DeliveryPolicy {
  public Delivery createDelivery(Pizza pizza) {
    LocalDateTime deliveryTime = LocalDateTime.now().plus(10, ChronoUnit.MINUTES);

    return new Delivery(pizza, deliveryTime);
  }
} 
```

## 我们如何测试？

由于时间总是变化的，我们无法在不改变代码的情况下非常精确地测试这个方法。我们可以测试交付时间大约是*未来* 10 分钟，但是像所有模糊测试一样，它可能会根据执行的上下文而不时失败。

## 使依赖关系可见

这段代码如此难以测试的原因是它有一个隐藏的依赖项:时钟。从 Java 8 开始，Date API 的所有`now`方法都以时钟作为参数。然后，我们可以使这种依赖性可见。

```
public class DeliveryPolicy {

  private final Clock clock;

  public DeliveryPolicy() {
    this.clock = Clock.systemDefaultZone();
  }

  public Delivery createDelivery(Pizza pizza) {
    LocalDateTime deliveryTime = LocalDateTime.now(clock).plus(10, ChronoUnit.MINUTES);

    return new Delivery(pizza, deliveryTime);
  }
} 
```

## 依赖注射来救援

既然依赖关系已经存在，我们可以把它注入到构造函数中，就像任何其他依赖关系一样，测试这个方法变得很简单。

```
public class DeliveryPolicy {

  private final Clock clock;

  public DeliveryPolicy(ClockProvider clockProvider) {
    this.clock = clockProvider.get();
  }

  public Delivery createDelivery(Pizza pizza) {
    LocalDateTime deliveryTime = LocalDateTime.now(clock).plus(10, ChronoUnit.MINUTES);

    return new Delivery(pizza, deliveryTime);
  }
} 
```

```
public class DeliveryPolicyTest {

  @Test
  public void should_schedule_delivery_ten_minutes_later() {
    ZonedDateTime now = ZonedDateTime.of(LocalDateTime.of(2017, 7, 18, 0, 0, 0), ZoneId.of("+01"));
    DeliveryPolicy policy = new DeliveryPolicy(() -> Clock.fixed(now.toInstant(), now.getZone()));

    Delivery delivery = policy.createDelivery(new Pizza());

    LocalDateTime tenMinutesLater = LocalDateTime.of(2017, 7, 18, 0, 10, 0);
    assertThat(delivery.getDeliveryTime()).isEqualTo(tenMinutesLater);
  }
} 
```

## 用法同 Spring 框架和 Spring Boot

如果您在应用程序中使用 Spring Framework，您可以创建一个`ClockProvider` bean 来给出默认时钟。此外，如果您使用 Spring Boot，它允许您在集成测试中用`@MockBean`注释非常容易地模拟这个 bean。

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class DeliveryPolicyIT {

  @MockBean
  private ClockProvider clockProvider;

  @Autowired
  private DeliveryPolicy policy;

  @Test
  public void should_schedule_delivery_ten_minutes_later() {
    ZonedDateTime now = ZonedDateTime.of(LocalDateTime.of(2017, 7, 18, 0, 0, 0), ZoneId.of("+01"));
    Mockito.when(clockProvider.get()).thenReturn(Clock.fixed(now.toInstant(), now.getZone()));

    Delivery delivery = policy.createDelivery(new Pizza());

    LocalDateTime tenMinutesLater = LocalDateTime.of(2017, 7, 18, 0, 10, 0);
    assertThat(delivery.getDeliveryTime()).isEqualTo(tenMinutesLater);
  }
} 
```

```
@Configuration
public class ClockConfig {

  @Bean
  public ClockProvider clockProvider() {
    return () -> Clock.systemDefaultZone();
  }
} 
```

## 结论

我以前觉得时间和随机一样，是很难检验的东西。有了 Java 8 Date API，这就变得微不足道了。你只需要承认你对时钟的依赖，并像对待其他依赖一样对待它。现在，我几乎从不在不传递时钟作为参数的情况下使用 Date API 的方法。这允许我非常容易地控制整个应用程序的时间(在单元、集成或功能测试中)。