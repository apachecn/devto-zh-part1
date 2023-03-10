# Spring 安全性和线程

> 原文：<https://dev.to/spooz/spring-security-and-threads>

### 简介

当使用 Spring Security 来保护我们的应用程序时，我们必须了解它的内部工作方式。基础是 [SecurityContext](https://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/core/context/SecurityContext.html) ,它保存身份验证过程产生的数据以及正确授权所需的数据。根据定义，它是线程绑定的- [ThreadLocal](https://docs.oracle.com/javase/7/docs/api/java/lang/ThreadLocal.html) 被用作持有者，在请求的安全过滤过程中创建。[(了解更多)](https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#technical-overview)
线程绑定的解决方案很方便，但是有一个缺点——默认情况下安全上下文不会传播到子线程。幸运的是，Spring 提供了处理这个问题的工具。

### 委派上下文

如[文件](https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#concurrency)中所述，我们被授予[DelegatingSecurityContextRunnable](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/concurrent/DelegatingSecurityContextRunnable.html)和[DelegatingSecurityContextExecutor](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/concurrent/DelegatingSecurityContextExecutor.html)。第一个类是我们的 [Runnable](https://docs.oracle.com/javase/7/docs/api/java/lang/Runnable.html) 实例的低级包装器，使用[委托模式](https://en.wikipedia.org/wiki/Delegation_pattern)实现。它只是在执行 **run()** 方法的过程中获取给定的上下文并设置其值。用法非常简单:

```
SecurityContext context = SecurityContextHolder.getContext();
DelegatingSecurityContextRunnable wrappedRunnable =
    new DelegatingSecurityContextRunnable(originalRunnable, context); 
```

**DelegatingSecurityContextExecutor**是一个更高层次的抽象。它委派了[的执行者](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executor.html)的实例，而不是**的运行者**，使得线程池的管理知道 Spring 的安全上下文。

在现代 Java 中，我们最有可能将它与[流并行 API](https://docs.oracle.com/javase/tutorial/collections/streams/parallelism.html) 或 [CompletableFutures](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) 一起使用。默认情况下，这两种抽象都使用 Java 8 默认的 [ForkJoinPool.commonPool](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html#commonPool--) ，这很好，但通常我们会创建专用于特定任务的定制池。虽然 **ForkJoinPool** 被设计用来处理窃取工作的分治算法，但是我们也可以使用老式的 [FixedThreadPool](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Executors.html#newFixedThreadPool(int)) 。[(阅读更多)](https://zeroturnaround.com/rebellabs/fixedthreadpool-cachedthreadpool-or-forkjoinpool-picking-correct-java-executors-for-background-tasks/)

以下示例显示了使用**DelegatingSecurityContextExecutor**创建自定义 **FixedThreadPool** 以及创建新的 **CompletableFuture** 任务:

```
SecurityContext securityContext = SecurityContextHolder.getContext();
Executor delegatedExecutor = Executors.newFixedThreadPool(10);
Executor delegatingExecutor =
    new DelegatingSecurityContextExecutor(delegatedExecutor, securityContext);
CompletableFuture.supplyAsync(() -> veryHardTask(),delegatingExecutor); 
```

### @异步方法

上面的例子显示了用普通的 Java 并发方法来委托安全上下文。当使用 Spring 时，我们经常使用 [@Async](https://docs.spring.io/spring/docs/current/spring-framework-reference/html/scheduling.html) 注释来使我们的方法异步运行。它使用自己的[simpleasynctaskmexecutor](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/task/SimpleAsyncTaskExecutor.html)和自己的线程池。为了传递我们的上下文，我们可以创建另一个包装委托。然而，春天的安全再次[给了我们一个处理问题的便捷方法](https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#securitycontextholder-securitycontext-and-authentication-objects):

```
SecurityContextHolder.setStrategyName(SecurityContextHolder.MODE_INHERITABLETHREADLOCAL); 
```

该属性可以通过以下方式进行配置:

```
@Bean
public MethodInvokingFactoryBean methodInvokingFactoryBean() {
    MethodInvokingFactoryBean methodInvokingFactoryBean = new MethodInvokingFactoryBean();
    methodInvokingFactoryBean.setTargetClass(SecurityContextHolder.class);
    methodInvokingFactoryBean.setTargetMethod("setStrategyName");
    methodInvokingFactoryBean.setArguments(new String[]{SecurityContextHolder.MODE_INHERITABLETHREADLOCAL});
    return methodInvokingFactoryBean;
} 
```

它迫使 Spring 用安全委托[delegatingsecuritycontexttaskmexecutor](http://docs.spring.io/autorepo/docs/spring-security/4.0.0.M1/apidocs/org/springframework/security/task/DelegatingSecurityContextTaskExecutor.html)包装它的异步执行器。就这么简单，我们可以安全地使用@Async 方法，而不用担心安全上下文。

### 总结

根据定义，Spring Security 是线程绑定的，但默认情况下还不能用于多线程环境。然而，通过简单的步骤，我们能够解决这个问题。