# nuster——一个基于 HAProxy 的 web 缓存代理服务器

> 原文：<https://dev.to/nuster/nuster---a-web-caching-proxy-server-based-on-haproxy-40k2>

大家好，我已经创建了一个 web 缓存代理服务器 [nuster](https://github.com/jiangwenyuan/nuster) ，请尝试一下并开始:)

# 简介

Nuster 是一个基于 HAProxy 的简单而强大的 web 缓存代理服务器。
与 HAProxy 100%兼容，充分利用 ha proxy 的 ACL
功能，根据
请求、响应或服务器状态的内容，提供细粒度的缓存策略，例如，

*   请求 url:仅当请求 url 等于 X 时缓存
*   请求查询:仅当请求查询包含 X 和 Y 的等式时缓存
*   cookie:仅当 cookie 包含 X 和 Y 的等式时缓存
*   响应头:仅当响应包含 X 等于 Y 的头时缓存
*   rate:仅当请求速率大于 X 时缓存
*   等等，以上的任何组合

# 表现

Nuster 非常快，一些测试显示 nuster 在使用单核时几乎比
nginx 快三倍，在使用所有内核时几乎比 nginx 快两倍，比
快三倍。

参见[详细基准](https://github.com/jiangwenyuan/nuster/wiki/Performance-benchmark:-nuster-vs-nginx-vs-varnish)

# 安装

## 下载

从[发布](https://github.com/jiangwenyuan/nuster/releases)页面
下载稳定版供生产使用，否则 git 克隆源代码。

## 打造

```
make TARGET=linux2628
make install 
```

Enter fullscreen mode Exit fullscreen mode

> 使用`make TARGET=linux2628 USE_PTHREAD_PSHARED=1`来使用 pthread 库

详见 [HAProxy 自述](https://dev.toREADME)。

## 码头工人

```
docker pull nuster/nuster
docker run -d -v /path/to/nuster.cfg:/etc/nuster/nuster.cfg:ro -p 8080:8080 nuster/nuster:1.7.9.3 
```

Enter fullscreen mode Exit fullscreen mode

参见 [Github](https://github.com/jiangwenyuan/nuster/blob/master/README.md) 上的用法、常见问题和示例