# 阻止 SSH 暴力攻击

> 原文：<https://dev.to/realflowcontrol/block-ssh-brute-force-attacks-1n1o>

…或者至少减慢它们的速度(在 Linux 上使用 iptables)

当服务器的端口 22 对互联网开放时，您会发现来自各种来源的登录尝试的数量是无穷无尽的。其中 99%都只是[蛮力](https://en.wikipedia.org/wiki/Brute-force_search)或[字典](https://en.wikipedia.org/wiki/Dictionary_attack)攻击。

因为我不希望我的日志文件因为所有这些失败的登录而溢出，所以我寻找一个解决方案来阻止它们或者至少减慢它们的速度。

下面是我做的(感谢 [@baptistecs](https://dev.to/baptistecs) 漏了`ACCEPT`加了 IPv6)

```
# cleanup
iptables -F
iptables -X SSH_CHECK

ip6tables -F
ip6tables -X SSH_CHECK

# set rules
iptables -N SSH_CHECK
iptables -A SSH_CHECK -m recent --set --name SSH
iptables -A SSH_CHECK -m recent --update --seconds 60 --hitcount 2 --name SSH -j DROP
iptables -A SSH_CHECK -m recent --update --seconds 3600 --hitcount 10 --name SSH -j DROP
iptables -A SSH_CHECK -p tcp --dport 22 -j ACCEPT # accept packet if not previously dropped

ip6tables -N SSH_CHECK
ip6tables -A SSH_CHECK -m recent --set --name SSH
ip6tables -A SSH_CHECK -m recent --update --seconds 60 --hitcount 2 --name SSH -j DROP
ip6tables -A SSH_CHECK -m recent --update --seconds 3600 --hitcount 10 --name SSH -j DROP
ip6tables -A SSH_CHECK -p tcp --dport 22 -j ACCEPT # accept packet if not previously dropped

iptables -A INPUT -p tcp -s 127.0.0.1 -j ACCEPT # whitelist your IP (replace 127.0.0.1)
iptables -A INPUT -p tcp --dport 22 -m state --state NEW -j SSH_CHECK # jump from INPUT to SSH_CHECK

ip6tables -A INPUT -p tcp -s ::1/128 -j ACCEPT # whitelist your IP (replace ::1/128)
ip6tables -A INPUT -p tcp --dport 22 -m state --state NEW -j SSH_CHECK # jump from INPUT to SSH_CHECK 
```

Enter fullscreen mode Exit fullscreen mode

我正在创建一个名为`SSH_CHECK`的新链，我将在端口 22 上收到的处于 new 状态的每个 TCP 包(也称为 syn 包)传递给这个链。源 IP 地址会被记录下来，如果同一地址在最后一分钟出现两次或更多次，或者在最后一小时出现十次或更多次，则该数据包会被丢弃。

我还添加了一条规则，如果源 IP 地址等于 127.0.0.1 / ::1/128，就绕过`SSH_CHECK`链(用您的静态 IP 替换它，或者直接删除那一行)。

老实说，我并不是在阻止暴力攻击，但攻击者(或攻击脚本)只能每分钟尝试一个密码或每小时尝试九个密码。

最后:这通常是一个好建议，在您的 ssh 服务器中关闭密码认证以及 root 登录。将以下内容添加到您的`/etc/ssh/sshd_config`文件中，或者将其改为如下所示:

```
PasswordAuthentication no
PermitRootLogin no
PubkeyAuthentication yes 
```

Enter fullscreen mode Exit fullscreen mode