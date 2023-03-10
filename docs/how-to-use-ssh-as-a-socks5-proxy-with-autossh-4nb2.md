# 如何将 ssh 作为 socks5 代理与 autossh 一起使用

> 原文：<https://dev.to/mnlwldr/how-to-use-ssh-as-a-socks5-proxy-with-autossh-4nb2>

为了随机化我的 ssh 隧道，我使用了一个小脚本。这个脚本在我的`~/.ssh/config`中查找在`Host`后面带有注释`#socks5`的主机，并挑选出一个。我使用`autossh`来保持连接。

> auto SHS(1):auto SHS 是一个启动 SSH 副本并对其进行监控的程序，如果它死亡或停止传递流量，可以根据需要重新启动它。

```
#!/bin/bash

# ssh user config (http://www.openbsd.org/cgi-bin/man.cgi?query=ssh_config)
SSH_CONFIG=~/.ssh/config

# local port
PORT=8888

# export pid to file
export AUTOSSH_PIDFILE=/tmp/proxy.pid

# array with hosts from SSH_CONFIG.
# append "#socks5" at the end of the Host line.
# Example: "Host yourHost #socks5"
eval HOSTS=( $(awk '/^Host(.*?)#socks5$/ {print $2}' ${SSH_CONFIG}) )
N=1
for index in `shuf --input-range=0-$(( ${#HOSTS[*]} - 1 )) | head -${N}`
do
        echo -en "Host: ${HOSTS[$index]} Port: ${PORT}\n"
        # using autossh - Automatically restart SSH sessions and tunnels
        AUTOSSH_DEBUG=1
        AUTOSSH_GATETIME=0
        autossh -M 20000 -N -D ${PORT} ${HOSTS[$index]}
done 
```

Enter fullscreen mode Exit fullscreen mode

我用[管理员](http://supervisord.org/)自动启动脚本

`/etc/supervisor.d/proxy.ini` :

```
command=/path/to/proxy.sh
autostart=true
autorestart=true
startretries=3
user=YOURUSERNAME
stderr_logfile=/var/log/proxy.log
stdout_logfile=/var/log/proxy.log 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您应用程序支持 SOCKS5 时，您可以将 127.0.0.1:8888 设置为代理