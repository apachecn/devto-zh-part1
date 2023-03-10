# 可行部分 2:安装 Telegraf

> 原文：<https://dev.to/just_insane/ansible-part-2-installing-telegraf-32b8>

在本系列的第二部分中，我们将创建一个部署和配置 Telegraf 的剧本，tele graf 是一个流行的 devops 服务，用于将您的系统指标放入时间序列数据库，与 Grafana 等工具一起使用。

一如既往，可以在 这里找到完整行动手册的链接 ~~[。](https://git.justin-tech.com/snippets/13)~~ 

```
---
- hosts: all

  tasks:
  - name: Add telegraf repository
    yum_repository:
      name: influxdb
      description: InfluxDB Repository - RHEL $releasever
      baseurl: https://repos.influxdata.com/rhel/$releasever/$basearch/stable
      gpgcheck: yes
      gpgkey: https://repos.influxdata.com/influxdb.key

  - name: Install telegraf
    yum:
      name: telegraf
      state: latest

  - name: Copy config
    copy:
      src: ./telegraf.conf
      dest: /etc/telegraf/telegraf.conf
      owner: root
      group: root
      mode: 0644
      backup: yes
      force: no
      notify:
      - restart telegraf

  handlers:
  - name: restart telegraf
    service:
      name: telegraf
      state: restarted 
```

这份行动手册比上一份更高级一些，但是，它很好地概述了简单行动手册的结构，以及如何定义多项任务。

下面我们来分解一下。

！请注意，我不会解释本行动手册和上一个行动手册中相同的部分，这些部分可以在此处找到。

首先，我们可以看到有三个任务，并定义了一个处理程序。处理程序是告诉 Ansible 去做`<something>`的一种方式，但是只有当一个任务实际上改变或更新某些东西的时候。在这个剧本中，处理程序告诉 Ansible，如果剧本中的最后一个任务发生变化，就重新启动 telegraf，否则 telegraf 不会重新启动。

其次，我们来看第一个任务。名为“添加 telegraf 存储库”，在这个任务中，我们将找到 Ansible 的一个新组件，名为“yum_repository”，它将向我们的系统添加一个 yum 存储库。在下面，我们将看到用于存储库的变量。这些是添加到。repo 文件，并且基于我们试图添加的存储库。在该文件中，您将看到安装 Telegraf 所需的信息。

第三，在第二个任务中，您将看到我们再次使用 yum 模块来安装系统服务。要了解更多信息，请参阅本系列的前一篇博文。

第四，我们将找到一个名为“复制配置”的任务，正如它听起来的那样，它会将预先制作的 telegraf.conf 文件复制到正确的位置，供 telegraf 使用。让我们进一步了解一下。

该任务还使用了一个名为 copy 的新模块，在它下面，我们可以看到该模块的一些可用变量。前五行非常基本，它们定义了文件的源位置、远程系统上的目的地以及文件的所有者和权限。接下来的三行稍微高级一点。

`backup`告诉 Ansible“创建一个包含时间戳信息的备份文件，这样如果你不知何故不正确地破坏了它，你就可以得到原来的文件”，如果你正在重写程序的配置文件，这是非常重要的，在某些东西损坏的情况下，你可以随时恢复到默认的配置文件，如果有的话。

告诉 Ansible 只将文件复制到远程系统，如果它不存在的话。"默认值是“是”,当内容与源文件不同时，它将替换远程文件。如果选择“否”，则只有在目标不存在的情况下，文件才会被传输。在我的例子中，这个文件一旦在远程系统上就不应该更改，将 force 设置为 no 可以为我节省几秒钟的剧本总运行时间，如果我更改了 conf 文件，我可以很容易地从剧本中更改或删除 force 变量。

这是一个有趣的论点，但对于在 Ansible 中创建一个强大的剧本非常重要。正如我前面提到的，notify 命令可以用来调用处理程序。处理程序可以做很多事情，但是在这个剧本中，我们只使用它们来编程地告诉 Ansible 我们是否想要重新启动 telegraf 服务。如果你想了解更多关于处理器的信息，请点击查看可翻译文档[。](http://docs.ansible.com/ansible/latest/playbooks_intro.html#handlers-running-operations-on-change)

现在，我们将看看行动手册的“处理者”部分。如您所见，处理程序的部分看起来非常类似于一个任务，这是因为它确实如此。它们是只有当你调用它们时才会运行的任务。在我们的示例中，每次运行剧本时重启 telegraf 是没有意义的，因为这会导致我们的指标停机，这通常不是一件好事，并且会导致发出大量警报(您设置了警报，对吗？).

这就完成了我们关于使用 Ansible 设置 Telegraf 的帖子，以及查看比我们之前所做的稍微高级一些的剧本。

附:如果你想看看你能用 Grafana 和系统指标做些什么，试着按一下页面左上角的“仪表板”按钮，或者去~~[https://dashboard.justin-tech.com](https://dashboard.justin-tech.com)~~