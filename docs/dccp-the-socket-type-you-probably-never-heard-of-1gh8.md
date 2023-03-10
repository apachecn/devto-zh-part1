# DCCP:你可能从未听说过的插座类型

> 原文：<https://dev.to/anmolsarma/dccp-the-socket-type-you-probably-never-heard-of-1gh8>

*TL；DR: DCCP 是相对较新的传输层协议，它吸取了 TCP 和 UDP 的优点。直接跳到示例 C 代码。*

## 背景

从历史上看，互联网上的大部分流量都是通过 TCP 在两台主机之间提供可靠的面向连接的数据流。 [UDP](https://en.wikipedia.org/wiki/User_Datagram_Protocol) 主要由应用程序使用，这些应用程序的短暂传输会因 TCP 的连接建立开销而慢得令人无法接受，或者那些时效性比可靠性更重要的应用程序。然而，UDP 在网络电话和流媒体等传输大量数据的应用中的使用越来越多，这可能会导致严重的[网络拥塞](https://en.wikipedia.org/wiki/Network_congestion)。与 TCP 不同，UDP 不提供固有的拥塞控制机制，因此应用程序可以以比可用路径容量高得多的速率发送 UDP 数据报，从而导致路径拥塞。拥塞加剧可能会导致延迟、数据包丢失和网络服务质量下降。

因此，选择使用 UDP 作为传输协议的应用程序和协议必须采用机制来防止拥塞，并与并发流量建立某种程度的公平性，以便网络保持可用。这种拥塞控制方案的一个突出例子是 [BitTorrent](https://en.wikipedia.org/wiki/BitTorrent) 采用的 [LEDBAT](https://en.wikipedia.org/wiki/LEDBAT) 。然而，实现拥塞控制方案是困难的、耗时的并且容易出错。多种非标准实现也使得很难推断应用程序将如何响应网络拥塞。 [DCCP](https://en.wikipedia.org/wiki/Datagram_Congestion_Control_Protocol) -数据报拥塞控制协议旨在通过内置拥塞控制来缓解不可靠数据报的传输问题。

从应用程序程序员的角度来看，DCCP 与 UDP 的不同之处在于它提供了四个附加功能:

*   主机之间的显式连接建立
*   可选拥塞控制方案
*   路径 MTU 发现以避免碎片
*   用于识别应用的服务代码

DCCP 使用显式拥塞通知，但它对应用程序是透明的。DCCP 旨在根据需要将可靠性或前向纠错(FEC)等附加功能置于顶层，而非协议层本身。

## 显式连接建立

DCCP 的连接建立语义反映了 TCP 的连接建立语义，客户端主动连接到被动侦听端口的服务器。DCCP 连接是双向的。然而，从逻辑上讲，DCCP 连接由两个独立的单向连接组成，称为半连接。每个半连接都是单向的、不可靠的数据报管道。下一节将解释这样做的理由。

## 可选的拥塞控制方案

TCP 对应用程序完全透明地实现拥塞控制。虽然可以将主机配置为使用特定的变体，但应用程序无法发现哪个拥塞控制方案有效，更不用说协商一个方案了。然而，DCCP 可以通过允许应用协商拥塞控制方案来迎合应用的不同需求。事实上，每个半连接可以使用不同的方案，允许更大的控制。

通过以比端点之间最慢的链路更快的速率发送数据来拥塞网络，会使网络不堪重负。这可能导致分组丢失，从而导致重新传输，这又可能导致进一步的拥塞。这个问题的解决方案是在新的连接上开始以低速传输数据，然后提高速度，直到检测到数据包丢失。然后，可以缩减传输速率，直到不再发生分组丢失。传输数据的最佳速度会随着网络条件的变化而变化。拥塞控制方案的不同之处在于如何估计分组丢失以及传输速度上升或下降的速率。DCCP 拥塞控制方案由拥塞控制标识符 CCIDs 表示。目前，已经正式指定了三个 CCIDs:

**[CCID 2](https://tools.ietf.org/html/rfc4341)——类似 TCP 的拥塞控制:**一种模仿 TCP 的快速反应方案，它将快速提升速度以利用可用带宽，并且在检测到拥塞时也快速缩减。适用于传输速率波动较大的应用。

**[CCID 3](https://tools.ietf.org/html/rfc5348) - TCP 友好的速率控制(TFRC):** 一种反应较慢的方案，旨在对网络中的并发 TCP 流友好。以可能不利用所有可用带宽为代价，提供相对更平滑的发送速率。适合喜欢将发送速率的突然变化降至最低的媒体流应用。

**[CCID 4](https://tools.ietf.org/html/rfc4828)——TCP 友好的小数据包速率控制(TFRC-SP):** 一种实验性方案，适用于使用较小数据报大小的应用程序，以及那些通过改变数据报大小来改变其发送速率的应用程序。

此外，Linux 内核的 [DCCP 测试树](https://github.com/uoaerg/linux-dccp)包含一个模仿 [TCP 立方](https://en.wikipedia.org/wiki/CUBIC_TCP)的方案的实验实现。还有一种模式是完全禁用对类似于*UDP*行为的拥塞控制。

## PMTU 发现

两台互联网主机之间的数据以一系列 IP 数据包的形式通过中间链路传输。这些链路中的每一个都有一个最大分组大小或最大传输单元(MTU ),它可以传输而不必将其分成更小的片段。在路径上任何地方都不需要分段的最大分组大小被称为路径最大传输单元或 PMTU。应用程序通常可以通过生成比 PMTU 更小的数据包来获得更好的容错能力。DCCP 基于 PMTU 和用于每个连接的拥塞控制方案来定义最大分组大小(MPS)。DCCP 实现不会发送任何大于 MPS 的数据包，而是向应用程序返回一个适当的错误。应用程序可以向 DCCP 堆栈查询当前的 MPS，并限制自己发送大于该值的数据报，从而避免[碎片](https://en.wikipedia.org/wiki/IP_fragmentation)。

## 服务代码

DCCP 定义了一个 32 位服务代码，用于区分与单个服务器端口相关的多个应用程序。客户端指定它想要连接的服务代码，该代码用于标识处理 DCCP 连接请求的预期服务或应用程序。本质上，服务代码为连接多路复用提供了一个额外的间接层。在端口上侦听的服务器可能与多个服务代码相关联，但是客户端可能只有一个服务代码，指示它希望连接的应用程序。

## 用法

自 2.6.14 以来，主流 Linux 内核已经包含了 DCCP 支持，Ubuntu 等主流发行版默认支持它。然而，为了获得更新的实验特性，您必须从 DCCP 测试树中构建内核。或者你也可以从[这里的](https://github.com/unmole/linux-dccp/releases/latest)获取最新的稳定内核版本和实验性的 DCCP 变化。务必在*联网支持*–>*联网选项*–>*DCCP 协议*–>*DCCP CCIDs 配置*中启用内核配置中的所有 ccid。正如 Debian 安装指南所说，“*不要害怕尝试编译内核。又好玩又赚钱。*“目前，Linux 是唯一支持原生 DCCP 的操作系统，除非你算上旧版本 FreeBSD 的补丁。

## C 中例子

除了套接字类型和服务代码的设置之外，服务器和客户机看起来几乎与它们的 TCP 对等物完全相同。客户端使用 *getsockopt()* 读取当前最大数据包大小。读取主机上可用的 CCIDs 显示在 **probe.c** 中。由于 libc 仍然没有 **netinet/dccp.h** 头文件，你将不得不从内核源码中获取所需的常量，或者直接使用下面的 **dccp.h** 头文件。[下载代码](https://www.anmolsarma.in/dl/dccp_socket_example.tar.gz)

**server.c**

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <errno.h>

#include "dccp.h"

#define PORT 1337
#define SERVICE_CODE 42

int error_exit(const char *str)
{
    perror(str);
    exit(errno);
}

int main(int argc, char **argv)
{
    int listen_sock = socket(AF_INET, SOCK_DCCP, IPPROTO_DCCP);
    if (listen_sock < 0)
        error_exit("socket");

    struct sockaddr_in servaddr = {
        .sin_family = AF_INET,
        .sin_addr.s_addr = htonl(INADDR_ANY),
        .sin_port = htons(PORT),
    };

    if (setsockopt(listen_sock, SOL_SOCKET, SO_REUSEADDR, &(int) {
               1}, sizeof(int)))
        error_exit("setsockopt(SO_REUSEADDR)");

    if (bind(listen_sock, (struct sockaddr *)&servaddr, sizeof(servaddr)))
        error_exit("bind");

    // DCCP mandates the use of a 'Service Code' in addition the port
    if (setsockopt(listen_sock, SOL_DCCP, DCCP_SOCKOPT_SERVICE, &(int) {
               htonl(SERVICE_CODE)}, sizeof(int)))
        error_exit("setsockopt(DCCP_SOCKOPT_SERVICE)");

    if (listen(listen_sock, 1))
        error_exit("listen");

    for (;;) {

        printf("Waiting for connection...\n");

        struct sockaddr_in client_addr;
        socklen_t addr_len = sizeof(client_addr);

        int conn_sock = accept(listen_sock, (struct sockaddr *)&client_addr, &addr_len);
        if (conn_sock < 0) {
            perror("accept");
            continue;
        }

        printf("Connection received from %s:%d\n",
               inet_ntoa(client_addr.sin_addr), ntohs(client_addr.sin_port));

        for (;;) {
            char buffer[1024];
            // Each recv() will read only one individual message.
            // Datagrams, not a stream!
            int ret = recv(conn_sock, buffer, sizeof(buffer), 0);
            if (ret > 0)
                printf("Received: %s\n", buffer);
            else
                break;

        }

        close(conn_sock);
    }
} 
```

**client . c**T2】

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <errno.h>

#include "dccp.h"

int error_exit(const char *str)
{
    perror(str);
    exit(errno);
}

int main(int argc, char *argv[])
{
    if (argc < 5) {
        printf("Usage: ./client <server address> <port> <service code> <message 1> [message 2] ... \n");
        exit(-1);
    }
    struct sockaddr_in server_addr = {
        .sin_family = AF_INET,
        .sin_port = htons(atoi(argv[2])),
    };

    if (!inet_pton(AF_INET, argv[1], &server_addr.sin_addr.s_addr)) {
        printf("Invalid address %s\n", argv[1]);
        exit(-1);
    }

    int socket_fd = socket(AF_INET, SOCK_DCCP, IPPROTO_DCCP);
    if (socket_fd < 0)
        error_exit("socket");

    if (setsockopt(socket_fd, SOL_DCCP, DCCP_SOCKOPT_SERVICE, &(int) {htonl(atoi(argv[3]))}, sizeof(int)))
        error_exit("setsockopt(DCCP_SOCKOPT_SERVICE)");

    if (connect(socket_fd, (struct sockaddr *) &server_addr, sizeof(server_addr)))
        error_exit("connect");

    // Get the maximum packet size
    uint32_t mps;
    socklen_t res_len = sizeof(mps);
    if (getsockopt(socket_fd, SOL_DCCP, DCCP_SOCKOPT_GET_CUR_MPS, &mps, &res_len))
        error_exit("getsockopt(DCCP_SOCKOPT_GET_CUR_MPS)");
    printf("Maximum Packet Size: %d\n", mps);

    for (int i = 4; i < argc; i++) {
        if (send(socket_fd, argv[i], strlen(argv[i]) + 1, 0) < 0)
            error_exit("send");
    }

    // Wait for a while to allow all the messages to be transmitted
    usleep(5 * 1000);

    close(socket_fd);
    return 0;
} 
```

**probe . c**T2】

```
#include <stdio.h>
#include <sys/socket.h>
#include <netinet/in.h>

#include "dccp.h"

int main()
{
    int sock_fd = socket(AF_INET, SOCK_DCCP, IPPROTO_DCCP);

    // Check the congestion control schemes available
    socklen_t res_len = 6;
    uint8_t ccids[6];
    if (getsockopt(sock_fd, SOL_DCCP, DCCP_SOCKOPT_AVAILABLE_CCIDS, ccids, &res_len)) {
        perror("getsockopt(DCCP_SOCKOPT_AVAILABLE_CCIDS)");
        return -1;
    }

    printf("%d CCIDs available:", res_len);
    for (int i = 0; i < res_len; i++)
        printf(" %d", ccids[i]);

    return res_len;
} 
```

**dccp.h**

```
/* This file only contains constants necessary for user space to call
 * into the kernel and thus, contains no copyrightable information. */

#ifndef DCCP_DCCP_H
#define DCCP_DCCP_H

// From the kernel's include/linux/socket.h
#define SOL_DCCP 269

// From kernel's include/uapi/linux/dccp.h
#define DCCP_SOCKOPT_SERVICE 2
#define DCCP_SOCKOPT_CHANGE_L 3
#define DCCP_SOCKOPT_CHANGE_R 4
#define DCCP_SOCKOPT_GET_CUR_MPS 5
#define DCCP_SOCKOPT_SERVER_TIMEWAIT 6
#define DCCP_SOCKOPT_SEND_CSCOV 10
#define DCCP_SOCKOPT_RECV_CSCOV 11
#define DCCP_SOCKOPT_AVAILABLE_CCIDS 12
#define DCCP_SOCKOPT_CCID 13
#define DCCP_SOCKOPT_TX_CCID 14
#define DCCP_SOCKOPT_RX_CCID 15
#define DCCP_SOCKOPT_QPOLICY_ID 16
#define DCCP_SOCKOPT_QPOLICY_TXQLEN 17
#define DCCP_SOCKOPT_CCID_RX_INFO 128
#define DCCP_SOCKOPT_CCID_TX_INFO 192

#endif //DCCP_DCCP_H 
```

## 说明和结论

DCCP 不是主流。它没有被广泛部署，甚至没有得到支持。文献很少。尽管 Linux DCCP NAT 功能正常，但许多中间设备可能只会丢弃 DCCP 流量。DCCP 是第四层的固定齿轮自行车，它是最终的时尚交通工具。