# 如何写一个不烂的面向对象程序

> 原文：<https://dev.to/naveen/how-to-write-an-object-oriented-program-that-doesnt-suck>

很明显，面向对象程序已经成为现代程序设计中的一颗银弹。无论你是一名技术架构师还是一名计算机科学专业的学生，OOP 的优点都是非常重要的。

我看到许多计算机程序员自豪地宣称“是的，我以面向对象的方式设计了我的代码——我将我的数据成员定义为私有的，它们只能通过公共方法访问——我创建了遵循层次结构的基类和子类——我在基类中创建了虚拟方法，这些方法共享可以在派生类中使用的常见行为。”这就是面向对象的全部吗？绝对不止这些。

首先，我们需要理解使用面向对象编程背后的目的。除了为执行一项任务而编写的、只运行一次的一次性小程序之外，复杂的软件应用程序总是需要更多的修改。您的源代码需要定期更新，以便为系统添加新功能或修复错误。面向对象编程组织你的代码，使改变更容易。除此之外，面向对象编程允许将更大更复杂的问题分解成更小的可重用模块，这为管理大型代码库提供了一致的方法。

> 大多数程序员认为他们理解面向对象的基础。当涉及到将它应用到现实世界的应用时，他们很难做到。

我们大多数人都不擅长面向对象编程——这没关系。成为一名优秀的人所需要的是耐心、练习和对自己的一点信心。面向对象不仅是一种编程技术，而且是一种思维方式的转变，这需要大量的实践和良好的设计。

**怎么练？**

我在 Medium 写了一篇关于[拖延症](https://medium.com/@naveenrtr/procrastinating-the-procrastination-4c9dee48d1b3)的文章，解释了如何从小事做起。最初尝试学习面向对象编程可能看起来令人生畏，势不可挡。不要把事情搞得太复杂，也不要试图一次学会所有的东西，试着一步一步来，试着理解在你的代码中应用面向对象设计背后的动机。通过练习如何设计系统，你将开始对对象关系有一种自然的感觉。然后，翻译成代码。你将开始看到可以被抽象、模块化的公共领域，以及其他可以改善你的设计和代码的方法。

如何判断我的代码是否糟糕？

我们的大多数代码在许多方面都不完美，要列出所有可能的缺陷需要很长时间。然而，我将列出一些最常见和最常见的错误，并给出一些建议来修复它们。

*   向不相关的类添加更多的功能或职责。考虑下面的例子

```
class Account
{    
   public void Withdraw(decimal amount)    
   {        
      try        
      {            
          // logic to withdraw money 
      }        
      catch (Exception ex)        
      {            
          System.IO.File.WriteAllText(@"c:\logs.txt",ex.ToString());   
      }    
   }
} 
```

你能找出上面代码中的问题吗？Account 类承担了向日志文件添加异常的额外责任。将来，如果需要更改日志记录机制，那么 Account 类也需要更改。嗯，这好像不对。

**修复:**

一个类必须只承担一项责任，并且应该只有一个改变类的理由。这可以通过将日志记录活动转移到一个单独的类来解决，该类只负责记录异常并将其写入日志文件。

```
public class Logger
{
   public void Handle(string error)
   {
       System.IO.File.WriteAllText(@"c:\logs.txt", error);
   }
 } 
```

现在，Account 类可以灵活地将日志记录活动委托给 Logger 类，它可以只关注与帐户相关的活动。

```
class Account
{
   private Logger obj = new Logger();    
   public void Withdraw(decimal amount)    
   {        
      try        
      {            
          // logic to withdraw money 
      }        
      catch (Exception ex)        
      {            
          obj.Handle(ex.ToString());
      }    
   }
} 
```

*   假设有一个处理不同账户类型(如储蓄账户和活期账户)的新需求。为了迎合这一点，我向 Account 类添加了一个名为“AccountType”的属性。根据账户类型的不同，利率也不同。我写了一个方法来计算账户类型的利息 d。

```
class Account
{
   private int _accountType;

   public int AccountType
   {
      get { return _accountType; }
      set { _accountType = value; }
   } 
   public decimal CalculateInterest(decimal amount)    
   {        
      if (_accountType == "SavingsAccount")
      {
         return (amount/100) * 3;
      }
      else if (_accountType == "CurrentAccount")
      {
         return (amount/100) * 2;
      }
   }
} 
```

上述代码的问题是“分支”。如果将来有一个新的账户类型，账户类也必须改变，以满足线性增加代码 if-else 条件数量的要求。请注意，我们会为每次更改更改 Account 类。这显然不是一个可扩展的代码。

**修复:**

开放代码以进行扩展，关闭代码以进行修改，这始终是一个好的做法。每当需要添加新的帐户类型时，我们是否可以尝试通过添加新的类来扩展代码，并从 account 类继承它？通过这样做，我们不仅抽象了 Account 类，还允许它在它的子类之间共享公共行为。

```
public class Account
{
   public virtual decimal CalculateInterest(decimal amount)
   {
       // default 0.5% interest
       return (amount/100) * 0.5;
   }
}
public class SavingsAccount: Account
{
   public override decimal CalculateInterest(decimal amount)
   {
       return (amount/100) * 3;
   }
}
public class CurrentAccount: Account
{
   public override decimal CalculateInterest(decimal amount)
   {
       return (amount/100) * 2;
   }
} 
```

*   请继续关注我，从现在开始，这将变得更加有趣——比如说，该公司即将推出名为在线账户的新账户类型。这个账户有一个 5%的好利率，并拥有类似于储蓄银行账户的所有好处。但是，你不能从自动取款机上取钱。你只能电汇钱。实现如下所示

```
public class Account
{
   public virtual void Withdraw(decimal amount)
   {
       // base logic to withdraw money
   }
   public virtual decimal CalculateInterest(decimal amount)
   {
       // default 0.5% interest
       return (amount/100) * 0.5;
   }
}
public class SavingsAccount: Account
{
   public override void Withdraw(decimal amount)
   {
        // logic to withdraw money
   }
   public override decimal CalculateInterest(decimal amount)
   {
       return (amount/100) * 3;
   }
}
public class OnlineAccount: Account
{
   public override void Withdraw(decimal amount)
   {
        throw new Exception("Not allowed");
   }
   public override decimal CalculateInterest(decimal amount)
   {
       return (amount/100) * 5;
   }
} 
```

现在，假设我想关闭我在这家银行的所有账户。所以，在完全关闭账户之前，必须取出每个账户的所有钱。

```
public void CloseAllAccounts()
{
    // Retrieves all accounts related to this customer
    List<Account> accounts = customer.GetAllAccounts();
    foreach(Account acc in accounts)
    {
      // Exception occurs here
      acc.Withdraw(acc.TotalBalance);
    }
} 
```

按照继承层次结构，Account 对象可以指向它的任何一个子对象。编译期间没有发现异常行为。然而，在运行时，它抛出一个异常“不允许”。我们由此推断出了什么？父对象不能无缝替换子对象。

**修复:**

让我们创建两个接口——“一个处理利息(IProcessInterest ),另一个处理提款(IWithdrawable)

```
interface IProcessInterest
{
    decimal CalculateInterest(decimal amount);
}
interface IWithdrawable
{
    void Withdraw(double amount);
} 
```

OnlineAccount 类将只实现 IProcessInterest，而 Account 类将同时实现 IProcessInterest 和 IWithdrawable。

```
public class OnlineAccount: IProcessInterest
{
    public decimal CalculateInterest(decimal amount)
    {
       return (amount/100) * 5;
    }
}
public class Account: IProcessInterest, IWithdrawable
{
   public virtual void Withdraw(decimal amount)
   {
       // base logic to withdraw money
   }
   public virtual decimal CalculateInterest(decimal amount)
   {
       // default 0.5% interest
       return (amount/100) * 0.5;
   }
} 
```

现在，这看起来很干净。我们可以创建一个 IWithdrawable 的列表，并向其中添加相关的类。如果将 OnlineAccount 添加到 GetAllAccounts 方法内的列表时出错，我们将会得到一个编译时错误。

```
public void CloseAllAccounts()
{
    // Retrieves all withdrawable accounts related to this customer
    List<IWithdrawable> accounts = customer.GetAllAccounts();
    foreach(Account acc in accounts)
    {
      acc.Withdraw(acc.TotalBalance);
    }
} 
```

*   假设我们的账户类需求超高。企业提出了公开一个 API 的想法，该 API 允许从不同的第三方银行的 ATM 上取款。我们公开了一个 web 服务，其他所有银行都开始使用这个 web 服务来取款。到现在为止一切听起来都很好。几个月后，该企业提出了另一个要求，即其他一些银行也要求设置该帐户从其 ATM 上取款的限额。没问题，提出变更请求——很容易做到。

```
interface IWithdrawable
{
   void Withdraw(decimal amount);
   void SetLimit(decimal limit);
} 
```

我们刚刚做的事情很奇怪。通过改变现有的界面，你增加了一个突破性的变化，并扰乱了所有满意的银行，这些银行真的很高兴消费我们的网络服务，只是退出。现在，您正在强迫他们也使用新公开的方法。一点都不好。

**修复:**

解决这个问题的最好方法是创建一个新的接口，而不是修改现有的接口。当前接口 IWithdrawable 可以保持原样，并且创建了实现 IWithdrawable 的新接口——例如 IExtendedWithdrawable。

```
interface IExtendedWithdrawable: IWithdrawable
{
   void SetLimit(decimal limit);
} 
```

因此，旧客户端将继续使用 IWithdrawable，新客户端可以使用 IExtendedWithdrawable。简单，但是有效！

*   让我们回到我们解决的第一个问题，我们添加了 Logger 类来委托 Account 类的日志责任。它将异常记录到文件中。有时，通过电子邮件获取日志文件很容易，或者将它与一些第三方日志查看器集成在一起以便于访问。让我们实现这一点。

```
interface ILogger
{
    void Handle(string error);
}
public class FileLogger : ILogger
{
   public void Handle(string error)
   {
       System.IO.File.WriteAllText(@"c:\logs.txt", error);
   }
}
public class EmailLogger: ILogger
{
   public void Handle(string error)
   {
       // send email
   }
}
public class IntuitiveLogger: ILogger
{
   public void Handle(string error)
   {
       // send to third party interface
   }
}
class Account : IProcessInterest, IWithdrawable
{
   private ILogger obj;    
   public void Withdraw(decimal amount)    
   {        
      try        
      {            
          // logic to withdraw money 
      }        
      catch (Exception ex)        
      {            
          if (ExType == "File")
          {
             obj = new FileLogger();
          }
          else if(ExType == "Email")
          {
             obj = new EmailLogger();
          }
          obj.Handle(ex.Message.ToString());
      }    
   }
} 
```

我们编写了一个优秀的可扩展代码“ILogger ”,作为其他日志记录机制的通用接口。然而，我们再次违反了 Account 类，因为我们赋予了更多的责任来决定创建日志类的哪个实例。决定为日志记录创建对象不是 Account 类的工作。

**修复:**

解决方案是将依赖关系“反转”到其他类，而不是 Account 类。让我们通过 Account 类的构造函数注入依赖关系。因此，我们将责任推给客户端，让其决定必须应用哪种日志记录机制。客户端可能又依赖于应用程序配置设置。至少在本文中，我们可能不担心配置级别的理解。

```
class Account : IProcessInterest, IWithdrawable
{
   private ILogger obj;
   public Customer(ILogger logger)
   {
       obj = logger;
   }
}
// Client side code
IWithdrawable account = new SavingsAccount(new FileLogger()); 
```

如果你密切关注，你可能很容易猜到所有这些建议的修正只不过是面向对象设计的坚实原则的应用。我将让您来探索和分析我是如何将这些设计原则的每个方面应用到代码中的。

如果你和我一样相信面向对象编程是思维方式的转变，请分享这篇文章，以便其他人可以从中学习。T3】

这篇[文章](https://medium.com/@naveenrtr/how-to-write-an-object-oriented-program-that-doesnt-suck-8bbac5417d71)最初发表在我的[媒体博客](https://medium.com/@naveenrtr)