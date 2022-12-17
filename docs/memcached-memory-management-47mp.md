# 内存缓存内存管理

> 原文：<https://dev.to/loginradius/memcached-memory-management-47mp>

memcached 是最流行的开源内存键值缓存系统之一。我简单说一下 memcached 的内存管理设计。Chunk 和 slab 这是 slabclass_t 的结构声明。每个 Slab 类包含相同大小的 chunk，但是…

[继续阅读](https://www.loginradius.com/engineering/blog/memcach-memory-management/)