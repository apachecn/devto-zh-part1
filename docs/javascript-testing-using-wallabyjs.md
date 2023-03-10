# 使用 WallabyJs 进行 Javascript 测试

> 原文：<https://dev.to/samueleresca/javascript-testing-using-wallabyjs>

# 使用 WallabyJs 进行 Javascript 测试

[![Javascript testing using Wallabyjs](img/f3f25b18be2d8cd038664522d068d1de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7JF6D6Aq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://samueleresca.net/wp-content/uploads/2016/03/wallby_js-300x83.png)

> Wallaby.js 的速度**快得惊人**，因为它**只执行受你的代码变化影响的测试**，并且并行运行你的测试**。**

 **[![javascript testing using wallabyjs](img/bad2036c7dd7ba980a5c03d2feaa2476.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--9BXbEZUV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://samueleresca.net/wp-content/uploads/2016/03/vsc_2.gif) [Wallaby.js](http://wallabyjs.com/) 是 JavaScript 的测试程序，它可以集成到最常见的代码编辑器/IDE 中，无论你是在做 TDD 还是 BDD，它都可以提高工作效率。Wallaby.js 不是免费的，但它比其他语言的类似商业解决方案便宜。

## [T1】wallaby . js 的力量](#the-power-of-wallabyjs)

Wallaby.js 的**功能**是测试和代码编辑器之间的**集成:**它在你写代码的时候报告代码覆盖率、测试结果和错误。Wallaby.js 支持很多技术，你可以在 [github 账号](https://github.com/wallabyjs)上找到很多**样本和模板**。

#### 支持的技术

Wallaby.js 支持在[最常见代码](http://wallabyjs.com/#download)编辑器: **Sublime text，Atom，Visual studio code，Visual Studio 2013/15，WebStorm.Â * *支持浏览器代码测试(通过**PhantomJS 或 node.js** )和 node.js 测试，也支持很多测试框架，例如: **Jasmine，Mocha，QUnit** 。最后，还可以使用**类型脚本**或 **CoffeeScript 编写测试。**

#### 安装和配置

Wallaby.js 可以作为代码编辑器的扩展来安装:例如，在 VS 代码中，您可以使用扩展管理器简单地安装:

## T2】

使用 Wallaby.js 的 Javascript 测试需要一个配置文件来工作，推荐的名称是`wallaby.js` / `wallaby.json`或`wallaby.conf.js.`以下代码显示了一些简单的配置选项:[【https://gist.github.com/samueleresca/f2da5b0579403d81ff6f】](https://gist.github.com/samueleresca/f2da5b0579403d81ff6f)[这里是](http://wallabyjs.com/docs/config/overview.html)**配置选项的完整列表。**

### 依我看..

请记住， **Wallaby.js 不是一个文件监视器**:文件监视器会运行你所有的 JavaScript 测试，这可能非常耗时。Wallaby 使用依赖分析和其他技巧，只运行受你的代码更改影响的测试，不多不少，所以即使在有大量测试的大型代码库中，反馈也会很快交付。我在两个 IDE 上试过 wallaby . js:**Visual studio 2013**和 **WebStorm 11** 。它改进了我的 [TDD 工作流程](http://samueleresca.net/2016/03/how-tdd-process-works/)，我认为它非常有用，因为它可以跳转到失败的测试，错误来源，显示错误细节。导入我写在 Mocha 和 Chai 上的测试代码也很容易。干杯:)**