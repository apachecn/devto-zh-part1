# Fleetctl 销毁所有服务

> 原文：<https://dev.to/adamkdean/fleetctl-destroy-all-services-2647>

这里有一个使用 fleetctl 销毁所有服务的 bash 小技巧。

```
fleetctl list-units | sed 1d | while read -r line ; \
    do fleetctl destroy $(echo $line | cut -f1 -d ' '); done 
```

Enter fullscreen mode Exit fullscreen mode

它获取一个单元列表，删除第一行，循环遍历，获取第一个单词，然后销毁它。

```
$ fleetctl list-units | sed 1d | while read -r line ; \
    do fleetctl destroy $(echo $line | cut -f1 -d ' '); done
Destroyed Job august-frosting_v4.web.1-announce.service
Destroyed Job august-frosting_v4.web.1-log.service
Destroyed Job august-frosting_v4.web.1.service
Destroyed Job august-frosting_v4.web.2-announce.service
Destroyed Job august-frosting_v4.web.2-log.service
Destroyed Job august-frosting_v4.web.2.service
Destroyed Job quaint-teamwork_v5.cmd.1-announce.service
Destroyed Job quaint-teamwork_v5.cmd.1-log.service
Destroyed Job quaint-teamwork_v5.cmd.1.service
Destroyed Job quaint-teamwork_v5.cmd.2-announce.service
Destroyed Job quaint-teamwork_v5.cmd.2-log.service
Destroyed Job quaint-teamwork_v5.cmd.2.service 
```

Enter fullscreen mode Exit fullscreen mode

现在想象一下用手输入每一个。