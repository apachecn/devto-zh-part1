# 使用实体框架通过 TCP/IP 连接到 SQL

> 原文：<https://dev.to/adamkdean/connecting-to-sql-over-tcp-ip-with-entity-framework-4l7i>

所以这让我很头疼，但我最终设法让它工作了。连接到远程 SQL server 的第一个实体代码。

首先是模型类:

```
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<post> Posts { get; set; }
}

public class User
{
    public int UserId { get; set; }
    public string DisplayName { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }

    public int UserId { get; set; }
    public virtual User User { get; set; }
} 
```

以及将它们结合在一起的上下文，请注意它在构造函数中有一个连接字符串名称:

```
public class BlogContext : DbContext
{
    public BlogContext() : base("name=BlogContextConnection") { }

    public DbSet<blog> Blogs { get; set; }
    public DbSet<post> Posts { get; set; }
    public DbSet<user> Users { get; set; }
} 
```

这与这个连接字符串有关，这是一个相当痛苦的工作。请注意，这是使用 SqlClient 而不是 EntityClient:

```
<connectionStrings>
  <add name="BlogContextConnection"
        providerName="System.Data.SqlClient"
        connectionString="Server=SERVER;Database=Blog;Integrated Security=False;User Id=USER;Password=PASSWORD;" />
</connectionStrings> 
```

当然，如果你愿意，你可以用 windows auth 连接，但是我是通过局域网和互联网连接的，所以我没有使用集成安全性。只是暂时的。