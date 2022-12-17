# 通过 mruby 尝试 seccomp

> 原文：<https://dev.to/udzura/trying-seccomp-via-mruby-6af>

## seccomp？

[`seccomp(2)`](http://man7.org/linux/man-pages/man2/seccomp.2.html) 是一个过滤进程的系统调用的 Linux 系统调用。在 Linux 3.5 之后，他们引入了“seccomp 模式 2”，允许系统通过系统调用号及其参数来过滤系统调用。

这也是基本容器特性之一，例如 [Docker 使用这个](https://docs.docker.com/engine/security/seccomp/)。

它使用 BPF (Berkeley Packet Filter)，也在`libpcap`中使用，尽可能快地过滤系统调用。

## 残片？

[mruby](https://github.com/mruby/mruby) 是 ruby 的实现之一(FYI mruby 是 Matz 创建的，正如 MRI 是 Matz 创立的一样)。它被设计成可嵌入到小工具中，并且比 CRuby 更轻便。

作为嵌入特性的一个副作用，mruby 有非常干净简洁的 C API，所以用它编写 C 绑定/系统程序很容易。在很多方面和 Lua 语言相似。

* * *

我将尝试通过 [libseccomp](https://github.com/seccomp/libseccomp) 使用 seccomp 基本特性，编写 mruby 的 gem(mrbgem)来绑定 libseccomp。

这里是 [`mruby-seccomp`](https://github.com/haconiwa/mruby-seccomp) 。您可以使用 seccomp access 构建一个`mruby`二进制文件，方法是在一些 Linux 中检查这个 repo，然后点击`make`(构建 mruby 本身需要 CRuby、bison 和一些库)。

## 基本用法

在 C 级中，可以通过`seccomp_init(3)`创建 seccomp 上下文，通过`seccomp_rule_add(3)`添加过滤规则，然后通过`seccomp_load(3)`加载到当前进程中。

seccomp 上下文有一个默认操作:
`:kill => SCMP_ACT_KILL, :allow => SCMP_ACT_ALLOW, :trap => SCMP_ACT_TRAP, ...`
然后您可以添加带有 syscall 和 arguments 规范的自定义过滤器操作。

默认使用`:kill`做白名单，使用`:allow`做黑名单。

这是限制`uname(2)`通话的黑名单。用 [mruby-uname](https://github.com/matsumotory/mruby-uname) 建造。

```
context = Seccomp.new(default: :allow) do |rule|
  rule.trap(:uname)
end

context.load
Uname.nodename # Really calls `uname(2)` ! 
```

Enter fullscreen mode Exit fullscreen mode

```
$  ./mruby/bin/mruby /tmp/test.rb 
Bad system call 
```

Enter fullscreen mode Exit fullscreen mode

`Bad system call`暗示`SIGSYS`——你甚至可以诱捕这个。

## 与`fork()/exec()`的组合

加载的 seccomp 的信息在`fork/clone and execve`之后不会改变，正如[一个内核文件](https://www.kernel.org/doc/Documentation/prctl/seccomp_filter.txt)所说。

您可以在`fork()`之后加载一个 seccomp 上下文，然后执行`execve()`，创建一个“沙箱”容器——限制子进程调用指定的系统调用。

```
# fork from https://github.com/iij/mruby-process
# exec from https://github.com/haconiwa/mruby-exec
context = Seccomp.new(default: :allow) do |rule|
  rule.kill(:mkdir, Seccomp::ARG(:>=, 0), Seccomp::ARG(:>=, 0))
end

pid = Process.fork do
  context.load

  puts "==== It will be jailed. Please try to mkdir"
  exec "/bin/sh"
end

p(Process.waitpid2 pid) 
```

Enter fullscreen mode Exit fullscreen mode

```
$  ./mruby/bin/mruby /tmp/jail.rb 
==== It will be jailed. Please try to mkdir sh-4.2$  mkdir /tmp/test1234
Bad system call 
```

Enter fullscreen mode Exit fullscreen mode

这类似于 [Linux 功能](http://man7.org/linux/man-pages/man7/capabilities.7.html)，但是 seccomp 有更细的粒度来控制程序。

## 高级功能

使用 seccomp，我们可以用阻塞的 syscall 信息捕获 SIGSYS(通过 struct `siginfo_t`)。mruby-seccomp 支持这一点。

```
context = Seccomp.new(default: :allow) do |rule|
  rule.trap(:uname)
end
Seccomp.on_trap do |syscall|
  puts "Trapped: syscall #{Seccomp.syscall_to_name(syscall)} = ##{syscall}"
end
context.load

begin
  # Then hit `uname(2)`
  p "nodename: " + Uname.nodename
rescue => e
  puts "Catch as error: " + e.message
  puts "Trapping is OK"
end 
```

Enter fullscreen mode Exit fullscreen mode

```
$  ./mruby/bin/mruby /tmp/trap.rb 
Trapped: syscall uname = #63
Catch as error: uname failed
Trapping is OK 
```

Enter fullscreen mode Exit fullscreen mode

注意:信号处理器将在`exec()`后清洗。所以现在不支持 exec()-be bash 和 trapping `uname(1)` hit。

## 结论

`seccomp`可以限制进程和进程树的系统调用。我们可以用 mruby，mruby-seccomp 和一些关于过程控制的 mrbgems 做一个实验。

BTW seccomp 在 Docker，LXC 和 [Haconiwa](https://github.com/haconiwa/haconiwa#readme) 都有支持。

欢迎将请求拉至 [mruby-seccomp](https://github.com/haconiwa/mruby-seccomp) ！

## 日本原文

我用日语写的原文:

> [http://udzura.hatenablog.jp/entry/2016/11/18/160020](http://udzura.hatenablog.jp/entry/2016/11/18/160020)