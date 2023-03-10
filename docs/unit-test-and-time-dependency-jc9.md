# 单元测试和时间依赖

> 原文：<https://dev.to/matheusrodrigues/unit-test-and-time-dependency-jc9>

当你开始编写单元测试时，不可避免地你会遇到一个困难时期，当一个功能依赖于时间的时候。取决于日期时间。现在对单元测试来说效果不好，你的测试在一天中的某些时间会通过，而在其他时间会失败。

为了解决这个问题，我们需要隔离系统的时间依赖性，以便能够使我们的单元测试可靠。

## **例子**

因此，我们有一个商店，它根据一天中的小时提供一些折扣，我们有一个方法返回折扣的多少:

```
public class Shop
{
    //Constructor
    //public Shop(.....

    //Other Implementations
    //.....

    public int GetCurrentDiscount()
    {
        switch (DateTime.Now.Hour)
        {
            case 9:
                return 30;
            case 12:
                return 20;
            case 18:
                return 40;
            default:
                return 0;
        }
    }
} 
```

[继续阅读...](https://www.matheus.ro/2017/09/25/unit-test-time-dependency/)