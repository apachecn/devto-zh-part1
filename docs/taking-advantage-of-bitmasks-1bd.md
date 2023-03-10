# 利用位掩码

> 原文：<https://dev.to/ianjudderparr/taking-advantage-of-bitmasks-1bd>

[![Binary](img/9add0bedd13300fcca13f637d0c3714c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wuNeLMpt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8b25vhmztxkltsgmodqn.jpeg) 
最近，我需要一种方法来存储一个有限列表或异步过程中可能出现的错误消息。

我本可以实现一些类似的东西——创建一个关系表结构，将它们存储为一个 JSON 文件等。所有的选择都有点像“大材小用”,原因我就不多说了。所以我决定选择位掩码。为什么？嗯，从本质上说，位掩码是在一个小空间里存储大量“信息”的好方法。

以下是我需要记录的潜在错误。

*   invalidName
*   无效日期
*   无效时间
*   无效类型
*   无效范围
*   无效状态

所以位屏蔽允许我做的是给每个潜在的错误分配一个整数值。所以:

*   invalidName = 1
*   invalidDate = 2
*   invalidTime = 4
*   invalidType = 8
*   invalidRange = 16
*   无效状态= 32

*用法*
在 C#中我们会实现这样的东西:

```
using System;

public class Program
{
    static int _errorTotal = 0;

    public static void Main()
    {           
        //simulate some errors
        _errorTotal += (int)Errors.Name;
        _errorTotal += (int)Errors.Date;
        _errorTotal += (int)Errors.Time;
        _errorTotal += (int)Errors.Type;

        Console.WriteLine("Error total: {0}", _errorTotal); // this is what we persist
        Console.WriteLine("Error list: {0}", (Errors)_errorTotal); // this is how we read the data back out
    }

    // Holds the value of each of the error types
    [Flags]
        public enum Errors 
    {
        Name = 1,
        Date = 2,
        Time = 4,
        Type = 8,
        Range = 16,
        State = 32
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:
`Error total: 15
Error list: Name, Date, Time, Type`

我们现在可以看到，只存储一个整数值，就可以访问异步过程中发生的任何错误组合。

注意:就像我在开始所说的，这是一个存储有限“东西”列表的好方法——对于特别长的“东西”列表，你可能想要将你的`[Flag]`枚举转换成`(long)` :

```
[Flags]
public enum Errors : long
{
    Name = (long)1,
    Date = (long)2,
    Time = (long)4,
        ... 
```

Enter fullscreen mode Exit fullscreen mode