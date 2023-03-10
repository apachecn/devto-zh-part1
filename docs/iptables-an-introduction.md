# Iptables:简介

> 原文：<https://dev.to/cyberomin/iptables-an-introduction>

服务器安全性是应用程序开发中不可忽视的微小元素之一。它可以成就你，也可以毁掉你。因此，对它给予足够的重视再怎么强调也不为过。

在当今世界，黑客们以摧毁完整的服务或数据中心为乐，在配置新服务器或部署到现有服务器时，将这一点放在列表的首位变得非常重要。虽然补丁和安全更新同样重要，但在某些情况下它们不会起太大作用——尤其是在像 DDoS 这样的协同攻击中。

很长一段时间以来，设置应用程序防火墙一直是保护服务器的方法之一。IPTables 是一个定义了一组规则的程序，这些规则显示了什么样的包可以通过服务器的什么样的端口。

#### 什么是 IPTables？

根据维基百科，IPtables 是一个用户空间应用程序，允许系统管理员配置 Linux 内核防火墙(实现为不同的 Netfilter 模块)提供的表及其存储的链和规则。

IPTables 存在于多种风格的 Linux 中。在目前可用的所有开源防火墙中，它是大多数 Linux 发行版(如 Ubuntu、CentOS 和 Fedora)捆绑的标准防火墙。

一个标准的 IPTables 通常分为 3 个单元，也称为链。这些链是输入、转发和输出。

链输入-该链控制传入流量的行为。如果用户正在访问服务器上的网站，IPTables 会尝试将传入的请求与输入链中定义的规则进行匹配。该规则通常会检查目的端口(端口 80)关于 web 流量的信息。

`0 0 ACCEPT tcp -- any any anywhere anywhere tcp dpt:http`

输出——这个链管理每一个输出连接。通常，主机服务器生成的所有流量，例如`ping`请求。

转发-该链控制通过服务器路由的所有网络数据包。

在我们进一步讨论之前，强调 IPTables 的 3p 是很重要的:包、端口和协议。数据包-通过网络传输的数据块。想象一个像邮差送来的邮件一样的包裹。端口-端口是一个逻辑连接点。它有一个数字名称；80、22、443 等。协议-管理设备间数据交换或传输的一组规则。例如:TCP、UDP。

#### 基础知识

要设置规则，我们运行以下命令:

`sudo iptables -A INPUT -i lo -j ACCEPT`

上面的命令是“接受输入链上环回接口上的流量”。环回接口这里是一个虚拟网络接口，计算机使用它与自己通信，因此有术语环回。

要检查 Linux 机器上的环回接口，请运行

`ifconfig lo`。

要为 HTTP 流量设置 iptables 规则，我们将运行

`sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT`

`sudo iptables -A INPUT 3 -p tcp --dport 80 -j ACCEPT`

接受去往目的端口 80 的所有 HTTP(tcp)流量，其中`-p`是协议，`-j`是跳转，`--dport`是目的端口，`-A`是附加端口。

上面的第二条规则看起来几乎和第一条完全一样，唯一的区别是第二条规则指定了规则应该添加到规则链中的位置。这一点非常重要，尤其是当您希望规则按顺序匹配时。

到目前为止，我们已经看到了如何接受特定端口的流量。虽然这很好，但我们设置规则来丢弃不需要的流量也很重要。

`sudo iptables -A INPUT -p tcp --dport 443 -j DROP`

上面的规则说，丢弃所有到达我们服务器的 HTTPS 流量。如果您仔细观察，接受和丢弃流量的规则非常相似，唯一的例外是接受流量使用 ACCEPT，丢弃流量使用 DROP。

虽然 DROP 工作得很好，但在大多数情况下，它有助于向用户反馈正在发生的事情。另一方面，拒绝会降低流量，还会提供有用的反馈。要设置拒绝规则，我们运行

`sudo iptables -A INPUT -p tcp --dport 3306 -j REJECT`

当一个客户端试图访问这个端口时，他们会收到一条消息，表明到端口 3306 的连接被拒绝。这个特殊的规则阻止外部客户端连接到 MySQL 数据库。

查看和删除规则要查看现有的 IPTables 规则，我们将运行`sudo iptables -L -n`或`sudo iptables -L -v`，其中`-v`表示详细。

```
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target prot opt in out source destination

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target prot opt in out source destination

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target prot opt in out source destination 
```

Enter fullscreen mode Exit fullscreen mode

虽然设置规则可能非常有趣，但是有时候你需要删除一个现有的规则；要么是因为你不再需要上述规则，要么是配置错误。有两种方法可以删除规则:

#### 方法 1

`sudo iptables -D INPUT 3`

从输入链中删除第三条规则。这里的编号不是从 0 开始，而是从 1 开始。

#### 方法二

`sudo iptables -D INPUT -P tcp --dport 3306 -J ACCEPT`

通过参数删除完全相同的规则。

虽然这两种方法实现了一个共同的目标——删除规则，但重要的是要注意它们的区别。方法 1 删除了第三条链上的所有规则。在索引不存在的情况下，将返回一条错误消息。方法 2 更加具体，尽管有些冗长。它只针对特定的规则，错误删除另一个规则的风险很小。

使用 Ansible 实现 IPTables 自动化说到配置管理，以幂等和可重复的方式构建系统的能力怎么强调都不为过。

Ansible 是为数不多的简化配置管理、编排和部署的工具之一。它允许您以可预测的方式构建和配置服务器。在花时间通过 IPtables 了解防火墙的基础之后，我们将尝试自动化整个过程。

让我们创建一个可行的剧本来自动化我们的 IPtables 配置。在本行动手册中，我们将接受到以下端口 22(ssh)、80(http)、443(https)和 3306(mysql)的连接，并丢弃不符合此规则的所有其他请求。

如果你是 Ansible 和配置管理的新手，这里有一本很好的入门书——[http://docs.ansible.com/](http://docs.ansible.com/)T2】

```
***
- name: Install iptables Persistent
  apt: name=netfilter-persistent state=present update_cache=true

- name: Set Loop Back Rule
  iptables: chain=INPUT action=append in_interface=lo jump=ACCEPT comment='Accept all loop back traffic'

- name: Set Established Connection Rule
  iptables: chain=INPUT ctstate='ESTABLISHED,RELATED' jump=ACCEPT comment='Let all established connection stay'

- name: Set SSH Port 22 SSH Rule
  iptables: chain=INPUT jump=ACCEPT protocol=tcp destination_port=22 comment='Accept all SSH traffic'

- name: Set HTTP Port 80 HTTP Rule
  iptables: chain=INPUT jump=ACCEPT protocol=tcp destination_port=80 comment='Accept all HTTP traffic'

- name: Set HTTP Port 443 SSL Rule
  iptables: chain=INPUT jump=ACCEPT protocol=tcp destination_port=443 comment='Accept all SSL traffic'

- name: Set HTTP Port 3306 SSL Rule
  iptables: chain=INPUT jump=ACCEPT protocol=tcp destination_port=3306 comment='Accept all MySQL traffic'

- name: Drop Any Traffic Without Rule
  iptables: chain=INPUT jump=DROP comment='Drop traffic for rules that did not match' 
```

Enter fullscreen mode Exit fullscreen mode

*本帖原载于[cyber omin . github . io](http://cyberomin.github.io/devops/2017/01/25/iptables.html)T3】*