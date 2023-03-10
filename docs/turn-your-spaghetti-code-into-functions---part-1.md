# 将你的代码转换成函数——第 1 部分

> 原文：<https://dev.to/monknomo/turn-your-spaghetti-code-into-functions---part-1>

*[原载于我的博客](http://www.gunnargissel.com/turn-your-spaghetti-code-into-functions-part-1.html)*

开发人员可能会花费大量时间来对抗业务规则代码。[意大利面业务规则](https://en.wikipedia.org/wiki/Spaghetti_code)使得很少的更改需要在 if/else 块中重复粘贴。这就像试图把一头大象塞进一辆智能汽车，而这应该像组装乐高积木一样。

任何研究过一套“成熟”商业规则的人都知道，理清正在发生的事情是极其困难的。如果把大象塞进智能汽车很难，那么把它弄出来就更难了。我将展示如何让你的智能汽车驾驶大象快乐一点。

[![](img/e805b97adf4466fae5770a29a3591a29.png "Cramming an elephant into a Smart Car")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0lTvFOEE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/WtnFFKs.png)

业务规则是你的赚钱机器，是你业务的本质。他们也改变了很多。这种动态往往会导致业务规则的仓促制定，从长远来看，会在你的赚钱机器的心脏部位制造一个糟糕的烂摊子。一堆乱七八糟的东西很难读懂，更难弄清楚它应该在做什么。

此外，面向对象封装业务规则的方式要么根本不完善，要么被正在工作的开发人员广泛误解，以至于通用模式是一个[神类](https://en.wikipedia.org/wiki/God_object)，它改变了它所涉及的一切。测试一个塞满意大利面和变异函数的大类是令人畏惧的，往好里说是不可能的。没有测试，没有恐惧就很难发展。

这里有一套让代码更容易遵循的技巧，所以你可以努力理解你的利益相关者，并确保代码中的内容是他们*希望*出现在代码中的。

这将教你如何解开商业规则，这样你就可以轻松地使用它们。

### 可读性

深度嵌套会削弱可读性。即使当开发人员很好地使用制表符并与他们的花括号用法保持一致时，在 if/else 语句的第三层，它开始变得很难区分你正在阅读的是哪个特定的分支。

> 这是带有空子对象的那个吗？我会很快地在这里再次检查 null

通过构建您的代码来避免这种冲动，这样就不会对您正在检查的内容有任何疑问。

深入到子对象或孙对象的长条件也会损害可读性。利益相关者从来不说(除非他们被严重滥用)类似这样的话:

> 如果转移类型代码为 200，并且不在 907、412 或 213 区域，则不允许

1.  比人类喜欢说的“不”还要多
2.  人类通常不会谈论代码。

他们可能会说这样的话:

> 如果是合作伙伴交易，我们只允许在合作伙伴区域进行

如果你的代码需要精神上的后空翻来配合你的利益相关者说话的方式，你会有一段不好的时间。他们也是，他们甚至不知道为什么。

### 可测性

复杂的业务规则代码很难测试。深入嵌套的 if/else 语句的曲折很容易迷失，很容易忘记分支，有时在测试工具中根本不可能设置。没有测试意味着每一个变化都是危险的——你是一个放错位置的人`}`，或者是一个让你的用户头疼的倒退的人`>`。

[![](img/54a388be70da43c3acd9e598d70f504e.png "Mutant strawberry")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r2OQmCpZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/UEHFn8Am.jpg)

粗糙的业务规则代码似乎鼓励开发人员改变它所验证的对象。我不知道这是为什么，但我在野外见过很多次。状态突变将测试难度放大了一个数量级。现在，您不仅必须测试消息和预期的错误，还必须检测您正在验证的对象的更改。他们是故意的吗？他们是正确的吗？

谁知道呢？我保证没人写下来。

### 反应速度

利益相关者、用户、管理者和开发者不断地参与时间线的拉锯战。技术债务束缚了开发者满足用户需求的能力。一个很好的“告诉”你的系统有很多技术债务是用户可以容易地描述一个特性，但是开发者不能容易地实现它。最简单的事情变成了长达一个月的丛林演习。

[![](img/d19cf3003228cc64cfccaa3cbd5f4c1c.png "Devil's Club is thorny")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YFdhrQDR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/PCMKeOMm.jpg)

对我来说，没有什么比下山、离开小路、穿越魔鬼俱乐部和松散的碎石更能说明丛林了

等等。我的意思是，没有什么比进入一个方法，进入一个错误的嵌套 if/else 分支，并修复一些没有被破坏的东西更糟糕的了。或者注意到 100 行代码从来没有被执行过，因为它们不工作。或者意大利面条式的商业规则带来的许多有趣的惊喜。

### 可组合性

可组合性允许您从旧规则中创建新规则。大多数新的商业规则与现有的规则有许多共同之处。组合现有规则并添加一个特例的能力非常强大。你的股东会对你的周转时间感到惊讶！

意大利面风格是不可组合的。复制粘贴、复制代码和篡改 switch 语句实际上是杂乱无章的业务规则的需求。祝你好运重复使用一些东西来得到一个

## 示例不良代码

[这里有一个标准 Java 业务逻辑](https://github.com/monknomo/If-Else-Block-Refactoring/tree/master/src/main/java/com/gunnargissel/suemez/businessrulerefactorexample)的例子，它评估一个业务对象(`BusinessTransfer`)，如果它违反了业务规则，就创建消息返回给用户:

```
public static final String checkWidgetTransfer(WidgetTransfer transfer) {
    String businessRuleErrors = "";

    if (transfer.getTransferer().getAccount(transfer.getFromAccount()).getBalance().compareTo(transfer.getAmount()) < 0) {
        businessRuleErrors += "Insufficient balance to transfer ; ";
    }

    if (transfer.getTransferTypeCode().equals("200")) {
        if (!transfer.getAreaCode().matches("907|412|213")) {
            businessRuleErrors += "This area is not a transfer eligible area. ; ";
        } else if (!transfer.getAreaCode().matches("213")) {
            if (transfer.getTransferer().getCategory().equals("D")) {
                businessRuleErrors += "D Category Transferer can only be transferred in transfer area 213\. ; ";
            }
        }
    } else if (transfer.getTransferTypeCode().equals("710")) {
        if (!transfer.getAreaCode().matches("574|213|363|510")) {
            businessRuleErrors += "This area is not a transfer eligible area. ; ";
        }
    }

    if (transfer.getTypeCode().equals("I")) {
        if (isBlockSize(transfer)) {
            businessRuleErrors += "Amount is too small for I type transfer. ; ";
        }
        if (isTotalOverCap(transfer)) {
            businessRuleErrors += "This transfer is too large. ; ";
        }
    }

    return businessRuleErrors;
}

public static boolean isBlockSize(WidgetTransfer transfer) {
    return transfer.getAmount().compareTo(1000) < 0;
}

public static boolean isTotalOverCap(WidgetTransfer transfer) {
    return transfer.getAmount().compareTo(1000000) > 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面这个例子的灵感来自于在野外运行的实际代码。我在这里展示的是简化的、匿名的。和原著一样难读。有效地读取它需要了解什么是“200”转移类型代码或者对于不同的转移区域代码什么是可接受的数据。括号是嵌套的，这使得复制粘贴(处理这种风格的代码的常用技术)变得非常危险。一个开发人员不能错过一个大括号而不引起一个难以调试的问题。

## 逻辑块范式转换

[重构长分支`if/else`代码的一个快速方法是取消分支和`elses`。](https://github.com/monknomo/If-Else-Block-Refactoring/tree/master/src/main/java/com/gunnargissel/suemez/businessrulerefactorexample/refactor1)通过将每个业务规则重新表述为一个正约束，开发人员可以检查是否满足约束条件，而不是遍历一个分支逻辑树。这种技术增加了一点行数，但是提高了可读性。

另一个容易实现的方法是创建实例变量来保存值，而不是使用 getter(或者更糟，嵌套 getter！).这提供了在您使用它的上下文中命名一个东西的能力，而不是依赖 getters 在您的上下文中有一个好的名称。

```
public static final String checkWidgetTransfer(WidgetTransfer transfer ) {

    String businessRuleErrors = "";

    Integer balance = transfer.getTransferer().getAccount(transfer.getFromAccount()).getBalance();

    Integer transferAmount = transfer.getAmount();

    String transferTypeCode = transfer.getTransferTypeCode();

    String areaCode = transfer.getAreaCode();

    String category = transfer.getTransferer().getCategory();

    String typeCode = transfer.getTypeCode();

```
if (balance.compareTo(transferAmount) &gt; 0) {
    businessRuleErrors += "Insufficient balance to transfer ; ";
}

{
    if (transferTypeCode.equals("200")
            &amp;&amp; !areaCode.matches("907|412|213")) {
        businessRuleErrors += "This area is not a transfer eligible area. ; ";
    }
}

if (transferTypeCode.equals("200")
        &amp;&amp; areaCode.matches("213")
        &amp;&amp; category.equals("D")) {
    businessRuleErrors += "D Category Transferer can only be transferred in transfer area 213\. ; ";
}

if (transferTypeCode.equals("710")
        &amp;&amp; !areaCode.matches("574|213|363|510")) {
    businessRuleErrors += "This area is not an eligible area. ; ";

}

if (!typeCode.equals("I")
        &amp;&amp; !isBlockSize(transfer)) {
    businessRuleErrors += "Amount is too small for I type transfer. ; ";
}

if (!typeCode.equals("I")
        &amp;&amp; isTotalOverCap(transfer)) {
    businessRuleErrors += "This transfer is too large. ; ";
}

return businessRuleErrors; 
```

    Enter fullscreen mode 

    Exit fullscreen mode 

} 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
好人

*   上面的代码可读性更好。
    *   每个业务规则都包含在自己的逻辑块中
    *   确定是否满足条件所需的所有属性都被命名为。
    *   你可以用书面形式描述每一条商业规则，它听起来非常像英语。
*   逻辑块很小并且是离散的。
    *   没有嵌套的花括号，所以你很难迷失在代码中。

### 坏了

*   仍然需要关于代码含义的商业知识
    *   什么是“200”转移类型代码？代码没有说明，所以希望它在某个地方有文档记录...
*   否定条件句比比皆是- `if (!typeCode.equals("I"))`
    *   消极条件句有点难以启齿，比积极条件句更难讲道理。
*   还在变异那些`businessErrorMessages`
    *   这只是在单元测试中要设置的另一件事

## 接下来是什么？

看看第二部分，看看接下来会发生什么！

你会看到:

*   如何使用谓词让你的生活更美好
*   使用实物，而不仅仅是它们的属性，让你的生活更美好
*   使用函数和验证器对象，让您的生活更美好

我们会努力让那头大象坐上他的智能汽车，但最重要的是，我们会努力让你的生活变得更好。

[![](img/ab46f4a873a0c8bb49586065eef1078f.png "An elephant driving a Smart Car")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YNLbFAlJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/xfmb1Bs.png)

如果你喜欢这个，[访问我的博客了解更多信息](http://www.gunnargissel.com)

## 学分

*感谢[沃蓝达](https://www.flickr.com/photos/volantra/)的[右向智能车图片](https://flic.kr/p/74L2vC)T5】*

*感谢 [roebot](https://www.flickr.com/photos/roebot/) 的[朝左智能车](https://flic.kr/p/73uXD3)T5】*

*感谢[奥利佛·多德](https://www.flickr.com/photos/oliverdodd/)给了[大象](https://flic.kr/p/8N681r)T5】*

*谢谢[尼尔斯·海德里希](https://www.flickr.com/photos/schoschie/)的[变异草莓](https://flic.kr/p/4VX3xT)T5】*

*感谢[彼得·斯蒂文斯](https://www.flickr.com/photos/nordique/)为[魔鬼俱乐部](https://flic.kr/p/zfoSkn)T5】*

*感谢 [NASA、ESA、N. Smith (U. California，Berkeley)等人，以及哈勃遗产团队(STScI/AURA)](https://www.nasa.gov/multimedia/imagegallery/image_feature_1146.html) 对船底座星云的贡献*