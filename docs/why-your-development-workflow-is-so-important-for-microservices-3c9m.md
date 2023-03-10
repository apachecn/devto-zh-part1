# 为什么您的开发工作流程对微服务如此重要

> 原文：<https://dev.to/datawireio/why-your-development-workflow-is-so-important-for-microservices-3c9m>

您的开发工作流是您的组织开发软件的过程。典型的开发工作流程从产品定义开始，然后经过开发、测试、发布和生产阶段。

## 稳定性与速度的权衡

组织针对其给定的业务需求和应用程序调整此工作流。通常，这涉及到优化工作流，以提供稳定性和速度的正确平衡。随着应用程序变得越来越流行，确保更新不会对用户产生负面影响变得越来越重要。更严格的发布标准、更好的测试和开发评审是提高稳定性的典型策略。然而这些策略不是免费的，因为它们降低了速度。

(难道你没有说过“我们过去运送软件的速度非常快，现在即使我们有两倍多的工程师，速度也慢了下来？”)

## 扩展您的开发工作流程

开发工作流的问题在于，再多的优化也无法克服这样一个事实，即没有一个开发工作流可以适用于应用程序的每一部分。

事实是，应用程序的某些部分需要稳定性，而应用程序的其他部分需要速度。您真正需要的是为应用程序的不同部分工作的多个工作流。

## 微服务

微服务是分布式开发工作流，通过将您的应用程序拆分成更小的服务来实现。通过将您的应用程序分割成更小的组件，您能够为您的每个服务运行独立的开发工作流。

您希望为早期功能开发运行原型工作流。随着服务的成熟，您将需要一个支持生产中快速更新的工作流。随着它成为其他服务或用户真正依赖的关键任务服务，您将需要一个工作流来确保坚如磐石的稳定性。

## 建立一个工作流程既容易又困难

构建微服务工作流的挑战在于，您需要一套支持所有这些不同开发模式的标准工具和流程。您不希望一套工具用于原型开发，而另一套工具和工作流程用于生产。

此外，在微服务架构中，开发团队通常负责一项服务，而不仅仅是代码。这意味着开发团队需要操作技能和能力，例如，监控和部署。

幸运的是，Kubernetes 已经成为运行云原生应用程序的事实标准。Kubernetes 生态系统提供了一套强大的运营基础设施来运行微服务。所以不需要从头开始。

## 开源工具

以下开源工具可能有助于优化您在 Kubernetes 上的开发工作流程:

*   [Kubernetes](https://www.kubernetes.io/)
*   [码头工人](https://www.docker.com)
*   [网真](https://www.telepresence.io/)
*   [特使](https://www.envoyproxy.io/)
*   [伪造](https://forge.sh/)
*   大使

## 你怎么看？

你的开发工作流程是什么样的？你的开发人员用什么工具，面临什么痛点？