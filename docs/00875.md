# 使用 dig 命令绕过 DNS 层次结构

> 原文：<https://dev.to/attilavm/exploring-dns-concepts-using-the-dig-command-4em>

### 主题

1.  临时切换域名服务器(从现在起 NS)。
2.  跟踪域名解析。
3.  找到直接询问 NS 的方法，绕过 DNS 层级。

### 第一个用例

您在域名注册机构更改了您的名称服务器设置，但是您没有立即看到效果，假设这个问题出现在您解析选择的 NS 以缓存域名 IP(或其他原始记录)映射的能力中。[<sup>1</sup>T4】](#caching-notes)

#### 解

您可以使用`dig`命令覆盖一个请求的名称服务器，而不是临时更改整个系统上的名称服务器设置。`dig`的超能力在于它模仿 NS 请求。

剧情简介:

```
dig [@address of the NS] [domain name] 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
$ dig @208.67.222.222 wikipedia.org 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
; <<>> DiG 9.10.6 <<>> @208.67.222.222 wikipedia.org
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 4381
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;wikipedia.org.                 IN      A

;; ANSWER SECTION:
wikipedia.org.          600     IN      A       91.198.174.192

;; Query time: 75 msec
;; SERVER: 208.67.222.222#53(208.67.222.222)
;; WHEN: Thu Jan 11 11:43:32 CET 2018
;; MSG SIZE  rcvd: 58 
```

Enter fullscreen mode Exit fullscreen mode

如果您只对生成的 IPs 和原始数据感兴趣。

```
$ dig +noall +answer @208.67.222.222 github.io | rev | cut -f 1 | rev 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
151.101.1.147
151.101.65.147
151.101.129.147
151.101.193.147 
```

Enter fullscreen mode Exit fullscreen mode

### 第二个用例

类似于第一种情况，域名 IP 映射被缓存，但不是由解析 NS 缓存，而是由其委托链中的一些其他 NS 缓存。

#### 解

我们将使用`dig`的其他一些简洁的特性，首先我们将跟踪我们的查询是如何在 NS 层次中被委托的，然后绕过这个层次。

通常的层级:
解析 NS - >根 NS - > TLD NS - >某公司的注册管理执行机构 NS - > NS。

```
$ dig +trace @208.67.222.222 en.wikipedia.org 
```

Enter fullscreen mode Exit fullscreen mode

```
; <<>> DiG 9.10.6 <<>> +trace @208.67.222.222 en.wikipedia.org
; (1 server found)
;; global options: +cmd
.                       518400  IN      NS      a.root-servers.net.
.                       518400  IN      NS      b.root-servers.net.
.                       518400  IN      NS      c.root-servers.net.
.                       518400  IN      NS      d.root-servers.net.
.                       518400  IN      NS      e.root-servers.net.
.                       518400  IN      NS      f.root-servers.net.
.                       518400  IN      NS      g.root-servers.net.
.                       518400  IN      NS      h.root-servers.net.
.                       518400  IN      NS      i.root-servers.net.
.                       518400  IN      NS      j.root-servers.net.
.                       518400  IN      NS      k.root-servers.net.
.                       518400  IN      NS      l.root-servers.net.
.                       518400  IN      NS      m.root-servers.net.
;; Received 239 bytes from 208.67.222.222#53(208.67.222.222) in 63 ms

org.                    172800  IN      NS      a2.org.afilias-nst.info.
org.                    172800  IN      NS      b0.org.afilias-nst.org.
org.                    172800  IN      NS      b2.org.afilias-nst.org.
org.                    172800  IN      NS      c0.org.afilias-nst.info.
org.                    172800  IN      NS      d0.org.afilias-nst.org.
org.                    172800  IN      NS      a0.org.afilias-nst.info.
org.                    86400   IN      DS      9795 7 2 3922B31B6F3A4EA92B19EB7B52120F031FD8E05FF0B03BAFCF9F891B FE7FF8E5
org.                    86400   IN      DS      9795 7 1 364DFAB3DAF254CAB477B5675B10766DDAA24982
org.                    86400   IN      RRSIG   DS 8 1 86400 20180124050000 20180111040000 41824 . BCA4iv0QFRRxn9WDpnUvHcuI7AnIMMuq/RhfnkyaSEA+XnYrcxHx2Tom UJDobnWucc5pDI/wLzdNFGP37VYUIfHKlnY8Fv9yFDWA5/nP2Wtdi591 SNrNFSmS+XXCwWIOMl7r6coouzGY1MRkmVXnbLjwN5FqvLqquAwFBWpa a5flfMnO7K5M2trNhYXUTo2BVB/ZnWNox+ynPjJB1xspkfS/K4Fv0BXx S6i6LMQk/rYEo4RIB/2JzPj+GWLTTSqD/LtQSA6f6kFFnDkywBSWSuBq hBGAxyrP0LWYbNuUBn8gP9H1kiWgcn8sgaHbcQlk4uKs9snB55myTQYw 7KLIUg==
;; Received 818 bytes from 192.33.4.12#53(c.root-servers.net) in 36 ms

wikipedia.org.          86400   IN      NS      ns0.wikimedia.org.
wikipedia.org.          86400   IN      NS      ns2.wikimedia.org.
wikipedia.org.          86400   IN      NS      ns1.wikimedia.org.
h9p7u7tr2u91d0v0ljs9l1gidnp90u3h.org. 86400 IN NSEC3 1 1 1 D399EAAB H9PARR669T6U8O1GSG9E1LMITK4DEM0T  NS SOA RRSIG DNSKEY NSEC3PARAM
h9p7u7tr2u91d0v0ljs9l1gidnp90u3h.org. 86400 IN RRSIG NSEC3 7 2 86400 20180201111010 20180111101010 1862 org. A3vUwzoJIFJbiWgQK1/ACwB6ZyvIK99ulaAJAoalHwrKn1JnecZ7Sina rEhvKs4vL+FWLvwLNfDWthfaFsO0++eaVFFQ3A6tWvx8PQk2nSKsGdEI Ri6Nld7lJIkL3DCo0PY+0+WwJFnhUGXTtr+B0uPSGA+jSBsQWYTmJnQI 2dM=
hhdfcaa81hb1nfs0h6sd0pq6ctib1781.org. 86400 IN NSEC3 1 1 1 D399EAAB HHE2JLM2DDBRLOQF1RVBUAGVETJ6EPFD 
hhdfcaa81hb1nfs0h6sd0pq6ctib1781.org. 86400 IN RRSIG NSEC3 7 2 86400 20180130153401 20180109143401 1862 org. YK0/K+mWIph03k8zsmD90XnmsxU2bdm60usXSMR8QnbsS4aJpmzGvGOQ FZOnOuUAv58++rtlXU5SI0cQ5vZ0L/iTGW3iXlD9WBQoa/e45E7vtzbK Kn2fhMSLVLWzn377i8AWOgpC4LNHPKIyA1o1NRtdvxkhASNbY5PQWaQI oN8=
;; Received 642 bytes from 199.19.56.1#53(a0.org.afilias-nst.info) in 299 ms

en.wikipedia.org.       600     IN      A       91.198.174.192
;; Received 89 bytes from 91.198.174.239#53(ns2.wikimedia.org) in 52 ms 
```

Enter fullscreen mode Exit fullscreen mode

每个表都是不同 NS 的响应。这些列是:

1.  要查找的域名
2.  生存时间值[秒]请求者应该缓存此结果的时间。
3.  域名映射到的网络类别，几乎总是互联网`IN`。如果你看到类似 [CH](https://en.wikipedia.org/wiki/Chaosnet) 的东西，那么你将会看到历史本身。
4.  [记录的类型](https://en.wikipedia.org/wiki/List_of_DNS_record_types)。
5.  记录的原始数据，如 IP 地址。

对我们来说最重要的行是这样的:

```
;; Received [number] bytes from [address]#[port](domain name) in [milliseconds] ms 
```

Enter fullscreen mode Exit fullscreen mode

喜欢:

```
;; Received 89 bytes from 91.198.174.239#53(ns2.wikimedia.org) in 52 ms 
```

Enter fullscreen mode Exit fullscreen mode

它们公开了名称服务器的地址和端口，我们可以用它来绕过名称解析层次结构，这对识别缓存问题很有用。

应用此:

```
$ dig -p 53 @208.80.154.238 en.wikipedia.org 
```

Enter fullscreen mode Exit fullscreen mode

```
; <<>> DiG 9.10.6 <<>> -p 53 @208.80.154.238 en.wikipedia.org
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 31772
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1024
;; QUESTION SECTION:
;en.wikipedia.org.              IN      A

;; ANSWER SECTION:
en.wikipedia.org.       600     IN      A       91.198.174.192

;; ADDITIONAL SECTION:
en.wikipedia.org.       600     IN      AAAA    2620:0:862:ed1a::1

;; Query time: 135 msec
;; SERVER: 208.80.154.238#53(208.80.154.238)
;; WHEN: Thu Jan 11 13:55:53 CET 2018
;; MSG SIZE  rcvd: 89 
```

Enter fullscreen mode Exit fullscreen mode

我使用了`-p`开关来明确设置端口，但是 53 是域名请求的默认端口。

### 安装

#### Linux

它几乎总是由您的默认包管理器提供，或者使用 [Nix](https://nixos.org/nix/) 。

#### MacOS

可以用 [MacPorts](https://www.macports.org/) 、[家酿](https://brew.sh/)或者 Nix。

#### 窗口

正如杰克·弗莱彻在讨论中指出的，你可以使用巧克力。

#### 在线

[@nslookuptool](https://dev.to/nslookuptool) 为`dig`分享了一个漂亮的[网络界面](https://www.diggui.com/)的链接。

### 进一步阅读

[DNS 概念介绍](https://www.digitalocean.com/community/tutorials/an-introduction-to-dns-terminology-components-and-concepts)
[什么是 DNS 响应？](https://blog.dnsimple.com/2015/03/whats-in-a-dns-response/)

我希望这篇文章给其他人提供了一个新的方面，域名系统是如何工作的。

注意事项:

您的浏览器也能够缓存域名和操作系统。Windows 和 MacOS 默认这样做，在 Linux 上你可以安装和配置 [dnsmasq](https://www.g-loaded.eu/2010/09/18/caching-nameserver-using-dnsmasq/) ，但你不应该，因为你的浏览器正在为你缓存域名。如果我有运行的话，我甚至会禁用 OS 级 DNS 缓存。