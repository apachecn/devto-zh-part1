# 用更快的枚举节省纳秒。哈斯标志

> 原文：<https://dev.to/adamkdean/save-nanoseconds-with-a-quicker-enum-hasflag-3l43>

管理安全标志的最简单的方法之一是拥有一个枚举并使用按位比较，然而你会期望。NET 来让事情变得简单一点，毕竟谁想开始用按位表达式来处理代码呢？

。NET*实际上让你更容易，允许你用`Enum.HasFlag`方法检查标志，但是它有类型检查，而且通常很慢。就像在水中缓慢的奔跑。*

 *让我们首先创建一个枚举:

```
[Flags]
public enum UserFlag
{
    User = 1,
    Moderator = 2,
    Administrator = 4,
    Developer = 8
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以给一个假设的`User`一些标志。我们可以让他们只是一个`User`，或者一个`User`和一个`Moderator`。我们可以把它们都做成四个。让我们让他们成为有权限的用户，这是一个非常受欢迎的选择。

```
UserFlag flags = UserFlag.User | UserFlag.Moderator; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们有一个只允许版主进入的部分，我们可以用 bitwise:

```
if ((flags & UserFlag.Moderator) == UserFlag.Moderator)
{
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，但是对于一个阅读代码的新手来说可能会很困惑。更简单的方法是使用`HasFlag`:

```
if (flags.HasFlag(UserFlag.Moderator))
{
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

穿着整齐，提着一篮子面包在水中奔跑。`HasFlag`如此缓慢的原因是因为类型检查，我们真的不需要类型检查，我们知道类型，我们只是想要检查。更简单的方法是拥有一个扩展方法:

```
public static class UserFlagExtensions
{
    public static bool HasFlagQuick(this UserFlag haystack, UserFlag needle)
    {
        return (haystack & needle) == needle;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能会问这要快多少？我分别运行了 1000 万次检查，结果非常明显。去掉不必要的类型检查给了我们一个比 T1 快 15 倍的方法，每次调用节省了 32.8 纳秒。

```
Method         Count        Elapsed Total   Elapsed Each
HasFlag        10,000,000   0.390 s         39.0 nano seconds
HasFlagQuick   10,000,000   0.062 s          6.2 nano seconds 
```

Enter fullscreen mode Exit fullscreen mode*