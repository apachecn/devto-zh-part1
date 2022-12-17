# 一个方便的 C#异步实用方法

> 原文：<https://dev.to/danieljsummers/a-handy-c-async-utility-method>

在利用新的(对于 4.5.1) [基于任务的异步编程](////msdn.microsoft.com/EN-US/library/vstudio/hh191443(v=vs.110).aspx)编写 C#代码的过程中，我遇到过几个地方不允许使用“await”关键字(catch 块或属性访问器)，或者“async”关键字使语法变得非常复杂(lambda 表达式)。我发现自己在为两个不同的项目编写这个方法，所以我想我会把这个 Q & D，注释比代码多的实用方法放在这里，如果你认为有必要的话，让其他人使用。

*(更新:这在控制台应用程序中运行良好；它会导致桌面和 web 应用程序死锁。依赖它之前先测试一下。进一步更新:为了解决这个问题，在`Task.WaitAll`调用后添加`.ConfigureAwait(false)`。)*

```
/// <summary>
/// Get the result of a task in contexts where the "await" keyword may be prohibited
/// </summary>
/// <typeparam name="T">The return type for the task</typeparam>
/// <param name="task">The task to be awaited</param>
/// <returns>The result of the task</returns>
public static T TaskResult<T>(Task<T> task)
{
    Task.WaitAll(task);
    return task.Result;
} 
```

Enter fullscreen mode Exit fullscreen mode

而且，在你不能做这种事情的地方…

```
/// <summary>
/// A horribly contrived example class
/// </summary>
/// <remarks>Don't ever structure your POCOs this way, unless EF is handling the navigation properties</remarks>
public class ExampleClass
{
    /// <summary>
    /// A contrived ID to a dependent entity
    /// </summary>
    public int ForeignKeyID { get; set; }

    /// <summary>
    /// The contrived dependent entity
    /// </summary>
    public DependentEntity DependentEntity
    {
        get
        {
            // Does not compile; can't use await without async, can't mark a property as async
            return await Data.DependentEntities
                .FirstOrDefaultAsync(entity => entity.ID == ForeignKeyID);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

…您可以在“DependentEntity 属性…
”中这样做

```
 /// <summary>
    /// The contrived dependent entity
    /// </summary>
    public DependentEntity DependentEntity
    {
        get
        {
            return UtilClass.TaskResult<DependentEntity>(Data.DependentEntities
                .FirstOrDefaultAsync(entity => entity.ID == ForeignKeyID));
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

*(dev . to post[2017 . 8 . 4]更新):关于 C#和`async` / `await`，我了解了很多。如果你的外部依赖定义了一个非 T2 API，使用它而不是用这个包装它。)*

*本帖最初发表于[techblog.djs-consulting.com](https://techblog.djs-consulting.com/2014/a-handy-c-sharp-async-utility-method.html)T3】*