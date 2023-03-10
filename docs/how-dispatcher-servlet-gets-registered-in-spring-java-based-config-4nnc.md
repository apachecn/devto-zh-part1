# 如何在基于 Spring java 的配置中注册 Dispatcher servlet

> 原文：<https://dev.to/jeetmp3/how-dispatcher-servlet-gets-registered-in-spring-java-based-config-4nnc>

几天前，我的同事问我如何在基于 Java 的配置中注册 Spring Dispatcher servlet，因为配置中没有使用 XML。关于类`AbstractAnnotationConfigDispatcherServletInitializer`注册了那些 servlet 和所有的配置，有一点我是肯定的(实际上我们两个都是肯定的)。但问题是怎么做？

为了理解这一点，我们将回到 servlet 世界。根据 [Servlet Java Doc](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletContainerInitializer.html?is-external=true) ，`ServletContainerInitializer`是用来注册 Servlet、过滤器和其他相关东西的接口。

因此，`ServletContainerInitializer`的任何实现都将使用回调方法
被通知 web 应用程序的启动阶段

```
public void onStartup(Set<Class<?>> c, ServletContext ctx) throws ServletException {

} 
```

在上面的方法中，你会注意到有一个类型集合<类<的参数 **c** ？> >。你可能在想为什么它会在那里。这是魔法的另一部分，在`ServletContainerInitializer`旁边有一个叫做`@HandlesTypes`的注释。该注释接受 Class 数组作为输入参数。

```
public @interface HandlesTypes {

    Class<?>[] value();
} 
```

因此，任何作为参数传递给注释(`HandlesTypes`)的类(及其子类)都将被容器扫描，最后，这些类的集合将在启动时传递给`ServletContainerInitializer#onStartup()`。

现在你可能会想，容器是如何选择`ServletContainerInitializer`的实现的？所以这个问题的答案是:

根据 [Java 文档](https://docs.oracle.com/javaee/7/api/javax/servlet/ServletContainerInitializer.html?is-external=true)

> 该接口的实现必须由位于 META-INF/services 目录中的 JAR 文件资源声明，并以该接口的完全限定类名命名，并且将使用运行时的服务提供者查找机制或语义上等效的特定于容器的机制来发现。在任一种情况下，必须忽略从绝对排序中排除的 web 片段 JAR 文件中的 ServletContainerInitializer 服务，并且发现这些服务的顺序必须遵循应用程序的类加载委托模型。

现在回到春天时代，检查一下 spring-web*。jar(只需提取 jar)并查找文件**javax . servlet . servletcontainerinitializer**。在该文件中，您会发现一个完全限定的类名，如**org . spring framework . web . springservletcontainerinitializer**。

就这样，你解开了谜团！！

现在检查那个类的源代码(转到 [GitHub](https://github.com/spring-projects/spring-framework/blob/master/spring-web/src/main/java/org/springframework/web/SpringServletContainerInitializer.java) ，你会注意到`@HandlesTypes`里面有一个类`WebApplicationInitializer`，它是大多数 Spring 初始化器(`AbstractAnnotationConfigDispatcherServletInitializer`、`AbstractSecurityWebApplicationInitializer`)的父类。

现在你知道如何创建你自己的初始化器了。

干杯！！！快乐编码。

请分享你的想法。☺ ☺