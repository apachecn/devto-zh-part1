# 用 Java 参数化构造函数反序列化 json

> 原文：<https://dev.to/jeetmp3/deserialize-json-with-java-parameterized-constructor-1gfl>

嗨，在我之前的博客[中，json 使用 Gson 和 Jackson](https://dev.to/blogs/2016/09/07/JSON-deserialize-generic-types-using-Gson-and-Jackson) 反序列化泛型，我谈到了如何在 Java 泛型中反序列化 JSON。在这篇博文中，我们将学习如何将 json 反序列化为没有默认构造函数的 Java 类。对于这个博客，我使用杰克逊图书馆。

几乎所有的框架都要求在你的类中有默认的/无参数的构造函数。因为这些框架使用[反射](https://docs.oracle.com/javase/tutorial/reflect/)通过调用默认构造函数来创建对象。但是如果类中没有默认的构造函数，那么就很难使用反射来实例化。假设我们有一个不带参数的构造函数的类(只有一个参数化的构造函数),我们想反序列化它。