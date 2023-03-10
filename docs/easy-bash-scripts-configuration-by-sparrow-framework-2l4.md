# Sparrow 框架简单的 Bash 脚本配置

> 原文：<https://dev.to/melezhik/easy-bash-scripts-configuration-by-sparrow-framework-2l4>

Bash 在脚本配置方面并不容易。我马上想到了几个案例:

*   将嵌套的类似散列的结构作为输入参数传递
*   通过 JSON/YAML 等声明性语言进行配置

这在 Bash 中很难做到，除非你使用 Sparrow 来开发 Bash 脚本。

以下是一些偶然的例子来证明。

# 安装麻雀

照例去配:

```
$ cpanm Sparrow --notest --q 
```

Enter fullscreen mode Exit fullscreen mode

# 开发 Bash 脚本

根据 Sparrow 规则，它应该是名为“story.bash”的文件。让我们做一些有用的事情，例如回显一个输入参数:

```
$ nano story.bash
#!/bin/bash
message=$(config message)
echo you say $message 
```

Enter fullscreen mode Exit fullscreen mode

然后运行脚本:

```
$ strun --param message="hello world" 
```

Enter fullscreen mode Exit fullscreen mode

输出将是:

```
2017-11-04 22:27:17 :  [path] /
you say hello world
ok      scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，它看起来并不令人印象深刻，我们可能已经用一个“non-sparrow”Bash 脚本做了同样的事情:

```
$ nano hello.bash
#!/bin/bash
echo you say $1 
```

Enter fullscreen mode Exit fullscreen mode

然后:

```
$ bash hello.bash "hello world" 
```

Enter fullscreen mode Exit fullscreen mode

然而，让我们继续前进，麻雀的优势很快就会显现出来...

# 定义默认值

比方说，我们想要为配置参数定义默认值。

在 Sparrow 中，你只需创建一个名为“suite.yaml”的文件就可以做到这一点:

```
$ nano suite.yaml

message: Hello Sparrow 
```

Enter fullscreen mode Exit fullscreen mode

然后:

```
$ strun 
```

Enter fullscreen mode Exit fullscreen mode

输出将是:

```
2017-11-04 22:34:50 :  [path] /
you say Hello Sparrow
ok      scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

# 嵌套参数

让我们把事情变得复杂一点，假设我们想要在复杂的结构中传递“消息”参数:

```
{
   "user" : {
      "id" : "Bash" 
      "message" : "Hello Bash"
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您将需要对代码进行微小的修改来接受这个新的需求:

一个用于 Bash 脚本:

```
$ nano story.bash
#!/bin/bash
user_id=$(config user.id)
message=$(config user.message)
echo $user_id say $message 
```

Enter fullscreen mode Exit fullscreen mode

一个用于默认配置:

```
$ nano suite.yaml
user:
  id: Sparrow 
  message: Hello Sparrow 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们试一试。

*   使用默认参数:

```
$ strun
2017-11-04 22:42:13 :  [path] /
Sparrow say Hello Sparrow
ok      scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

*   通过一些用户的输入:

```
$ strun --param user.id=Bash --param user.message="Hello Bash"
2017-11-04 22:43:01 :  [path] /
Bash say Hello Bash
ok      scenario succeeded
STATUS  SUCCEED 
```

Enter fullscreen mode Exit fullscreen mode

我们只是添加了一些代码，它仍然是纯粹的 Bash，但是我们设法处理嵌套的、类似散列的结构，非常简洁！:)

顺便说一下，嵌套结构的层次是不受限制的，一个想象的例子是这样的:

```
$ strun --param user.language.dialects=Shell --param user.message="Hello Shell" 
```

Enter fullscreen mode Exit fullscreen mode

等等...

# 文档

如果您到了这里，我假设您对这个主题感兴趣，要深入研究细节——使用 Sparrow SDK 的非常全面的文档，名为[outhentic](https://github.com/melezhik/outthentic)。在这里，您会发现许多如何创建和配置 Bash(不仅仅是 Bash)脚本的例子和方法。

# 麻雀外挂

假设我们想将 Bash 脚本作为名为“用户问候”Sparrow 插件上传，以便其他人可以使用它。不出所料，Sparrow 插件“继承”了我们到目前为止看到的相同的配置方式。以下是一些例子:

*   作为命令行参数:

```
$ sparrow plg install user-greeting
$ sparrow plg run user-greeting --param user.id=Bash --param user.message="Hello Bash" 
```

Enter fullscreen mode Exit fullscreen mode

*   作为麻雀任务:

```
$ sparrow plg install message
$ sparrow project create utils
$ sparrow task add utils greeting user-greeting
$ sparrow task ini utils/greeting

user:
  id: Ruby 
  message: Hello Ruby

$ sparrow task run utils/greeting 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以部分覆盖默认配置*，这种方式:* 

```
$ sparrow plg run user-greeting --param user.id=Corbie 
```

Enter fullscreen mode Exit fullscreen mode

还是这个:

```
$ sparrow task ini utils/greeting

user:
  id: Ð¡orbie 

$ sparrow task run utils/greeting 
```

Enter fullscreen mode Exit fullscreen mode

或者甚至是那样:

```
$ sparrow task ini utils/greeting --param --param user.id=Raven 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

Sparrow 为脚本开发人员提供我们的配置需求。只需添加最少的代码，就可以轻松地为 Bash 脚本构建灵活的配置。*