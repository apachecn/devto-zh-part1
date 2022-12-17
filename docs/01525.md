# 通过 kinesis 消防水管将普罗米修斯记录备份到 s3

> 原文：<https://dev.to/shirou/back-up-prometheus-records-to-s3-via-kinesis-firehose-54l4>

因为[普罗米修斯](https://prometheus.io/)没有进行长期存储，它会在适当的时间间隔被擦除。对于长期数据存储，它将被保存为 influxdb。然而，AWS S3 更容易管理。

[AWS Kinesis firehose](https://aws.amazon.com/kinesis/data-firehose/?nc1=h_ls) 已于 2017 年 7 月来到东京地区。通过使用 kinesis 消防软管，我们可以自动保存 S3 的记录。

所以我实现了 prometheus 的远程写适配器集成，它可以向 Kinesis 发送记录。

[https://github.com/shirou/prometheus_remote_kinesis](https://github.com/shirou/prometheus_remote_kinesis)

这只是为了评估，并没有部署到生产，可能会有一些问题。

## 普罗米修斯 _ 远程 _ 动力

### 如何使用

有必要用 go 来构建，但我省略了。使用 Docker 很容易实现多阶段构建。

```
 $ prometheus_remote_kinesis -stream-name prometheus-backup 
```

Enter fullscreen mode Exit fullscreen mode

-s tream-name

kinesis stream name (required)

-l isten-addr

listen address. If not specified `:9501`.

当然，您应该设置 AWS 凭据。

我还把它放在了 docker hub 里。

[https://hub.docker.com/r/shirou/prometheus_remote_kinesis/](https://hub.docker.com/r/shirou/prometheus_remote_kinesis/)

应该从这样的感觉开始。

```
 docker run - d -name remote_kinesis \
    --restart=always \
    -p 9501:9501 \
    -e STREAM_NAME=backup-prometheus-log \
    shirou/prometheus_remote_kinesis 
```

Enter fullscreen mode Exit fullscreen mode

## 设置在普罗米修斯一方

如下设置`prometheus.yml`的远程写入设置。在 url 前添加一个`-`使其成为一个序列是很重要的。

```
 remote_write:
   - url: http://localhost:9501/receive 
```

Enter fullscreen mode Exit fullscreen mode

省略了身势和身势正身的设定。

设定就以上结束了。随着时间的推移，s3 中生成的日志越来越多。

# JSON 格式

发送给 kinesis 的数据被做成这样的 JSON 格式。

```
 {
   "name" : "scrape_duration_seconds" ,
   "time" : 1513264725773 ,
   "value" : 0.004345524 ,
   "labels" : {
     "__name__" : "scrape_duration_seconds" ,
     "instance" : "localhost: 9090" ,
     "job" : "prometheus" ,
     "monitor" : "monitor"
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

在普罗米修斯的时间序列中，一个记录可以存储多个样本。但是，由于我不想增加层次结构，所以我将其展平，以便为每个样本创建一个记录。因为不可能真正标记，所以它在地图上。此外，它假设使用雅典娜或 S3 选择，它使它与新的路线(JSON-LD)。我试着用 gzip 压缩发送到 kinesis，但是我现在删除了，因为我的 t2.small 占用了太多的 CPU。此外，它每 500 条记录发送一次。因为它会被缓冲，所以如果遥控死亡，它可能会丢失。尽管如此，还是实现了正常关机。

由于来自 prometheus 的写请求带有 snappy compression + protobuf，这可能是以其字节序列原样转移到 kinesis 的最快方式，但这将很难在以后处理它。

# 总结

我创建了远程存储集成，通过 AWS Kinesis 保存到 s3，用于 prometheus 的长期日志预设。

我还没有部署到生产环境中，所以可能会有问题。另一个问题是阅读 S3 的作品。但它只是一个普通的 JSON 格式，我认为如果需要的话很容易转换。虽然可以通过远程读取存储集成从 promehteus 中读取目录，但是性能可能不好。

哦，[羊驼日本](https://www.alpaca.ai/ja/)正在招募广受好评的人来做这项工作。请联系 Twitter `@r_rudi`