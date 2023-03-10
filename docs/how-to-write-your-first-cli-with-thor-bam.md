# 如何用 Thor 写你的第一个 CLI

> 原文：<https://dev.to/fbzga/how-to-write-your-first-cli-with-thor-bam>

在 Magnetis，我们正在将云基础设施迁移到 Kubernetes。为此，我开始在我们的应用程序中创建一些 shell 脚本。它没有问题，但是当我得到 5 个不同的 shell 脚本文件时，它开始变得混乱。在我们招聘过程中的一次结对编程会议上，一名候选人选择 Thor 来编写测试项目。这是有组织的，似乎很容易开始。所以把这些点联系起来，我想我们的剧本可能会更有雷神的表现力。命令行已经成为我们日常生活的一部分:git、bundler 和 rails 等等。创建一个有助于您组织和更好地共享日常工作的工具。
在这篇文章中，我们将从《绝命毒师》的一个精彩场景中获得灵感，写一个简单的例子。但是除了简单之外，为你想要的任何东西创建一个 CLI 也是同样的过程。

## 创造新的宝石

让我们从创建一个新的 gem 开始，这将使我们的软件更容易分发，在这个例子中是可执行文件。为了创造一个新的宝石，bundler 有一个方便的命令(bundler 本身是用 Thor 制作的):

```
$ bundle gem --exe walter
This will generate a skeleton as follows:
 create walter/Gemfile
 create walter/lib/walter.rb
 create walter/lib/walter/version.rb
 create walter/walter.gemspec
 create walter/Rakefile
 create walter/README.md
 create walter/bin/console
 create walter/bin/setup
 create walter/.gitignore
 create walter/.travis.yml
 create walter/.rspec
 create walter/spec/spec_helper.rb
 create walter/spec/walter_spec.rb
 create walter/LICENSE.txt
 create walter/exe/walter 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要将 Thor 作为一个依赖项添加到我们的项目中，打开 walter.gemspec 并添加:

`spec.add_dependency "thor", "~> 0.20"`

我们还需要删除 gemspec 文件中的 TODO 项，因此继续编辑 summary、description 和 homepage 的值。运行 bundle install 命令，就可以编写一些 Thor 行了。
输入 Thor
打开 lib/walter.rb，让我们添加一些代码，首先你需要要求并继承 Thor:

```
require 'walter'
require 'thor'
module Walter
 class CLI < Thor
   # ...
 end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们添加一个简单的 hello world:

```
class CLI < Thor
  desc "hello world", "my first cli yay"
  def hello
    puts "Hello world"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

加油，2017 你好世界？让我们打个招呼海森堡！

```
class CLI < Thor
  desc "hello [name]", "say my name"
  def hello(name)
    if name == "Heisenberg"
      puts "you are goddman right"
    else
      puts "say my name"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们就要到了，最后一件事是从二进制文件中调用我们的 lib。我们用来创建 gem 的 exe 指令在我们的项目中生成了一个 walter 二进制文件。您需要做的第一件事是更改权限，以便我们可以实际执行该文件:

`chmod +x exe/walter`

现在，我们可以称之为:

`$ bundle exec exe/walter`

这不会产生任何输出，但是不要担心。这是意料之中的。在您最喜欢的编辑器中打开 exe/walter，让我们添加以下内容:

`Walter::CLI.start(ARGV)`

我们调用刚刚创建的 CLI，并传递收到的参数。现在我们再调用一次:

```
$ bundle exec exe/walter
  walter hello [name]    # say hello
  walter help [COMMAND]  # Describe available commands or one specific command 
```

Enter fullscreen mode Exit fullscreen mode

Thor 列出这个命令行界面所有可用的命令，太棒了。
如果您尝试调用我们的 hello 命令而不使用名称:

```
$ bundle exec exe/walter hello
ERROR: “walter hello was called with no arguments
Usage: “walter hello [name]” 
```

Enter fullscreen mode Exit fullscreen mode

是，它告诉我们如何使用它。在 shell 脚本中实现这一点需要一些条件，但是 Thor 内置了这些条件。

```
$ bundle exec exe/walter hello Heisenberg
you are goddman right. 
```

Enter fullscreen mode Exit fullscreen mode

## 安装和发布

要将 gem 安装到您的系统中，您可以从任何地方安装它:

```
$ rake install 
```

Enter fullscreen mode Exit fullscreen mode

调用它:

```
$ walter hello Jesse
say my name 
```

Enter fullscreen mode Exit fullscreen mode

如果你想把它释放给 rubygems，只需运行:

```
$ rake release 
```

Enter fullscreen mode Exit fullscreen mode

Ruby 的生态系统很美，不是吗？

## 结论

托尔让日常剧本更有表现力和意义。对于一个已经将 Ruby 作为官方语言的团队来说，这是一个很好的补充，因为所有的团队成员都可以让您的命令变得更好。在我们的例子中，它被用来创建命令来启动 kubernetes 集群，并为暂存环境部署我们的应用程序。您可以使用来自动化您公司的一些内部流程或工作流！
最后，给你一些提示:

*   尽可能使用其他 CLI 通用的命令方法:init、status、new、create、update。
*   尝试将尽可能少的方法公开为 CLI，这样就可以封装只在代码中需要的东西。
*   如果你需要写一些配置文件，使用`$HOME/.<yourcli>`

如果你想更深入地了解这个主题，这里有一些官方文件。
快乐雷神！

先前[在媒体](https://medium.com/magnetis-backstage/how-to-write-your-first-cli-with-thor-9da6636bf744)发布