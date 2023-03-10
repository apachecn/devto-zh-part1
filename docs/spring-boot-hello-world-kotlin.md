# Spring Boot:你好，世界，科特林

> 原文：<https://dev.to/moelholm/spring-boot-hello-world-kotlin>

在这篇文章中，我将展示如何使用 Kotlin 1.1 创建一个 Spring Boot 1.5 应用程序(相对于现在的 Java 8)。

> Spring Boot:你好，世界，科特林

我创建的示例是一个典型的“Hello World”示例。我选择实现一个 Spring MVC 控制器——和一个令人敬畏的 Spring Boot 集成测试。Gradle 构建脚本也使用了 Kotlin(这很酷)。您可以在 GitHub 上找到完整的示例项目和真实的上下文[。](https://github.com/nickymoelholm/smallexamples/tree/master/springboot-kotlin-helloworld)

在这篇文章中，我还解释了一些 Kotlin 规范，值得一个典型的(Spring) Java 开发人员注意。如果你是一个精通 Kotlin 的开发人员，并且发现我的解释是错误的或误导性的，那么请留下评论，以便我可以修正它们。我绝不是科特林专家(目前还不是！).

只是想看看代码？

# 1/4:Spring Boot 申请

```
import org.springframework.boot.SpringApplication
import org.springframework.boot.autoconfigure.SpringBootApplication

@SpringBootApplication
class Application

fun main(args: Array<String>) {
    SpringApplication.run(Application::class.java, *args)
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们使用普通的`@SpringBootApplication`注释激活了 Spring Boot。有趣的是，如果你愿意，在 Kotlin 类中，甚至不需要有一个实体。我猜他们中的许多人都会有一个-但在这里它是完全没有必要的。

Kotlin 中的`main`方法是一个包级函数(它没有嵌入到类中)。

我们将`Application::class.java`传递给`run`方法。这不是输入错误，也不是 Java 源文件引用:)，它仍然是代表类`Application`的`Class`对象。如果你只是传递`Application::class`——那么你会传递一个类型为`KClass`的对象——这是 Kotlins 自己对 Java 中的`Class`类型的表示！

奇怪的`*args`不是指针——而是 kot Lins*spread*操作符(`run`方法声明了一个 *vararg* 参数)。

(此外:不使用分号——不过对我来说没什么大不了的)

# 4 个中的 2 个:控制器

```
import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.PathVariable
import org.springframework.web.bind.annotation.RestController

@RestController
class GreetingController {

 @GetMapping("/hello/{name}")
 fun get(@PathVariable name: String) = "Hello, $name"

} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个典型的 Spring MVC 控制器。我打赌你能认出`@RestController`、`@GetMapping`和`@PathVariable`(如果你是一个有经验的 Spring Java 开发者的话)。

请注意，这里的函数以`fun`为前缀。它看起来像一个变量赋值，但它不是。你看到的是字符串 *Hello，$name* 的隐式`return`语句。该函数本来可以有一个主体和一个`return`语句，但在这种情况下没有必要。

注意，函数没有显式的返回类型。你可以把`: String`放在等号前面。但是 Kotlin 可以推断出来——在这种情况下也没有必要。

`$name`参数被替换为`name`参数的内容——演示了 Kotlins 对*字符串插值*的支持。这个几乎无关紧要的特性会对我见过的许多 Java 项目产生巨大的影响！

# 3/4:整合测试

```
import org.assertj.core.api.Assertions.assertThat
import org.junit.Test
import org.junit.runner.RunWith
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.boot.test.context.SpringBootTest
import org.springframework.boot.test.web.client.TestRestTemplate
import org.springframework.test.context.junit4.SpringRunner

@RunWith(SpringRunner::class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class GreetingControllerIntegrationTests {

    @Autowired
    lateinit var restTemplate: TestRestTemplate

    @Test
    fun `GET when given Duke then returns "Hello, Duke"`() {

        // Given
        val name = "Duke"

        // When
        val body = restTemplate.getForObject("/hello/{name}", String::class.java, name)

        // Then
        assertThat(body).isEqualTo("Hello, $name")

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这里值得注意的是古怪的修饰语[lateinit]。事情是这样的:Kotlin 通常假设成员为非空，因此必须在构造函数中将其显式赋值为非空值。但是由于 *Spring TestContext 框架*在构造函数运行后负责注入，那么我们需要使用`lateinit`修饰符显式地允许它。

另一个有趣的元素是`@Test`函数名:我实际上是从名字`get_whenInvokedWithDuke_thenReturnsHelloDuke`开始的。但是我根据一位读者的提示更新了这个例子:在函数名周围使用反勾号允许我们提供类似句子的函数名。如果你问我，我觉得命名测试非常好。

在`@Test`本身:注意`val`的使用。这就像 Java 的`final`修饰语(在变量的上下文中)。科特林的类型推断意味着我们不需要写下类型(`String`)。

因此....我觉得这个看起来超级好看。代码中没有过多的类型信息。至少在 Hello World 这个案例中，我认为这是非常好的。我对字符串插值也很满意:)...(在 Java 领域，我倾向于使用`String.format("stuff...%s", varhere)`——相当烦人)。

# 4 之 4:格拉德脚本

在过去的 1.5 年里，我一直使用 Gradle 作为 Maven 的替代者。在那段时间里——我总是在我的 Gradle 脚本中使用 Groovy 作为编程语言。我有点喜欢这样——但是，我也必须承认 IDE 的帮助并不是最优的——即使是在我现在使用的 IntelliJ 中。

然后当我浏览 Kotlin 特性等时，我发现 Gradle 的人正致力于支持 Kotlin 作为另一种脚本编程语言。不仅如此:他们正致力于使 Kotlin 成为开发 Gradle 插件的首选语言！

好的-代码:

```
buildscript {

    val springBootVersion = "1.5.2.RELEASE"
    var kotlinVersion: String by extra
    kotlinVersion = "1.1.0"

    repositories {
        mavenCentral()
    }

    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:$springBootVersion")
        classpath("org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlinVersion")
        classpath("org.jetbrains.kotlin:kotlin-allopen:$kotlinVersion")
    }

}

val kotlinVersion: String by extra

apply {
    plugin("kotlin")
    plugin("kotlin-spring")
    plugin("org.springframework.boot")
}

repositories {
    mavenCentral()
}

dependencies {
    compile("org.jetbrains.kotlin:kotlin-stdlib:$kotlinVersion")
    compile("org.jetbrains.kotlin:kotlin-reflect:$kotlinVersion")
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
} 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这就像普通的基于 Groovy 的脚本。这很好-所以没有大的重新调整。

我很确定你应该使用最新版本的 IntelliJ 来获得良好的基于 Kotlin 的构建脚本的 IDE 体验。对我来说似乎还可以(*IntelliJ IDEA 2016 . 3 . 5*)——但是内容辅助非常慢。我想那会在某个时候被正确地钉上。此外，我仍然认为我得到了一堆奇怪的建议，在我的内容在不同的区块协助。

# 动机:为什么是科特林？

我喜欢 Java，因为它简单——而且我对它了如指掌。

但是，说实话，我觉得今天大多数其他流行语言都有一些 Java 所没有的好特性。Kotlin 是这些新 JVM 语言中的一种——它有一个真正了不起的特性列表。翻到参考手册，浏览一下[kotlin reference]；我保证当你读完每一篇文章时，你会拍手喝彩。

最近，我注意到 Pivotal 是如何拥抱 Kotlin 的——通过示例、公开演示会议(由我们最喜欢的摇滚明星 Starbuxman [starbuxman])以及无缝使用 Kotlin [springkotlin]的核心 Spring 框架。作为一个狂热的 Spring Boot 粉丝，这引起了我的注意。

我还没有在现实世界的项目中尝试过 Kotlin。但是我真的希望我能很快得到这个机会。

# 参考文献

*【科特林参考】:科特林参考*
T3】https://kotlinlang.org/docs/reference/

*【starbuxman】:Spring Tips:kot Lin 编程语言*
[https://Spring . io/blog/2016/10/19/Spring-Tips-The-kot Lin-Programming-language](https://spring.io/blog/2016/10/19/spring-tips-the-kotlin-programming-language)

*【Spring kotlin】:介绍 Spring 框架 5.0 中的 Kotlin 支持*
[https://Spring . io/blog/2017/01/04/Introducing-Kotlin-support-in-Spring-Framework-5-0](https://spring.io/blog/2017/01/04/introducing-kotlin-support-in-spring-framework-5-0)

*【late init】:kot Lin late init 修饰符:*
[https://kot linlang . org/docs/reference/properties . html # late-initialized-properties](https://kotlinlang.org/docs/reference/properties.html#late-initialized-properties)

*【格拉德·科特林】:科特林遇上格拉德*

*【gradlescriptkotlin】:Gradle Script Kotlin-FAQ*
[https://github . com/Gradle/Gradle-Script-Kotlin/wiki/FAQ-Questions # in-what-language-should-I-develop-my-plugins](https://github.com/gradle/gradle-script-kotlin/wiki/Frequently-Asked-Questions#in-what-language-should-i-develop-my-plugins)