# 如何在 EntityFramework Core 2.0 中从 DbSet 中获取 DbContext？

> 原文：<https://dev.to/j_sakamoto/how-to-get-a-dbcontext-from-a-dbset-in-entityframework-core-c6m>

在 EntityFramework 核心编程中，可以像这样从 DbSet 对象中获取 DbContext 对象:

```
using System;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Infrastructure;
using Microsoft.EntityFrameworkCore.Internal;

public static class DbContextGetter
{
  public static DbContext GetDbContext<T>(this DbSet<T> dbSet) where T: class
  {
    var infrastructure = dbSet as IInfrastructure<IServiceProvider>;
    var serviceProvider = infrastructure.Instance;
    var currentDbContext = serviceProvider.GetService(typeof(ICurrentDbContext))
                               as ICurrentDbContext;
    return currentDbContext.Context;
  }
}

// How to use:
// 
// class MyDbContext {
//    public DbSet<FooBar> FooBar { get; set; }
// }
// ...
// var myContext = new MyDbContext();
// var contextFromDbSet = myContext.FooBar.GetDbContext();
//                                         ~~~~~~~~~~~~~~
// myContext == contextFromDbSet; // -> true 
```

Enter fullscreen mode Exit fullscreen mode

快乐编码:)