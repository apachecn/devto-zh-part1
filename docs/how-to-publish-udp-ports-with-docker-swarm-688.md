# 如何用 Docker-Swarm 发布 UDP 端口

> 原文：<https://dev.to/ophasnoname/how-to-publish-udp-ports-with-docker-swarm-688>

我相信你知道这些要求，你会想:嘿，没问题，我已经做了上千次了，给我 5 分钟，它就会工作。

上周也发生了同样的事情，当一个来自 [zisops](https://zenner-iot.com) 的队友要求我为一个已经运行的 Docker Swarm 服务发布一个 UDP 端口。对于“正常的”码头集装箱，我已经这样做过 100 次了。但是嘿..Docker Swarm 无法处理以下问题:

```
-p 500:500/udp 
```

好的下一步:RTFM 但是嘿..没有关于为服务发布 UDP 端口的信息。下一次尝试:开始谷歌..找到了，在莫比项目的 Github 问题中找到了解决方案。所以，如果你不得不在 Docker-Swarm 上使用 UDP，请记住这篇小文章，并给我买杯啤酒:-)

***解***

```
docker service create --name my_service \
    --publish mode=host,published=500,target=5004,protocol=udp \
    my_image 
```

德语原帖:[https://www . geek pub . de/2017/10/docker-swarm-UDP-port-freigeben-fuer-einen-service/](https://www.geekpub.de/2017/10/docker-swarm-udp-port-freigeben-fuer-einen-service/)