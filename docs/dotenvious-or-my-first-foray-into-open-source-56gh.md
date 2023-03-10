# 或者说，我第一次尝试开源

> 原文：<https://dev.to/farisj/dotenvious-or-my-first-foray-into-open-source-56gh>

## 昏庸；或者，我第一次尝试开源

作为一名软件工程师，我每天都依赖开源软件。开源软件(以下简称 OSS)描述了一种通常由社区驱动的免费使用的技术。开放源码软件的常见例子是 Ruby on Rails 或 ReactJS，但 OSS 覆盖面很广——甚至操作系统(如 Linux、Ubuntu)也可以是开放源码的。

由于我每天都在使用由全球成千上万人编写的软件，我想我应该深入了解一些软件是如何工作的，希望有一天我能有所贡献。然而-这是一个很好的意图，但我也需要一个开源项目的想法！

我记得留意任何我可能用自己的开源项目解决的问题/项目，于是我回去做了一名软件工程师。在我的工作中，我主要用 Ruby on Rails 和 EmberJS 构建 web 应用程序。我创建自己的开源项目的最佳选择是找到一个我经常遇到的问题，并尝试根据我的需要提出一个定制的解决方案。

### 我的开源缘起故事

在我们最大的 Rails 应用程序中，我们依赖一个包含各种敏感环境变量的`.env`文件。在生产环境中，这些通常是指向我们域中其他应用程序的身份验证凭证或变量。由于我们是一家快节奏的公司，业务需求不断发展，工程师可能会在开发功能时随时向该文件添加变量。一般来说，如果需要一个新的环境变量，工程师会在一个名为`.example-env`的文件中提交一个假的例子。该文件用于在我们的团队中共享领域知识，并帮助确保每个人的开发环境是一致的。

随着这个`.example-env`文件的不断变化，我发现自己总是在反复检查我的`.env`文件，以确保它与`.example-env`保持同步。这是一个手工任务，我注意到我的团队中的人们经常忘记做，并且发现他们自己有一个破碎的开发环境，没有任何原因。

因此，我对开源项目的想法诞生了:创建一个 CLI 来帮助管理我们各种环境变量设置之间的差异。

我决定将这个项目命名为 Dotenvious，因为它是管理我们当前`.env`流程的一个扩展——一个叫做`Dotenv`的宝石。明白了吗？我的`.env`文件“嫉妒”它的同类 git 提交的示例文件，它们总是有必要的变量。

我意识到有一些其他的开源 gem 可以做到这一点，但是我喜欢这个名字，我想进一步了解 gems 和开源，所以我决定为了自己的利益继续这个过程。

### 宝石如何工作

要创建一个新的宝石，最简单的方法是安装[捆绑器](http://bundler.io/)并运行`bundle gem my-new-gem`。这将创建一个名为`my-new-gem`的目录，并创建一些子目录和重要文件。你的目录可能看起来像:

```
bin/
lib/
spec/
.gitignore
Gemfile
LICENSE.txt
my-new-gem.gemspec 
```

那里也会有一些其他的文件。我想提到的一个关键文件是`gemspec`文件。这个文件包含了很多元数据和关于你的 gem 应该如何编译的信息。你可以在这里查看更多关于如何使用捆绑器[开发宝石的信息，在这里](https://bundler.io/v1.15/guides/creating_gem.html)查看更多关于[的信息。](http://guides.rubygems.org/make-your-own-gem/)

典型的开发过程包括修改你的 gem 的`lib`或者`bin`文件夹(或者任何你通过`.gemspec`包含的文件)。随着 gem 的开发，你将不得不重新编译并重新安装 gem 到你的控制台中

```
gem build my-new-gem.gemspec //=> compiles my-new-gem-0.0.x.gem
gem install my-new-gem-0.0.x.gem //=> Installs updated gem 
```

从我个人使用公司大型 Rails 应用程序的经验来看，我发现我在检查`.example-env`文件时做了两件真实的事情:添加新变量和随着约定的改变更新变量。

考虑到这两个用例，我想创建一个 CLI，在这里可以迭代`.example-env`文件中的不同变量。

### 与昏庸打交道

当您在终端运行`dotenvious`时，多特将比较`.env`和`.example-env`(默认)并评估它们是否有任何差异。如果有任何差异，宝贝欠条会提示您进行补救:

```
You have missing ENV variables. Examime them? [y/n] 
```

当 CLI 引导用户了解其发现的差异时，它会提示用户添加一个新变量或覆盖一个具有不同值的变量:

```
MY_SECRET_KEY=secret
Add to .env? [y/n/q] 
```

或者

```
ENV[MY_AUTH_TOKEN] is set to: the0dev8token
Example [MY_AUTH_TOKEN] is set to: example-dev-token
Replace with the example value? [y/n/q] 
```

当 Dotenvious 引导用户遍历示例文件中的每个变量时，他们会看到上述消息的一个变体。完成后，Dotenvious 将按键对变量集合进行排序，保持有序，以防用户想要手动编辑文件。

它还会读取存储库中的一个`.envious`文件，看起来类似于:

```
Dotenvious::Configuration.new do |config|
    config.example_file = '.env.example'
    config.custom_variables = ['MY_LOCAL_VAR']
    config.optional_variables = ['GOOGLE_AUTH_TOKEN','OTHER_AUTH_TOKEN']
end 
```

加载时，Dotenvious 将检查该文件是否存在，如果存在，则加载任何存在的定制。如果设置了`example_file=`，Dotenvious 将检查该文件作为示例文件与`.env`进行比较(默认为`.example-env`)。此外，`custom_variables`将允许白名单中的变量与示例文件中的设置不同(如果它们不同，它不会提示用户)。第三，它也不会提示用户输入`optional_variables`。

### 我的第一个功能请求

我向我的同事们宣布了这个非常有价值的项目，它很受欢迎！就开源项目的第一次迭代而言，我收到的总体反馈是积极的——它解决了我的同事们遇到的问题，他们不得不浏览`.example-env`文件寻找新添加/更改的变量，并手动将其复制到他们的`.env`文件中。Dotenvious 为此提供了一个更加自动化的解决方案，同时仍然让用户完全控制这两个文件之间的关系。

一天，不同团队的一位同事问了我一个问题。Dotenvious 能处理 YML 文件吗？他们在一个我没有使用的存储库中做一个项目，试图让他们的 CircleCI 设置正常工作。作为实验，他试图将一个`.yml`文件中的一些变量导入到他的开发环境中。

当时，不，Dotenvious 不支持`.yml`文件。这意味着我收到了我的第一个功能请求！

由于 Dotenvious 内部的性质，这实际上是一个非常容易做的加法！简单地移动几行代码，我就可以很快地添加这个特性。

### 未来的工作

我对未来充满希望。我希望扩展 CLI 的功能，使其能够动态地向存储在`.envious`中的可选或自定义白名单添加变量。另一个要求我做的功能是将变量从`.env`直接导出到终端。(这可能很危险，所以我一直犹豫要不要这么做。)

无论如何，我构建 Dotenvious 的经验对我了解我欣赏或经常使用的其他开源项目的开发过程非常有帮助。这里希望另一个开源项目将在我的未来！:D