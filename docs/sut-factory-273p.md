# SUT 工厂

> 原文：<https://dev.to/matheusrodrigues/sut-factory-273p>

SUT 工厂可能是一个奇特的名字，它基本上是一个实用方法，用于创建被测系统的一个实例。SUT 工厂的主要目的是减少代码重复，提高单元测试套件的可维护性。

我将向您展示一个如何实现这种类型的实用方法的快速示例。

想象一个这样的测试:

```
[TestMethod]
public void TestUsingConstructor()
{
    //Arrange
    MySUT sut = new MySUT();

    //Act
    //...Exercise the sut

    //Assert
    //...Verifying the results
} 
```

[继续阅读...](https://www.matheus.ro/2017/09/25/sut-factory/)