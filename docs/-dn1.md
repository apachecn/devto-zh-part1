# Microservice module architecture diagram

> 原文：<https://dev.to/fangdajiang/-dn1>

### 找到了原理

[Configuration document](https://github.com/Netflix/eureka/wiki/Configuring-Eureka)
[![Eureka 原理](img/7172aeea02f1557fccffae2a67d889c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IJaMni6X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gr9xz2aaqd875cvfiwje.png)

### Eureka architecture diagram

[![Eureka 架构图](img/556b47a1251adbfbca5dc04f1a24b75b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--olNHS3Gw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dy8qb079qtcrw6joeaj0.png)

### Gateway

[![gateway](img/1d3300e3ac307f7b32081be6063119ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yNdfSKaN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4eyyk8nh9en52fc85jd9.png)

### ZUUL

[Routing configuration](http://blog.didispace.com/spring-cloud-starter-dalston-6-2/)
Netflix uses Zuul to do the following:

*   Authentication
*   censor
*   stress testing
*   Canary test
*   [Dynamic routing](http://xujin.org/sc/docs/sc-zuul/)
*   Service migration
*   Load clipping
*   safe
*   Static response processing

### Configuration Center Config

*   介绍:[聊聊春云配置](https://blog.coding.net/blog/spring-cloud-config)
*   Cloud Config project
    -providing server and client support
    -centralized management of application configuration in distributed environment
    -Spring-based environment, seamless integration with Spring applications
    -program that can be used for any language development
    -default implementation is based on git repository, and version management can be performed
    -user-defined implementation can be replaced.

*   Spring Config Server serves as the server of configuration center
    -updates the copy of git repository when pulling configuration, and ensures that it is the latest result
    -supports rich data structure, yml, json, properties, etc
    -cooperates with eureke to realize service discovery, With cloud bus, configuration push update can be realized
    -configuration storage is based on git warehouse, and version management can be carried out
    -simple and reliable, with rich supporting schemes.

*   Access mode

```
/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{profile}.properties
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties
/{label}/{application}-{profile}.yml
/{label}/{application} 
```

*   How to manage the various configurations in the following figure? ![unconfiged staff](img/010ef8d7cf0a1d35af318c26d38ad977.png)
*   Solution ![solution](img/30ac3907e60bd7f34e4c5141b10da554.png)
*   Figure ![Config Server](img/fd93befdf75296f352e0944121a9ff38.png) ![Config Server with Cloud Bus](img/f2f8b81785b7b82b1a6842ed747b8dac.png)

### 丝带

客户端负载均衡器，[配置文档](https://github.com/Netflix/ribbon/wiki/Programmers-Guide)

### 假装

声明性 REST 客户端

### Principle of Hystrix Circuit Breaker/Fuse

[配置文档](https://github.com/Netflix/Hystrix/wiki/Configuration) [涡轮](https://github.com/Netflix/Turbine/wiki) [涡轮配置 1 . x](https://github.com/Netflix/Turbine/wiki/Configuration-(1.x))
[![Hystrix](img/28471ad4f6656ebf265f1a3b611cc37a.png)T9】](https://res.cloudinary.com/practicaldev/image/fetch/s--M8WHrJNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/is5fof68f0n2311952hj.png)

### Schematic diagram of distributed service tracking system

(Source: [distributed service tracking and implementation of Spring Cloud](http://daixiaoyu.com/distributed-tracing.html) )
[![分布式服务跟踪系统示意图](img/98c363f9763c9460076d748612998bbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IKbnWLZR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nfxs9fsfqplhdo3k1hnk.png)

### data collection Zipkin

[![数据收集](img/fae57bb3c1f56328130b7652bcb92bdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_4WRhBgD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9khh3x2vcvp5cbzt0fyl.png)

### 侦察

*   Sleuth:通过日志进行分布式跟踪，[使用侦探追踪微服务](https://dzone.com/articles/tracing-in-microservices-with-spring-cloud-sleuth)
*   Zipkin:具有请求可视化的分布式跟踪系统

### Data flow

[![数据流转](img/24b04f82bfece16e2d0b6a5c4ba88a60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l_cw98lO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/09u64s6jhqokq6ll8ood.png)

*   跟踪+跨度![trace span](img/22d31fb17dc5b879dfb51dba10b2136d.png)

### Elk technology stack

ELK stack 基于日志文件
[![ELK](img/200d7e46c91b48dc9b298cd17723c9d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Pl-7ZO8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0nbsy3d84oo5hu617n7f.jpg) 与不同的应用程序交互

### Stream architecture diagram

[Configuration and development documents](http://xujin.org/sc/docs/sc-stream/)
[![Stream](img/181e1c2594c9c65371e9c36862819e13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NQHIrxDC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z40rpdfhv94k61obm5hz.png)