# 在 Squarespace 从 Java Monolith 转移到微服务

> 原文：<https://dev.to/thepracticaldev/moving-from-a-java-monolith-to-microservices-at-squarespace>

Julian Applebaum 停下来讨论了将 Squarespace 的 Java monolith 迁移到面向服务的架构所面临的挑战。Julian 描述了如何在不同的业务逻辑层之间划分界限，以及如何在整个迁移过程中发现重构应用程序逻辑的基本矛盾。Squarespace 通向一系列 RESTful API 端点的旅程是一个构建服务并随着服务变得可靠而慢慢集成它们的过程。对于 Squarespace 约 100 名工程师的团队来说，这一举动是不可避免的。