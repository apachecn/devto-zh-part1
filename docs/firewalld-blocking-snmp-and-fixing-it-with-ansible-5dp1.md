# 防火墙阻止 snmp 并修复它(使用 Ansible)

> 原文：<https://dev.to/rndmh3ro/firewalld-blocking-snmp-and-fixing-it-with-ansible-5dp1>

前一段时间，我不得不使用一个新的 CentOS 7 虚拟机来做一些与本文无关的事情。这台机器必须一直运行，但偶尔出现的应用服务器故障也会带来一些问题。这就是为什么我决定监控机器，从一些基本的 snmp 监控开始。

当我将主机添加到我的 Icinga 监控中时，Icinga 无法进行 snmpwalk。在对主机进行了一些搜索后，我发现默认的防火墙阻止了访问；直到此刻我才不得不处理的事情。但是我没有简单地关闭防火墙，而是决定允许 snmp 访问。

阅读关于*防火墙*的非常好且全面的 [RedHat 文档](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Security_Guide/sec-Using_Firewalls.html)给了我足够的信息来允许访问主机。它还向我展示了(当时)没有预定义的服务来处理 snmp 流量，所以我必须自己向防火墙添加例外。

这是命令:

```
[root@centos ~]# firewall-cmd --add-port=161-162/udp --zone=public 
```

这意味着使用 UDP 协议的端口范围 161-162 将作为例外添加到公共区域(默认区域)。要了解更多关于区域的信息，请看这里的。

在测试了新规则的有效性之后，我不得不将这些改变永久化。这可以通过在第一个命令
中添加一个*—永久的*来实现

```
[root@centos ~]# firewall-cmd --add-port=161-162/udp --zone=public --permanent 
```

我知道在部署新机器时我必须经常这样做，所以我决定为它写一个简单的 [ansible](//www.ansible.com) -playbook。当然还有一个[模块](https://docs.ansible.com/ansible/firewalld_module.html)来配置*防火墙*！遗憾的是，文档只给出了如何添加单个端口或服务的示例，而没有给出端口范围。但我认为增加一个范围应该也是可能的，所以我就试了一下，而且成功了！下面是任务:

```
- name: add snmp-exception to firewalld
  firewalld: 
    port: 161-162/udp
    permanent: true
    state: enabled 
```

由于 Ansible 是一个开源项目，我决定为它做点贡献，并创建一个 [pull-request](https://github.com/ansible/ansible-modules-extras/pull/114) 来添加缺失的文档和一个示例。这样我就可以帮助 Ansible 社区，而不必深入研究代码本身！