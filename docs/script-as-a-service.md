# 脚本即服务

> 原文：<https://dev.to/rpalo/script-as-a-service>

我将向你展示我是如何让 Python 脚本在 Ubuntu 上作为服务运行的。我正在做我的一个项目，这是一个推特机器人，它在推特上对人们表示称赞。如果你想了解更多的背景，我不久前写了一篇[的文章](http://assertnotmagic.com/2017/05/01/fanbot-and-doing-new-things-right.html)。基本上，我让这个机器人在一个 Python 脚本中运行，并且我希望这个 Python 脚本运行很长时间。我肯定希望能够启动它并退出我的服务器！所以我去寻找一些选择。

## 选项

1.  永远不要注销服务器。运行`python main.py`并保持外壳打开。优点:很容易做到，而且我已经知道怎么做了。缺点:可能不会长久。我假设 ssh 连接最终会超时。此外，日志记录变成了`python main.py | tee fanbot.log`的一个额外步骤或者类似的事情。总之，感觉 hacky。

2.  `nohup`。如果作业的控制终端关闭，HUP(挂起)信号将发送给任何作业。这方面的一个例子是注销 SSH 会话或关闭打开的终端窗口。这导致它结束并关闭。`nohup`是告诉作业忽略 HUP 信号的命令。我可以做`nohup python main.py &`。&将作业推到后台。然后，作业在后台运行，并将所有输出附加到`nohup.out`。这样你就可以得到一个自定义的日志文件:`nohup python main.py > custom.log &`。优点:我可以注销，我可以在需要的地方获得日志文件，相当简单的命令。缺点:如果服务器重置，就会死亡。我也不确定它能被干净利落地杀死。至少需要几个命令才能正确完成。

3.  使用终端多路复用器。我可以使用类似`tmux`的东西来运行。对于那些不知道`tmux`是什么的人来说，这是一种保存和重用终端窗口、窗格和历史记录的好方法。您可以随时连接和分离 tmux 会话，但是在该会话中运行的程序将保持运行。我将不得不在我要写的清单上增加一个`tmux`概述。优点:使用方便。基本上就像在终端中运行一样。缺点:如果服务器重置，也会死亡。`tmux`仅仅管理这一项工作就感觉有点大材小用。如果你感兴趣的话，下面是事情的经过:

```
$ tmux new -s fanbot_session
$ python main.py | tee fanbot.log
$ <ctrl-b>d # To detach from tmux

# Later, to check on things:
$ tmux attach -t fanbot_session 
```

Enter fullscreen mode Exit fullscreen mode

## 我们以服务为荣

最后我屈服了。我一直在推迟将它作为一项服务来做，因为这听起来很难，很可怕，即使这听起来可能是做事情的“正确”方式。事实证明，没那么糟。但是，我知道你在想什么:少说话，多举例！假设我们有下面的脚本。

```
# /home/ryan/bigben.py import time

def bong():
    print("BONG!  It is now {}".format(time.ctime()))

if __name__ == "__main__":
    while True:
        bong()
        time.sleep(3600) # Tell the time once an hour 
```

Enter fullscreen mode Exit fullscreen mode

有点傻，但这是你想要长时间运行的脚本类型，可能会自动重启，并在以后查看日志。好的。所以我们只需要另外一个文件:服务的单元文件！创建一个名为`bong.service`的文件。的。服务不是真的需要，但我认为有服务很好。

```
; /home/ryan/bong.service 
[Unit]
Description=Bong time telling service
After=multi-user.target

[Service]
Type=idle
ExecStart=/usr/local/bin/python /home/ryan/bigben.py

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

一些解释。`Unit`部分描述了这个服务是什么以及它应该如何运行。`After`变量告诉这个服务何时被允许运行。`After=multi-user.target`本质上仅仅意味着一旦服务器准备好登录，这个服务就可以运行了。`Service`部分描述了`systemd`(服务管理员)将做什么以及如何做。`Type=idle`告诉它只在没有作业运行时才运行我们的服务。`ExecStart`变量是我们希望它运行的命令。注意，绝对路径是必需的。最后，`Install`部分允许我们在引导时自动运行服务。`WantedBy`变量告诉它我们的服务应该在哪个已经自动运行的服务之后启动。

将我们的服务文件复制到 systemd 服务库中，设置权限，加载它，然后让 er rip！

```
$ sudo cp bong.service /lib/systemd/system/bong.service
$ sudo chmod 644 /lib/systemd/system/bong.service
$ sudo systemctl daemon-reload  # Refresh the available service list
$ sudo systemctl enable bong.service

# Now watch your service auto run at bootup
$ sudo reboot
...
$ sudo systemctl status bong.service
# Blah blah blah you should see something happy and green
# Want to check your logs?
$ sudo journalctl -e -u bong.service
# -e scrolls to the end of the logs
# -u bong.service filters by the service that we care about
# OR: sudo journalctl -f -u bong.service to follow, similar to tail -f 
```

Enter fullscreen mode Exit fullscreen mode

原来真的是这样！创建一个服务文件，在正确的目录下弹出，并告诉`systemd`这件事。没我想的那么难！你得到自动重启，健全的日志文件，容易的状态检查。如你所见，这不仅适用于 Python，也适用于任何语言、脚本、程序等。您可以从命令行运行它。它应该可以和 Ruby、Bash、Node 等等一起工作！

## 奖励:虚拟环境

如果你是一个很酷的孩子，你是在虚拟环境中编程 Python。你通常可以像下面这样手工运行你的脚本:

```
$ python -m venv .venv  # Creating a virtual environment in .venv/
$ source .venv/bin/activate
(.venv) $ which python
/home/ryan/.venv/bin/python
(.venv) $ python bigben.py
... 
```

Enter fullscreen mode Exit fullscreen mode

你会兴奋地问我，如何让服务在你的虚拟环境中运行？这比你想象的要简单！还记得我们`bong.service`文件中的这一行吗？

```
ExecStart=/usr/local/bin/python /home/ryan/bigben.py 
```

Enter fullscreen mode Exit fullscreen mode

只需将 python 路径指向您的虚拟环境。它也会自动从那里获取已安装的软件包。

```
ExecStart=/home/ryan/.venv/bin/python /home/ryan/bigben.py 
```

Enter fullscreen mode Exit fullscreen mode

希望这篇文章能给你提供一个有用的服务。(Womp womp womp)

* * *

*最初发布于[我的博客](http://assertnotmagic.com)T3】*