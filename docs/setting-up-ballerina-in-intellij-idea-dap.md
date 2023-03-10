# 在 IntelliJ IDEA 中设置芭蕾舞演员

> 原文：<https://dev.to/shan1024/setting-up-ballerina-in-intellij-idea-dap>

芭蕾舞演员是一种新的开源编程语言，有文本和图形语法。从这篇文章中，我将重点介绍如何在 [IntelliJ IDEA](https://www.jetbrains.com/idea/) 建立一个芭蕾舞项目。如果你想了解更多关于芭蕾舞演员语言的知识，你可以参考[这篇](https://medium.com/ballerinalang/ballerina-making-sequence-diagrams-work-d0d7b3846a80)文章。

## 安装芭蕾舞演员插件

因此，我们需要做的第一件事是将芭蕾舞演员插件安装到 IntelliJ IDEA。但请注意，必须有 IDEA 版本 2016.3 或以上才能使用该插件。这个插件支持 IDEA 社区和终极版。

安装芭蕾舞女插件的步骤-
1)进入**文件** ( **偏好**在 **MacOS** ) - > **设置**。
2)选择**插件**部分，点击**浏览存储库**按钮。
[![Settings](img/187ddd23451eb40be0c9b7a0780e8a06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--138ZqtNS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aab69ypxtakk9d4z330c.png) 
3)在搜索框中输入“芭蕾舞演员”。芭蕾舞演员插件将如下所示。单击安装按钮。
[![Browse Repositories](img/1cfddd5c8b6d963585032281db9771ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f5RWDgNp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ac3w2pand2tl8yjcl7oq.png) 
4)成功安装插件后，需要重启意念。

## 下载芭蕾舞演员运行时

为了充分发挥 Ballerina 插件的潜力，您需要将 Ballerina 运行时作为 SDK 添加到这个想法中。
你可以从[芭蕾郎](https://ballerinalang.org/downloads/)网站下载运行时发行版。下载运行时发行版并解压缩文件。

## 创建新的芭蕾舞项目

1)进入**档**->-**新建**->-**项目**。
2)选择**芭蕾舞演员**项目类型，然后选择**下一个**。
[![Project Wizard](img/11a3cb3bcf7887819a80271805e5e902.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w99Kfi8h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a74ketkjokqb37poxjkw.png) 
3)现在我们要在下一个窗口设置一个芭蕾舞演员 SDK。点击**配置**按钮。
[![Project Wizard](img/2aa414bc36da32e2d58908492e5a6616.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Bmrk1G4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qn7wwjatdxjqrt3wr8im.png) 
4)然后选择解压后的运行时目录，选择 **OK** 。
[![SDK Home selection](img/cd6026cc5358cacf0f3b1411151bc6cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xhYRAUwu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lby5snrgwbwitzeabjxz.png) 
5)如果您按照正确的步骤操作，芭蕾舞演员运行时应该成功添加为 SDK。现在选择下一个的**。
[![Project Wizard](img/f917798d81c99afeec070e2d0f00605a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WtMSCCxT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/suzmb1olewx1izvv8e4b.png) 
6)现在更新项目名称和项目位置，选择**完成**。
[![Project Wizard](img/4271a413bbb6c66f29e66d07301ad81a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SKs5bgRC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c9ftfvxchh99wc0r6k6z.png) 
7)芭蕾舞演员项目将被创建并打开，如下图所示。
T45![Ballerina Project](img/29d06797984e04af5512bd15b10bfd78.png)T47】**

## 创建新的芭蕾舞演员文件

1)进入**档** - > **新** - > **芭蕾舞演员档**。
2)输入文件名并选择**芭蕾舞主**种类。芭蕾舞演员主类型将创建一个具有主功能的新文件。其他种类也很少。
[![New File](img/6cf082f5e80959771f7c15f0d0bcd9be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dTsgwuk6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/thsk87wlpw5frfh4rs5p.png) 
3)现在你会有一个新的芭蕾舞者程序文件，带有一个主函数。
[![New File](img/7ac34b93f3cd0ae6e9441e9c9faf866b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PcGB_tAQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1aqbti7xziyuifm4fziz.png) 
4)现在你可以使用主函数旁边的运行图标来运行程序。如果你不添加芭蕾舞演员 SDK，你将无法在 IDEA 中运行芭蕾舞演员程序。这就是我们在上一节中配置芭蕾舞演员 SDK 的原因。
[![Run File](img/f9be7e9e942a52ecc564576d124e3ad6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wt4Ud3hB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1gkyrafipg652h8n7epl.png) 
点击运行图标，选择运行命令。
[![Run File](img/a68ed1582be88a99ec9ea2640d727f5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mrYUSNVA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/04dn9pd27636wtzsjrik.png) 
5)你会在运行窗口看到输出。
[![Run Window](img/1578ab35269d0743dd643643e73be3ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L7nvv97P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cdru60n1yx41fwgp9dig.png)

祝贺您，您已经成功地在 IntelliJ IDEA 中配置了 Ballerina 并执行了 Hello World 程序。现在你已经准备好在芭蕾舞中进行一些严肃的动作了:)

## 那么接下来去哪里呢？

可以参考 [Ballerina by Example](https://ballerinalang.org/docs/by-example/) 看各种用 Ballerina 写的例子。如果你有问题，建议等，你可以-

*   将它们发送到[芭蕾舞女演员开发](https://groups.google.com/forum/#!forum/ballerina-dev)谷歌群组。
*   将它们发送到[芭蕾舞演员松弛](https://ballerinalang.slack.com/messages/C47EAELR1/)频道。
*   打开一个 [GitHub 问题](https://github.com/ballerinalang/ballerina/issues)。