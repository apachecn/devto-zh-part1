# 如何为 Java 设置 Cucumber 测试

> 原文：<https://dev.to/monknomo/how-to-set-up-cucumber-tests-for-java>

Java cucumber 测试有 3 个部分。

1.  特征文件
2.  步骤定义(“粘合”文件)
3.  junit 测试文件

特征文件是面向行为或业务的测试描述所在的地方。这些文件是用小黄瓜语法编写的。

在编写黄瓜测试时，首先要编写特征文件。如果在你的组织中可能的话，让利益相关者、企业主和用户来写。如果没有，首席开发人员和/或项目经理应该与风险承担者、企业所有者和用户一起编写特性文件。把作品放进去，收集他们的故事和场景。

编写完特征文件后，编写步骤定义。这就是黄瓜人所说的“胶水”代码。它本质上是正则表达式、设置和测试代码。

最后，为了使在 Java 中运行 Cucumber 测试变得容易，编写一个 junit 包装类。这是一个带注释的空类，它将使 Cucumber 能够使用 junit 的测试机制来运行 Cucumber 测试。

## 举例

我已经创建了一个示例项目[。](https://github.com/monknomo/Cucumber-Example-Art-Fight/) Cucumber 使用 Maven，所以我们有一个具有标准 Maven 目录结构的项目:

```
+---src
|   +---main
|   |   +---java
|   \---test
|       +---java
|       \---resources 
```

Enter fullscreen mode Exit fullscreen mode

### 组织

让我们将特性文件放在测试/资源下。让我们将 junit 类放在主测试包下——在本例中是`com.gunnargissel.cucumberexample.artfight`。让我们将步骤定义(胶水)放在主测试包- `com.gunnargissel.cucumberexample.artfight.stepdefs`下的 stepdefs 包中

### 行为场景

对于这个例子，我们正在为一家美术用品商店创建业务引擎。让我们以斯图亚特·森普尔为例。斯图尔特·森普尔向所有人出售“黑色 2.0——世界上最粗糙、最平坦的黑色艺术材料”，除了[安尼施·卡普尔。](http://anishkapoor.com/)这里有一个[的背景故事。](https://www.format.com/magazine/features/art/anish-kapoor-stuart-semple-vantablack-blackest-black)

下面是一个特征文件可能的样子，`no_anish_kapoor.feature`:

```
Feature: Stuart Semple will sell Black 2.0 to any artist except Anish Kapoor

    Scenario Outline: Anish Kapoor tries to buy Black 2.0
                       Given a purchaser, <purchaser name>, Black 2.0 is <saleable>
                       Examples:
                                 | purchaser name    | saleable     |
                                 | Anish Kapoor      | not saleable |
                                 | John Doe          | saleable     |
                                 | Jane Doe          | saleable     |
                                 | Manish Kapoor     | saleable     |
                                 | Anish Kapoorski   | saleable     | 
```

Enter fullscreen mode Exit fullscreen mode

有了特征文件，就该创建步骤定义或“粘合”代码了。在这种情况下，创建 src/test/Java/com . gunnargissel . blacktwopointoh . step defs . noanishkapoor . Java

上面概述的场景只使用了一个关键字“Given”。这意味着我们将在步骤定义“glue”类中创建一个使用`@Given`注释的方法。`@Given`注释的目标是编写一个匹配场景的正则表达式。在这种情况下，我们希望`<purchaser name>`和`<saleable>`质量成为传递给 stepdef 的变量。剩下的`@Given`是我们模式匹配的商业语言。stepdef 执行典型 junit 测试的角色，以及测试的设置。

```
@Given("^a purchaser, (.*), Black 2\\.0 is (not saleable|saleable)$")
            public void testWhoCanPurchaseBlackTwoPointOh(String purchaser, String saleable) {
                    boolean actual_saleable = BusinessRules.saleable(purchaser);
                    boolean expected_saleable = saleable.equals("saleable");
                    assertEquals(expected_saleable, actual_saleable);
            } 
```

Enter fullscreen mode Exit fullscreen mode

现在有了场景和步骤定义，需要像这样创建一个 junit runner 类:

```
@RunWith(Cucumber.class)
@CucumberOptions(
                       format={"pretty", "junit:target/cucumber/black_two_point_oh_sales_list.xml"},
                        features = {"classpath:no_anish_kapoor.feature"},
                        glue={"com.gunnargissel.cucumberexample.artfight.stepdefs"}
)
public class TestBusinessRules {} 
```

Enter fullscreen mode Exit fullscreen mode

创建必要的文件后，运行`mvn test`将执行上面创建的特性场景。Maven 将在`target/cucumber`目录中创建`black_two_point_oh_sales_list.xml`。

## 进一步资源

《Cucumber 的正则表达式就够了》是一本很好的指南，解释了如何在 Cucumber 中使用正则表达式(和 Java)。同一作者还制作了[的小抄](http://agileforall.com/just-enough-regular-expressions-for-cucumber/)，这是一个有益的参考。

artfight 示例代码。

[每月收到一封电子邮件，里面有来自网络的优秀技术和技术领导文章](http://www.gunnargissel.com/pages/email-signup-1.html)

[原创文章](http://www.gunnargissel.com/how-to-setup-cucumber-junit-tests-for-java.html)请访问[我的博客](http://gunnargissel.com)

[*感谢阿里·伯朗·蒂蒂泽尔的头球攻门*](https://flic.kr/p/9xmxaQ)