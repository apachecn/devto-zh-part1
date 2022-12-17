# 微服务 API 网关与传统 API 网关

> 原文：<https://dev.to/datawireio/microservices-api-gateways-vs-traditional-api-gateways-7fh>

*本文原载于[www . getambassador . io](https://www.getambassador.io/about/microservices-api-gateways)T3】*

微服务 API 网关是一个 API 网关，旨在加速独立服务团队的开发工作流。微服务 API 网关为团队提供了独立发布、监控和更新微服务的所有功能。

这种对加速开发工作流的关注不同于传统 API 网关的目的，后者关注的是管理 API 的挑战。在过去的十年中，组织一直致力于通过定义良好的 API 来公开内部系统。向最终用户(内部和外部)安全地公开成百上千个 API 的挑战导致了 API 网关的出现。随着时间的推移，API 网关已经成为控制对这些 API 的访问的集中的、任务关键的基础设施。

在本文中，我们将讨论业务目标(生产力与管理)的差异如何导致截然不同的 API 网关。

## 微服务组织

在微服务组织中，开发人员的小团队相互独立地工作，以快速地向客户交付功能。为了让服务团队能够独立工作，拥有高效的工作流程，服务团队需要能够:

1.  发布他们的服务，以便其他人可以使用该服务
2.  监控他们的服务，看看它有多好
3.  测试和更新他们的服务，这样他们就可以不断改进服务

*不需要*另一个团队的帮助。(一旦一个服务团队需要另一个团队，他们就不再独立于另一个团队工作，从而产生瓶颈。)

对于服务发布，微服务 API 网关为消费者提供静态地址，并将请求动态路由到适当的服务地址。此外，为了安全起见，提供身份验证和 TLS 终止是向其他消费者公开服务时的典型考虑因素。

了解服务的最终用户体验对于改进服务至关重要。例如，软件更新可能会无意中影响某些请求的延迟。微服务 API 网关非常适合在将流量路由到终端服务时收集终端用户流量的关键可观察性指标。

微服务 API 网关支持将用户请求动态路由到不同的服务版本进行 canary 测试。通过将一小部分最终用户请求路由到服务的新版本，服务团队可以安全地测试新更新对一小部分用户的影响。

## 微服务 API 网关与传统 API 网关

乍一看，上面描述的用例可以用传统的 API 网关来实现。让我们更仔细地看看它们的区别。

| 用例 | API 网关 | 微服务 API 网关 |
| --- | --- | --- |
| 出版 | 运营部注册/更新新服务 | 服务团队注册/更新新服务 |
| 监视 | 测量每个消费者的 API 调用，用于计量 | 测量 L7 延迟、吞吐量、可用性 |
| 限速 | 当一个消费者超过其配额时，切断每个消费者的 API 调用 | 当服务没有响应时限制 API 调用，以获得弹性 |
| 测试和更新 | API 版本控制的稳定性 | 用于动态测试的金丝雀路由 |

## 自助发布

服务团队需要能够向客户发布新服务，而不需要运营团队(“自助服务”)。虽然传统的 API 网关可以提供用于发布新服务的简单机制(例如，REST API ),但是实际上，其使用仅限于操作。将发布限制到操作团队的主要原因是为了提供一个额外的(人类)安全机制:一个错误的 API 调用可能会对生产造成潜在的灾难性影响。微服务 API 网关利用各种机制，使服务团队能够轻松地*和*安全地发布新服务。一种示例方法是将路由元数据直接附加到服务对象，这消除了一个服务团队无意中影响另一个服务的可能性。

## 监控&限速

API 的一个常见业务模型是计量，根据 API 的使用情况向消费者收取不同的费用。传统的 API 网关在这个用例中表现出色:它们提供了监控每个客户端对 API 的使用的功能，以及在客户端超出配额时限制使用的能力。

微服务也需要监控和速率限制，但原因不同。监控用户可见的指标(如吞吐量、延迟和可用性)对于确保新的更新不会影响最终用户非常重要。强大的最终用户指标对于实现快速、增量更新至关重要。速率限制用于提高服务的整体弹性。当服务没有按预期响应时，API 网关可以抑制传入的请求，以允许服务恢复并防止级联故障。

## 测试和更新

一个微服务应用有多个服务，每个服务都是独立更新的。移动目标的综合测试是必要的，但对微服务来说还不够。金丝雀测试是帮助测试更新的重要工具，其中一小部分流量被路由到新的服务版本。通过将新的服务版本限制为一小部分用户，服务失败的影响是有限的。

在传统的 API 网关中，路由用于管理不断变化的 API 版本。微服务 API 网关将 canary routing 直接集成到路由规则中，以便服务团队可以快速、安全地部署新版本的服务。

# 总结

传统的 API 网关旨在解决 API 管理的挑战。虽然它们似乎解决了采用微服务的一些挑战，但事实是微服务工作流产生了一组不同的要求。将微服务 API 网关集成到您的开发工作流中，使服务团队能够快速、安全地自行发布、监控和更新他们的服务。这将使您的组织能够以前所未有的速度交付软件。