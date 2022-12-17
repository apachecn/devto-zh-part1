# 如何在 EntityFramework Core 2.0 中从 DbSet 获取实际的表名？

> 原文：<https://dev.to/j_sakamoto/how-to-get-the-actual-table-name-from-dbset-in-entityframework-core-20-56k0>

# 获取实际表名的方式

例如，如果你有这样的 DbContext 对象(C#):

```
public class MyDbContext : DbContext {
    public DbSet<FooBar> FooBars { get; set; }
}
...

var dbContext = new MyDbContext(); 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用以下代码从 DbContext 实例中获取 DbSet 属性的实际表名。

```
// DbContext knows everything about the model.
var model = dbContext.Model;

// Get all the entity types information contained in the DbContext class, ...
var entityTypes = model.GetEntityTypes();

// ... and get one by entity type information of "FooBars" DbSet property.
var entityTypeOfFooBar = entityTypes.First(t => t.ClrType == typeof(FooBar));

// The entity type information has the actual table name as an annotation!
var tableNameAnnotation = entityTypeOfFooBar.GetAnnotation("Relational:TableName");
var tableNameOfFooBarSet = tableNameAnnotation.Value.ToString(); 
```

Enter fullscreen mode Exit fullscreen mode

# 包作为扩展方法

顺便说一下，我们可以从 DbSet 中获取 DbContext。

另见:[“如何在 EntityFramework Core 2.0 中从 DbSet 获取 DbContext。”](https://dev.to/j_sakamoto/how-to-get-a-dbcontext-from-a-dbset-in-entityframework-core-c6m)

所以，你可以像这个 C#代码一样，把上面的代码打包成一个扩展方法:

```
using System;
using System.Linq;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Infrastructure;

// Prerequire: You have to install DbContextGetter class in this project.
// https://dev.to/j_sakamoto/how-to-get-a-dbcontext-from-a-dbset-in-entityframework-core-c6m

public static class TableNameOfDbSetGetter
{
  public static string GetTableName<T>(this DbSet<T> dbSet) where T: class
  {
    var dbContext = dbSet.GetDbContext();

    var model = dbContext.Model;
    var entityTypes = model.GetEntityTypes();
    var entityType = entityTypes.First(t => t.ClrType == typeof(T));
    var tableNameAnnotation = entityType.GetAnnotation("Relational:TableName");
    var tableName = tableNameAnnotation.Value.ToString();
    return tableName;
  }
}

// How to use:
// 
// class MyDbContext : DbContext {
//    public DbSet<FooBar> FooBars { get; set; }
// }
// ...
// var myContext = new MyDbContext();
// var tableName = myContext.FooBars.GetTableName();
//                                  ~~~~~~~~~~~~~~
// tableName; // -> "FooBars" 
```

Enter fullscreen mode Exit fullscreen mode

即使实体类用`[TableAttribute("TableName")]`修饰，这段代码也能很好地工作。

快乐编码:)