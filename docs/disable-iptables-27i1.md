# 禁用 iptables

> 原文：<https://dev.to/adamkdean/disable-iptables-27i1>

而不是在 Ubuntu 14.04 中禁用 iptables，你可以允许一切。

```
iptables -F
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT 
```

Enter fullscreen mode Exit fullscreen mode

很糟糕，但是很有效。