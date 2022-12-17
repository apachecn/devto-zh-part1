# 使用 inSpec 安全左移

> 原文：<https://dev.to/mattstratton/shifting-left-securely-with-inspec-6fk>

*(这篇文章最初出现在 [SysAdvent 博客](https://sysadvent.blogspot.com/2017/12/day-2-shifting-left-securely-with-inspec.html) )*

**车·车·你
*车-车-茶***

1.  稳定的状态或性质。
2.  立场坚定。
3.  延续不变；永恒。
4.  抵制变化，尤其是突然的变化或恶化。
5.  坚定不移；如性格或目的的坚定性。

稳定性是我们试图通过减少能改变系统的因素来确保的。实际上，这意味着“部署由具有管理权限的可信个人执行”。

用“职责分离”的方法来解决所有稳定性问题的问题是，每个人都可能犯错误。职位并不意味着绝对正确。每一个认为他们的头衔意味着他们不会犯错的系统管理员都会在几杯酒后告诉我一个他们完全搞砸了生产的故事。

对于一个团队来说，能够理解“操作手册”中的步骤将导致的所有选项和差异也是一个巨大的负担。

我们通常是怎么做的？我们可能有一个 8 次冲刺的项目，但是我们直到第 8 次冲刺才考虑安全性，第 8 次冲刺是“硬化”冲刺。

很自然，我们所有这些强化都失败了，因为我们直到现在才考虑安全性。从一开始就需要考虑安全性和合规性。我们通过工具和文化做到这一点。在这篇文章中，我将主要关注如何利用工具来支持所需的文化变革。具体来说，我将重点使用 inSpec 来验证您的 infracode(在这种情况下，我将使用 Chef，但这也可以与其他 infracode 工具一起使用)。

## 左移

到底什么是“左移”？这个想法是我们将测试在我们的管道中进一步“向左”移动。发现的缺陷越接近引入，修复起来就越便宜和容易。

当然，这也带来了一些挑战，对吗？如果我的测试没有通过，我可以改变测试。我们需要构建我们的系统来防止这种情况。

## 这对安全性有什么帮助？

合规性和安全性只是质量的另一个方面。在我们部署到生产环境之前，只对我们的应用程序进行 QA 测试，这听起来很可笑。等到“强化冲刺”才开始考虑安全性和法规遵从性，这听起来应该同样荒谬。

另一个问题是，通常情况下，我们在最后做的这些测试是繁重的。也就是说，它们是象征性的或字面上的昂贵；它们需要大量资源或昂贵的每席位许可证，所以我们没有像应该的那样经常使用它们。

我们需要更好的东西。

## 试图阻止特定行为是一场必败之战

*   你花时间阻止人们做 x、y 或 z
*   他们做 a、b 或 c 来得到他们想要的结果
*   没有交流发生。洞察力丧失。

不管你怎么努力阻止“如何”，你都应该把注意力集中在“什么”上。考虑结果，而不是导致结果的机制。

## 分布式配置管理的感知问题

当我向系统管理员建议开发人员应该编写 Chef cookbooks 时，这通常是他们认为将要发生的事情:

*   开发者在堆栈溢出上读到禁用`selinux`将使他的节点应用工作得更好
*   开发者更新了他的食谱来禁用`selinux`
*   系统管理员因为 3viL haxx0rz 而被解雇

## 更好的方法

*   开发者在堆栈溢出上读到禁用`selinux`将使他的节点应用工作得更好
*   开发者更新了他的食谱来禁用`selinux`
*   开发人员运行本地测试，包括符合性检查
*   合规检查测试`selinux`的状态
*   测试失败。开发者说“好吧，我想我做不到。”

## 如果开发人员不运行那些本地测试怎么办？

管道接住了它们。

他们下次会做得更好。

即使是拥有高度信任文化的组织也会测试一切。引用罗纳德·里根的一句话——“信任，但要核实”。我甚至不相信*自己*会一直记得测试。记住，我们支持本地测试，但是我们*不指望它*。

## 如果你真的在乎一件事，你会在乎到去写一个测试

很多时候，给出的借口是“我没时间为这个东西写测试”。这是一种逃避，我在这里告诉你，它不会飞。这又回到了用于符合性和测试的轻量级工具的问题上——如果编写这些测试太麻烦，没有人会去做。好消息是，这并不难。

当你写这些测试的时候，想想这个:我们测试的是结果。结果才是最重要的。我们的管道正在创建一个工件(在 infracode 中，这个工件是一个聚合节点)。重要的是结果的状态。你是怎么得到它的这不是问题。我们应该针对工件测试法规遵从性和安全性，并且我们测试的结果是“这个东西是它应该的方式，还是它是一个不应该看到生产的可怕的噩梦”？

## 使你的测试民主化

还记得我说过系统管理员的傲慢吗？Infosec 的人也这么做。工具是需要考虑的最不重要的东西，但是要确保它不是一个只能运行来自安全人员的测试的工具。如果你关心合规性，把这些东西移到左边。如果你的工具做不到这一点，那就该另找工具了。

这并不意味着你需要扔掉现有的东西，而是考虑增加一些东西。您越能在生产中模拟您所关心的测试到您的管道中，您就越高兴。监控只是一个时间维度的测试；这也适用于合规性。

你所做的*而不是*想要做的是在管道中进行一个测试，而这个测试不能由本地开发者来运行。基本上，如果你这样做，你就是一个大吝啬鬼。

## 输入 inSpec

检查是否符合规范。我们编写测试系统状态的控件，并报告这个状态。它也很容易阅读，因为它是用规范风格的语言编写的。考虑在系统上检查 SSH 协议版本的这两种方法之间的区别:

### 外壳

```
> grep "^Protocol" /etc/ssh/sshd_config | sed 's/Protocol //'
 2 
```

###检查

```
control 'ssh-1234' do
  impact 1.0
  title 'Server: Set protocol version to SSHv2'
  desc "
    Set the SSH protocol version to 2\. Don't use legacy
    insecure SSHv1 connections anymore...
  "

  describe sshd_config do
   its('Protocol') { should eq 2 }
  end
end 
```

正如你所看到的，使用 inSpec，不仅仅是人类可读，它还提供了*上下文*。我们理解控件的影响，我们甚至可以添加一个漂亮的描述来解释这是怎么回事。用户体验[2]更好。

## 例子

(这篇文章的所有代码可以在[github.com/mattstratton/sysadvent-2017](https://www.github.com/mattstratton/sysadvent-2017)找到)

### 食谱

我们首先来看一个基本的食谱，它创建了一个用户，并赋予了这个用户对`/var/log`目录的所有权(这可能是我们不希望发生的):

```
user 'sa2017-app' do
  comment 'SysAdvent User'
  system true
  shell '/bin/false'
end

directory '/var/log' do
  owner 'sa2017-app'
end 
```

### 合规概况

我们确认`/var/log`归 root 所有的合规性配置文件如下所示:

```
title 'Directory Tests'

control 'log-1.0' do                        
  impact 0.7                                
  title 'Ownership of /var/log'             
  desc 'The /var/log directory should be owned by root'
  describe file('/var/log') do
    it { should be_directory }
    it { should be_owned_by 'root' }
  end
end 
```

### 绑在一起

在本例中，我创建了一个 Jenkins 管道来响应 cookbook 的 GitHub repo 中的更改。 [Jenkinsfile](https://github.com/mattstratton/sa2017-app/blob/master/Jenkinsfile) 定义了以下阶段:

1.  Lint -对照食谱运行`foodcritic`和`cookstyle`
2.  使用烹饪书本身的测试进行冒烟测试
3.  合规性-运行`kitchen test`，但包括我们的合规性配置文件，加载有一个`git` URL。

为了完成`Compliance`阶段，我们生成一个新的`kitchen.yml`文件，它包含了我们想要包含的测试的路径。还有其他方法来实现这一点，这将取决于你如何建立你的管道。但希望这说明了一种可行的方法。

为了证明这一点，下面是运行合规阶段时 Jenkins 中的输出:

```
Profile: My Compliance (sa2017-compliance)
Version: 0.1.0
Target:  docker://4967508ea0b8f431b161edf561164ab8a49eba780b58ed85673f64c60b3bb8bd

[38;5;9m  ×  log-1.0: Ownership of /var/log (1 failed)[0m
[38;5;41m     ✔  File /var/log should be directory[0m
[38;5;9m     ×  File /var/log should be owned by "root"
     expected `File /var/log.owned_by?("root")` to return true, got false[0m

Profile Summary: 0 successful controls, [38;5;9m1 control failure[0m, 0 controls skipped 
```

[1] -抱歉使用了强烈的语言。

【2】——而且我敢打赌你以为“用户体验”只是前端开发者的事。