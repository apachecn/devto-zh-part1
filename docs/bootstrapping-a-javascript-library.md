# 引导 JavaScript 库

> 原文：<https://dev.to/roperzh/bootstrapping-a-javascript-library>

*本帖最初发表于[monades.roperzh.com](https://monades.roperzh.com/bootstrapping-javascript-library/)T3】*

我觉得有必要写这篇文章，因为我读到许多有经验的程序员抱怨 npm 包的质量/数量，而这些抱怨对我没有帮助。这并没有提供问题的解决方案，而是阻碍了新的库作者开始工作，这对整个社区来说是不公平的:想象一下我们失去了多少好的软件包！想象一下，我们失去了多少潜在的 OSS 合作者，因为他们担心会搞砸！

如果你想写你的第一个 JavaScript 库，请不要害怕，在这篇文章中，我会试着带你完成这个过程。

## 哲学

在开始之前，我想澄清一下，指导本指南的主要原则是**简单**。

编写和发布节点包应该是一个轻松的过程，但是使用所有可用的工具来增强您的工作流也是可能的。虽然这些工具非常有用，但它们在开始时会降低您的速度。

前提是**从小**开始，按需添加工具。

## 入门

你需要有 [node.js](https://docs.npmjs.com/getting-started/installing-node) 、`npm`和 Git 的更新版本。在写这篇文章的时候，我正在使用:

*   node.js v8.2.1
*   npm v5.3.0

## 代码组织

所有代码都包含在一个文件夹中:

```
+-- mylib
|   +-- index.js
|   +-- test.js
|   +-- // other files 
```

*   `index.js`文件是你的应用程序的入口点，这是你的库的消费者在`require()` / `import`它时得到的。
*   `test.js`文件包含您的测试。
*   还有其他文件，都与项目的配置和文档相关。

*注意:随着你的项目变得越来越复杂，你可能想用不同的方式组织代码。在这种情况下，惯例是创建一个名为`src`或`lib`的文件夹来包含您的多个源文件，并为您的测试创建一个名为`test`的文件夹。*

现在，继续创建一个文件夹来包含您的项目以及`index.js`和`test.js`文件。

## 创建一个包

每个项目都有一个`package.json`文件，其中包含与 npm 使用的项目相关的元数据，以便识别项目以及处理项目的依赖关系；npm 附带了一个方便的命令(`npm init`)来帮助你以交互的方式生成你的`package.json`。

运行该命令时，系统会提示您填写几个描述项目的值。除了基本的东西(包名、作者等)，还有两件事情需要注意:

*   尽量考虑包的版本控制，如果可能的话，尽量遵循语义版本控制。
*   使您的软件包可用的许可证。杰夫·阿特伍德有一篇关于这个主题的好文章。T2 麻省理工学院的许可是一个很好的选择。

现在，让`test command`和`git repository`条目为空，稍后您将填充它们。这是用`npm init`初始化一个项目的样子:

```
Â§ npm init

This utility walks you through creating a package.json [...]

package name: (mylib)
version: (1.0.0) 0.1.0
description: This is a short description of my library!
entry point: (index.js)
test command:
git repository:
keywords: relevant, keywords, here
author: Roberto Dip
license: (ISC) MIT 
```

如果一切顺利，你应该在项目的根目录下有一个`package.json`文件。

## 版本控制

如果你不确定什么是版本控制，或者为什么你的项目需要它，请阅读这个[教程](https://www.atlassian.com/git/tutorials/what-is-version-control)。

### 忽略文件

> 版本控制适用于人们编辑的文件。生成的文件不应提交给版本控制。例如，不要提交编译产生的二进制文件，如。o 文件或。类文件。也不要犯。从文本格式应用程序生成的 pdf 文件；通常，您应该只提交。生成 pdf 文件。

*迈克尔·恩斯特，[版本控制概念和最佳实践](https://homes.cs.washington.edu/~mernst/advice/version-control.html)*

您通过创建一个名为`.gitignore`的文件来告诉 Git 忽略项目中的某些文件。这个文件包含一系列模式，让 Git 知道哪些文件或文件夹是您不想跟踪的。

如果你懒得打字，这里有[多个](https://github.com/github/gitignore) [模板](https://www.gitignore.io)，但是现在，这已经足够了:

```
# Temporary files created by OSX
*.DS_Store

# Files related to development
node_modules 
```

### 第一次提交

既然你已经取得了进步，开始追踪你的变化是个好主意。首先，初始化您的存储库:

```
Â§ git init
Initialized empty Git repository in projects/mylib/.git/ 
```

现在添加并提交您的文件:

```
Â§ git add .

Â§ git commit -m "Initial commit"

[master (root-commit) 88b12fb] Initial commit
 2 files changed, 18 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 package.json 
```

## 测试

要设置您的测试套件，您需要:

*   执行断言的库。至少目前来看，内置 [`assert`](http://nodejs.org/api/assert.html) 绰绰有余。
*   运行和报告断言的工具。我喜欢摩卡咖啡的简单

注意:如果将来你的测试越来越复杂，你可以切换到 JavaScript 拥有的任何一个强大的测试库。

### 设置摩卡

您唯一需要做的就是通过 npm 安装它:

```
Â§ npm install --save-dev mocha 
```

为了让您的生活更轻松，您可以在键入`npm test`命令时运行 tell npm how to run 您的测试，方法是在您的`package.json`中填充测试脚本:

```
"scripts":  {  "test":  "mocha"  } 
```

*注意:这里没有黑魔法，`scripts`对象允许你用`npm run <command>`定义任意命令，由于测试命令使用如此频繁，npm 允许你省略`run`，直接调用测试命令。*

### 编写你的第一个测试

在您的`test.js`文件中:

```
// Require the assertion library
const assert = require('assert')
// Require you library
const mylib = require('.')

// Describe you library
describe('MyLib', function() {
  describe('#hello()', function() {
    it('should return a string representing a greeting', function() {
      assert.equal(mylib.hello(), "Hello!")
    })
  })
}) 
```

现在，使用您之前定义的命令运行测试:

```
Â§ npm test 
```

如果一切都是正确的，测试就失败了(说起来真奇怪)。这是因为您的库需要导出一个`hello()`方法。打开`index.js`并定义它:

```
module.exports = {
  hello() {
    return 'Hello!'
  }
} 
```

现在测试通过了。这里需要注意的重要一点是，您可以轻松地请求和调用库的方法，从而允许您测试不同的输出。覆盖尽可能多的边缘总是一个好主意，从长远来看，这将使您的代码更加可靠，并且在进行未来的更改时会使您更加自信。

随着您取得更多进展，您应该提交这些更改:

```
Â§ git add .
Â§ git commit 
```

*提示:将`git add .`和`git commit`放在一起并不总是一个好主意。接下来，我鼓励你检查更安全的选择，比如在组合中添加 [`git diff`](https://git-scm.com/docs/git-diff) 和 [`git add -p`](http://johnkary.net/blog/git-add-p-the-most-powerful-git-feature-youre-not-using-yet/) 。*

## 自述

在自述文件中，你让世界知道你的项目是做什么的，如何做贡献，如何使用它，以及你希望你未来的贡献者知道的任何附加信息。[创建自述文件的初学者指南](https://changelog.com/posts/a-beginners-guide-to-creating-a-readme)是了解一份好的自述文件是怎样的一个很好的来源。

虽然你可以自由地用任何格式来写，但标准格式是 [markdown](https://en.wikipedia.org/wiki/Markdown) 。如果你对语法有困难，[这里有一个教程](http://www.markdowntutorial.com/lesson/2/)，这里有一个[完整参考](http://commonmark.org/help/)。

提示: [dillinger.io](http://dillinger.io/) 是一个在线减价编辑器，可以让你实时预览你正在编辑的内容。

*注意:有些项目文件是用大写字母命名的。这是我决定坚持的一个老约定，基本原理是让项目中的新人更容易找到这些文件。*

一旦有了 README.md 文件，不要忘记提交:

```
Â§ git add README.md
Â§ git commit -m "Add a README" 
```

## 出版

### 来源

一旦人们开始使用你的软件包，他们就会发现漏洞、改进、新特性等等。(这就是开源软件的妙处)。因此，向全世界公开您的代码是一个好主意，允许其他人做出贡献。

你可以通过 git 做到这一点，在托管的 Git 服务器上发布你的库，如 [GitHub](https://github.com/) 、 [GitLab](https://about.gitlab.com/) 或 [Bitbucket](https://bitbucket.org/product) 。项目托管在哪里并不重要，您可以随时切换。

为了发布源代码，您需要:

1.  在上述任何服务中创建一个帐户。
2.  创建存储库。
    *   [GitHub 指令](https://help.github.com/articles/creating-a-new-repository/)
    *   [git lab 的说明](https://docs.gitlab.com/ee/gitlab-basics/create-project.html)
    *   [位桶指令](https://confluence.atlassian.com/bitbucket/create-a-git-repository-759857290.html)
3.  获取您的存储库的 URL 并推送您的更改。

```
Â§ git remote add origin [your-url-here]
Â§ git push origin master 
```

既然您已经有了自己的存储库 URL，请在您的`package.json`中填充`git`条目:

```
{  "git":  "[your-url-here]"  } 
```

### npm 上的

虽然出版听起来很吓人，但这个过程可以归结为:

1.  登录(`npm login`)或注册(`npm adduser`)为 npm 用户。
2.  运行`npm publish`发布您的包。
3.  尽情享受吧！

在 [npm 文档](https://docs.npmjs.com/getting-started/publishing-npm-packages)中有更详细的说明。

## 最后的想法

本指南的目的是让您开始开发您的软件包。就发行版而言，根据您想要支持的环境(node.js、浏览器或两者)的不同，这个过程可能会稍有不同。遵循这个指南，你的包就可以在浏览器 node.js **或**上运行了(取决于你如何编写代码)。如果你想支持这两种环境[请查看](https://github.com/umdjs/umd) [查看](http://www.matteoagosti.com/blog/2013/02/24/writing-javascript-modules-for-both-browser-and-node/) [这些](http://www.matteoagosti.com/blog/2013/02/24/writing-javascript-modules-for-both-browser-and-node/) [资源](http://www.richardrodger.com/2013/09/27/how-to-make-simple-node-js-modules-work-in-the-browser/)，如果你还有问题，请随时通过 [Twitter](http://twitter.com/roperzh) 或[电子邮件](mailto:rroperzh@gmail.com)联系我。