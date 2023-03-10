# 痛击贝壳把戏

> 原文：<https://dev.to/bhaskar_vk/bash-shell-tricks>

这最初是在 2007 年发表在我的博客上的，我很高兴看到它已经很成熟了。直到今天，我仍然发现自己在使用这里提到的许多技巧。

# 快速命令编辑

*   `^orig^repl^`用‘repl’替换先前命令中的‘orig ’,并执行新命令。

# 文件名生成

*   `cp filename{,.bak}`将‘文件名’复制为‘文件名. bak’
*   `mkdir -p dir{1,2,3}/subdir{1,2}`创建`dir1/subdir1`、`dir1/subdir2`、`dir2/subdir1`、`dir2/subdir2`、`dir3/subdir1`和`dir3/subdir2`目录

# 完成

不，我说的不是完整的命令/文件名。我说的是完成各种命令的参数，完成基于应用程序的文件名。这是被吹捧的
‘zsh’的特性之一，但许多人不知道的是，它在 bash 中也是可用的。你需要做的就是安装 [bash-completion](http://www.caliban.org/bash/index.shtml#completion) 。

安装了 bash 完成功能后，您可以使用`TAB`键来完成常见命令的参数，如`rpm, apt, find, grep`等。另外，bash-completion 将通过在您的`$HOME/.ssh/authorized_keys`文件中查找主机来完成`ssh, scp`的主机名
。对于基于`rpm`的发行版，bash-completion 甚至会查找已经安装的包名。

我发现最方便的特性是基于命令上下文的文件名补全。例如，如果您键入`tar -zxvf`，然后按 TAB 键两次，您将仅获得以`.tar.gz or .tgz`结尾的文件列表，而不是目录中所有文件的列表。

# 套接字通信

我打赌很多人都没有听说过这个，但是 bash 确实可以通过`/dev/tcp, /dev/udp`执行基本的套接字通信。这些是 bash 用来与网络套接字通信的伪设备。事实上，如果你做了`ls -l /dev/tcp /dev/udp`，你会得到一个“没有这样的文件或目录”的错误信息。

那么如何使用它们，我们下面我举 2 个例子。

一个快速检查来自 HTTP 服务器的头。这里有一个简单的函数，你可以放在你的`$HOME/.bashrc`中，它将检查 HTTP 服务器的头。

```
headers() {
  server=$1; port=${2:-80}
  exec 5<> /dev/tcp/$server/$port
  echo -e "HEAD / HTTP/1.0\nHost: ${server}\n\n" >&5;
  cat 0<&5;
  exec 5>&-
} 
```

Enter fullscreen mode Exit fullscreen mode

只需通过以下方式调用它

`headers <servername or ip> <port>`

如果没有提供，端口号默认为 80。

第二个例子是检查端口是否打开的快速方法。你总是可以使用 netcat 或 telnet，但我觉得这样更容易。

```
testPort() {
  server=$1; port=$2; proto=${3:-tcp}
  exec 5<>/dev/$proto/$server/$port
  (( $? == 0 )) && exec 5<&-
} 
```

Enter fullscreen mode Exit fullscreen mode

再次调用它

`testPort <servername or ip> <port> <protocol>`

协议可以是 tcp 或 udp，默认为 tcp。

# 算术求值

Bash 可以执行算术运算。他们更容易使用`expr`工具。下面是一些例子。*注意变量名没有前缀$了。*T3】

```
((count++)) #increment value of variable 'count' by one.
((total+=current)) # set total = total+current.
((current>max?max=current:max=max)) # ternary expression. 
```

Enter fullscreen mode Exit fullscreen mode