# 用 TestCafe 进行 E2E 测试|简介

> 原文：<https://dev.to/chrisvasqm/e2e-testing-with-testcafe-17jl>

## 警告

#### 这将是一篇 looong 帖子。所以，拿杯咖啡/茶，抓紧时间！

## 简介

### 什么是 E2E？

端到端(E2E)测试是一种用于测试整个流程的技术，就像我们是一个真实的用户一样，通过模拟他们的行为(点击、按特定的键、在字段中键入等等)。

这对测试人员来说非常有用，因为随着项目的增长，要测试的场景数量也在增加，让人类做所有的工作会导致他们因为分心或疲劳而忽略/错过一些事情。

此外，这些测试可以被配置为在一个`Continuous Integration` (CI)系统上运行，该系统可以每隔 X 时间或者在部署之后执行所有的测试。但这是另一篇文章的主题:)

### 什么是 TestCafe？

TestCafe 是一个基于 NodeJS 的 E2E 测试工具，非常容易安装和使用，支持 JavaScript 和 TypeScript。

## 要求

### 安装 NodeJS

这可以通过他们的[网站](https://nodejs.org/en/download/)或者通过你的操作系统的终端/命令行来完成(步骤会因你的操作系统而异)。

之后，为了验证 NodeJS 是否安装成功，在终端/命令行中运行以下命令:

`npm -v`

你应该能看到版本号，我写这篇文章的时候我的是`5.6.0`。

### 安装测试咖啡馆

现在我们已经安装了`npm`，运行以下命令(这可能需要 root/admin 权限):

`npm install -g testcafe`

要以 root/admin 用户的身份运行，请在 macOS 或基于 linux 的操作系统中执行相同的命令，同时在上述命令的开头添加`sudo`，Windows 用户应该在打开`cmd`或`PowerShell`时选择`right click`并选择`Run as administrator`。

如果一切顺利，您应该能够通过运行以下命令看到 TestCafe 模块的版本:

`testcafe -v`

输出应该类似于:

```
Using locally installed version of TestCafe.
0.18.6 
```

Enter fullscreen mode Exit fullscreen mode

#### 干得好！我们差不多准备好出发了👨‍💻 /👩‍💻。

## 选择你的武器(IDE/editor)

我将使用 [VS 代码](https://code.visualstudio.com/download)作为我选择的武器+一些扩展(我稍后将展示它们，我也很兴奋开始编码！)但是可以随意选择您喜欢的 IDE/编辑器。

## 项目结构

我们最初的项目结构将是这样的:

```
project
|
└─── tests
     │  
     └─── devto.js 
```

Enter fullscreen mode Exit fullscreen mode

### 第一次测试

我们需要做的第一件事是从`testcafe`模块中`import`一个名为`Selector`的类，就像这样:

```
import { Selector } from 'testcafe' 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们需要创建一个`fixture`，给它一个名字和在所有测试开始时使用的`page` url。

稍后您将看到在哪里使用了`fixture`的名称。

现在你的代码应该看起来像:

```
import { Selector } from 'testcafe'

fixture('DEV Tests')
    .page('http://dev.to/'); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了这个，让我们开始编写测试代码。

为简单起见，我们将执行以下步骤:

*   转到开发人员主页。
*   单击“关于”链接。
*   查查每个创始人的名字。

## 回到代号——洞穴！

为了做到这一点，我们需要添加一个带两个参数的`test`方法:一个是测试名称的`String`值，一个是内部有逻辑的`async`箭头函数。

大概是:

```
test("Check founder's names", async (t) => {
    // Brace yourself, the real magic goes here.
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 我们为什么要用`async`？

这将允许我们在*并行*中运行我们的测试(这太棒了！)后来。但是现在，我们将在一个浏览器中运行我们的测试。

#### 真正的魔力在哪里？

#### 我们为什么要上`import``Selector`班？

#### 狐狸说什么？

Ssssh...就让它发生吧。

我们会到达那里:)

我们需要做的第一件事是获取 about 链接的`selector`。

> 一个`selector`基本上是一种识别页面上一个元素或一组元素的方法。

为了实现这一点，我将使用 Chrome 的内置开发工具。前往[开发到](https://dev.to/)主页，向下滚动到“关于”链接顶部的`right click`，并选择`Inspect`选项。

这将打开 DevTools 选项，您希望将注意力集中在突出显示的`<a>` HTML 元素上。

在 DevTools 面板上，点击关于链接的`<a>`上的`right click`，并选择`Copy > copy selector`。这将把`selector`值复制到你的剪贴板上。

现在，回到编辑器，像这样存储这个值:

```
const aboutLink = Selector('#sidebar-wrapper-left > div.side-bar > div.widget > div.side-footer > a:nth-child(1)'); 
```

Enter fullscreen mode Exit fullscreen mode

现在所有的代码应该是这样的:

```
import { Selector } from 'testcafe'

fixture('DEV Tests')
    .page('http://dev.to/');

test("Check founder's names", async (t) => {
    const aboutLink = Selector('#sidebar-wrapper-left > div.side-bar > div.widget > div.side-footer > a:nth-child(1)');
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 但是等等！

让我们借此机会使用 TestCafe 提供给我们的另一个*咒语*...`.withText()`法。

我们可以这样写同样的代码:

```
const aboutLink = Selector('a').withText('About'); 
```

Enter fullscreen mode Exit fullscreen mode

`.withText()`方法采用一个`String` `argument`，其工作方式类似于您可能熟悉的`.contains()`方法。它会将元素的文本值与您传入的`argument`进行比较，只有当`argument`与给定文本值的任何部分匹配时才返回`true`(注意:它是区分大小写的)；否则将返回`false`。

> 区分大小写意味着一个字母的大写使它与另一个字母不同，即使它们听起来一样。示例:“test”和“Test”在这种情况下被认为是不同的。

让我们转到 About 页面，找到我们将需要使用的其他`selector`值。

我们可以用 *copy-pasta* 的方式来做，重复我们用 DevTools 做的所有步骤，但是我们知道我们比那更好(或者我们是这么认为的)。

所以，让我们像刚刚学的那样做。借助`withText()`法的力量。

唯一不同的是，这次我们的`selector`不是一个`<a>`标签，它实际上是一个`<b>`标签。你可以通过检查每一个创始人的名字或者相信我的话来验证这一点(实际上我不会这么做)。

我们的代码现在应该是这样的:

```
const aboutLink = Selector('a').withText('About');
const firstFounder = Selector('b').withText('Ben Halpern');
const secondFounder = Selector('b').withText('Jesse Lee');
const thirdFounder = Selector('b').withText('Peter Frank'); 
```

Enter fullscreen mode Exit fullscreen mode

厉害！

如果我们看整个`devto.js`文件，应该是:

```
import { Selector } from 'testcafe'

fixture('DEV Tests')
    .page('http://dev.to/');

test("Check founder's names", async(t) => {
    const aboutLink = Selector('a').withText('About');
    const firstFounder = Selector('b').withText('Ben Halpern');
    const secondFounder = Selector('b').withText('Jesse Lee');
    const thirdFounder = Selector('b').withText('Peter Frank');
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们开始使用我们新的闪亮的选择器！

你忘了我们在测试中要做什么了吗？

是啊，我也是。但是别担心，我有家人！😎

*   转到开发人员主页。
*   单击“关于”链接。
*   查查每个创始人的名字。

`.page()`方法已经覆盖了第一步，所以我们可以标记它。

[x]转到开发人员主页。
[ ]点击“关于”链接。
[ ]查每一个创始人的名字。

为了点击“About Link ”,我们需要在测试结束时添加以下代码:

```
await t
    .click(aboutLink); 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们将必须检查是否每个创始人姓名标题都显示在页面上:

```
await t
    .click(aboutLink)
    .expect(firstFounder.exists).ok()
    .expect(secondFounder.exists).ok()
    .expect(thirdFounder.exists).ok(); 
```

Enter fullscreen mode Exit fullscreen mode

让我们后退一步，验证我们的`devto.js` :
中的所有内容都是这样的

```
import { Selector } from 'testcafe'

fixture('DEV Tests')
    .page('http://dev.to/');

test("Check founder's names", async(t) => {
    const aboutLink = Selector('a').withText('About');
    const firstFounder = Selector('b').withText('Ben Halpern');
    const secondFounder = Selector('b').withText('Jess Lee');
    const thirdFounder = Selector('b').withText('Peter Frank');

    await t
    .click(aboutLink)
    .expect(firstFounder.exists).ok()
    .expect(secondFounder.exists).ok()
    .expect(thirdFounder.exists).ok();
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 还在吗？

我希望你知道。因为现在到了*有趣的*部分！

## 运行测试

为了运行测试，您需要找到您的`devto.js`所在的文件夹，并运行以下命令:

`testcafe chrome devto.js`

> 注:可以用自己喜欢的浏览器替换`chrome`。

现在外星人将控制你的电脑，开始做一些疯狂的事情...比如运行你的测试。

如果一切顺利，您应该会在您的控制台上看到类似这样的内容:

```
Using locally installed version of TestCafe.
 Running tests in:
 - Chrome 63.0.3239 / Mac OS X 10.13.2

 DEV Tests
 ✓ Check founder's names

 1 passed (4s) 
```

Enter fullscreen mode Exit fullscreen mode

## 汪！

那是相当多的工作！

但是还有更多好东西要学。

*   同时使用多个浏览器。
*   并行运行测试。
*   重构我们的代码以使用页面对象模型设计模式。

### 我们可以继续下一部分:

[![chrisvasqm](img/e4a5dffbeebdbe975aabe7903c567401.png)](/chrisvasqm) [## 使用 TestCafe | Pararell 执行的 E2E 测试

### 克里斯蒂安瓦斯奎兹 1 月 15 日 182 分钟阅读

#testing #e2e #testcafe #javascript](/chrisvasqm/e2e-testing-with-testcafe--part-2-1lfg)