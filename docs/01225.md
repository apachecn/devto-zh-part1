# DNS 基里希多:用于安全监控的被动 DNS 查询/回复日志工具

> 原文：<https://dev.to/mizutani/dns-gazer-4c8n>

本帖是对 [**DNS gazer**](https://github.com/m-mizutani/dns-gazer) 的介绍。DNS 基里希多是一个被动的 DNS 数据包分析和安全监控记录工具。从安全操作的角度来看，DNS 查询和回复日志对于检测和调查安全事件非常重要。入侵系统的攻击者使用一个独特而奇怪的域名。这些域名通常被共享为 IOC(泄露指示器)。然后，

*   安全操作员可以通过比较出现在内部 DNS 查询/回复日志和共享日志中的域名来检测恶意软件感染、特洛伊木马活动等。
*   一些恶意软件使用 DNS 查询与 C2(命令和控制)服务器通信。安全操作员可以在事件响应中找出与 C2 服务器通信的主机。

对于收集 DNS 查询和回复日志，DNS 基里希多有以下优势。

*   不仅将日志存储到本地文件，还将日志连续发送到 [fluentd](https://www.fluentd.org/) 。
*   支持包括查询+回复的“事务日志”和从 DNS 数据包中提取的作为每个 DNS 部分(查询、回复等)的单独记录的“记录日志”。)

# 入门

### 用例 1:将 DNS 事务日志从网络接口`eth0`发送到`localhost`上的 fluentd 和端口`24224`

```
$ dns-gazer -i eth0 -f localhost:24224 
```

Enter fullscreen mode Exit fullscreen mode

然后，fluentd 收到以下日志。

```
2017-12-21 17:58:00.000000000 +0900 dns-gazer.dns.tx: {
  "client_addr": "10.139.96.169",
  "client_port": 53684,
  "query": [
    {
      "name": "bf-pro-front.cloudapp.net.",
      "section": "question",
      "type": "A"
    }
  ],
  "query_ts": 1444531212.628222,
  "reply": [
    {
      "name": "bf-pro-front.cloudapp.net.",
      "section": "question",
      "type": "A"
    },
    {
      "data": "23.100.102.231",
      "name": "bf-pro-front.cloudapp.net.",
      "section": "answer",
      "type": "A"
    }
  ],
  "reply_ts": 1444531212.643494,
  "server_addr": "210.196.3.183",
  "server_port": 53,
  "status": "success",
  "tx_id": 23904
} 
```

Enter fullscreen mode Exit fullscreen mode

(为了可读性，印刷得很漂亮)

### 用例 2:从`eth0`到 stdout 打印 DNS 事务日志

```
$ dns-gazer -i eth0 -t - 
```

Enter fullscreen mode Exit fullscreen mode

然后，将以下日志输出到 stdout。

```
2015-10-11T02:40:12+00:00   dns-gazer.dns.tx    {"client_addr": "10.139.96.169", "client_port": 53684, "query": [{"name": "bf-pro-front.cloudapp.net.", "section": "question", "type": "A"}], "query_ts": 1.44453e+09, "reply": [{"name": "bf-pro-front.cloudapp.net.", "section": "question", "type": "A"}, {"data": "23.100.102.231", "name": "bf-pro-front.cloudapp.net.", "section": "answer", "type": "A"}], "reply_ts": 1.44453e+09, "server_addr": "210.196.3.183", "server_port": 53, "status": "success", "tx_id": 23904} 
```

Enter fullscreen mode Exit fullscreen mode

### 用例 3:不仅打印 DNS 事务日志，还打印从`eth0`到 stdout 的“记录日志”

“记录日志”是指每个 DNS 记录的日志。dns-gazer 通过`-R` optoin 将一个 dns 查询/回复拆分为多个记录。记录日志用于基于非嵌套字典数据类型的日志管理系统，例如 Graylog。记录日志没有嵌套的字典和数组。

```
$ dns-gazer -r captured.pcap -R -t - 
```

Enter fullscreen mode Exit fullscreen mode

然后，将 flowwing 日志输出到 stdout。

```
2015-10-11T02:40:12+00:00   dns-gazer.dns.record    {"client_addr": "10.139.96.169", "client_port": 53684, "msg_type": "query", "name": "bf-pro-front.cloudapp.net.", "section": "question", "server_addr": "210.196.3.183", "server_port": 53, "tx_id": 23904, "type": "A"}
2015-10-11T02:40:12+00:00   dns-gazer.dns.tx    {"client_addr": "10.139.96.169", "client_port": 53684, "query": [{"name": "bf-pro-front.cloudapp.net.", "section": "question", "type": "A"}], "query_ts": 1.44453e+09, "reply": [{"name": "bf-pro-front.cloudapp.net.", "section": "question", "type": "A"}, {"data": "23.100.102.231", "name": "bf-pro-front.cloudapp.net.", "section": "answer", "type": "A"}], "reply_ts": 1.44453e+09, "server_addr": "210.196.3.183", "server_port": 53, "status": "success", "tx_id": 23904}
2015-10-11T02:40:12+00:00   dns-gazer.dns.record    {"client_addr": "10.139.96.169", "client_port": 53684, "msg_type": "reply", "name": "bf-pro-front.cloudapp.net.", "section": "question", "server_addr": "210.196.3.183", "server_port": 53, "tx_id": 23904, "type": "A"}
2015-10-11T02:40:12+00:00   dns-gazer.dns.record    {"client_addr": "10.139.96.169", "client_port": 53684, "data": "23.100.102.231", "msg_type": "reply", "name": "bf-pro-front.cloudapp.net.", "section": "answer", "server_addr": "210.196.3.183", "server_port": 53, "tx_id": 23904, "type": "A"} 
```

Enter fullscreen mode Exit fullscreen mode

# 如何使用

## 设置(Docker 图像)

DNS gazer docker 图片可通过此[docker 文件](https://github.com/m-mizutani/dns-gazer-docker-image)在 [mztn/dns-gazer](https://hub.docker.com/r/mztn/dns-gazer/) 获得

### 要求

*   环境变量
    *   设备:指定监控设备，如 eth0。这将传递给`-i`选项。
    *   FLUENTD _ ADDRESS:FLUENTD 前向输入插件的目的地，例如 127.0.0.1:24224 将被传递给`-f`选项。
*   网络
    *   `--net=host`是必需的，因为 docker 容器需要直接监控网络接口。

### 举例

```
$ docker run --net=host -e DEVICE=eth0 -e FLUENTD_ADDRESS=localhost:24224 mztn/dns-gazer 
```

Enter fullscreen mode Exit fullscreen mode

## 设置(构建源代码)

### 先决条件

*   支持操作系统:Linux 和 macOSX
    *   Linux >= 4.4.0
    *   macOSX >= 10.12.6
*   C++11 编译器(推荐:clang++ >= 3.9)
*   CMake >= 3.5.1
*   libpcap >= 1.7.4
*   libmsgpack >= 0.5.9

### 构建&安装

```
$ git clone --recurse-submodules git@github.com:m-mizutani/dns-gazer.git
$ cd dns-gazer
$ cmake .
$ make
$ sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

如果你有额外的第三方包目录(比如家酿)并且需要的库在那里，你可以为`cmake`指定`INC_DIR`和`LIB_DIR`。

```
$ cmake -DINC_DIR=/opt/include -DLIB_DIR=/opt/lib . 
```

Enter fullscreen mode Exit fullscreen mode