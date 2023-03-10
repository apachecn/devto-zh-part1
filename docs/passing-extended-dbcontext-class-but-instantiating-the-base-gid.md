# 传递扩展的 DbContext 类，但实例化基

> 原文：<https://dev.to/adamkdean/passing-extended-dbcontext-class-but-instantiating-the-base-gid>

在使用 [EasyAuth](http://github.com/Imdsm/EasyAuth/) 时，我遇到了一个小问题，我如何在允许程序员使用他们的实体类的同时使用用户存储的实体。我想，也许他们应该能够扩展 EasyAuth DbContext，并传递回他们的自定义版本，然后 EntityUserStore 可以将其用于存储。

但是我不能让它工作。

所以我把它带到了一个更小的项目中，做了些小改动，这确实有效。即使没有注释掉的位，如果需要，它仍然会创建其他表。太棒了。

更新:在 contextType 中增加了默认类型，在基类中增加了一个无参数的构造函数，以防程序员从来不给扩展类。如果不设置上下文，它仍然可以工作，但是不会创建额外的扩展表等。

```
class Program
{
    static void Main(string[] args)
    {
        UserStorage storage = new UserStorage();
        storage.SetContext(typeof(ExtensionContext));
        storage.AddUser();

        /*using (var context = new ExtensionContext())
        {
            Item item = new Item { ItemName = "My Item" };
            context.Items.Add(item);

            context.SaveChanges();
        }*/

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
}

public class UserStorage
{
    private Type contextType = typeof(BaseContext);

    public void AddUser()
    {
        using (var context = (BaseContext)Activator.CreateInstance(contextType))
        {
            User user = new User { Username = "username", Password = "password" };
            context.Users.Add(user);
            context.SaveChanges();
        }
    }

    public void SetContext(Type type)
    {
        contextType = type;
    }
}

// ---- base ---------------------------------------------------------------

public class BaseContext : DbContext
{
    public BaseContext()
        : base("DefaultConnection")
    {
    }

    public BaseContext(string connectionString = "DefaultConnection")
        : base(connectionString)
    {
    }

    public DbSet<user> Users { get; set; }
}

public class User
{
    public int UserId { get; set; }
    public string Username { get; set; }
    public string Password { get; set; }
}

// ---- extension ----------------------------------------------------------

public class ExtensionContext : BaseContext
{
    public ExtensionContext()
        : base("DefaultConnection")
    {
    }

    public DbSet<item> Items { get; set; }
}

public class Item
{
    public int ItemId { get; set; }
    public string ItemName { get; set; }
} 
```