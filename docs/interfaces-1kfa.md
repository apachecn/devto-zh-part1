# 接口

> 原文：<https://dev.to/funkysi1701/interfaces-1kfa>

我试图理解接口以及何时在我的代码中使用它们。

接口定义了一个契约，任何实现该接口的类都同意履行该契约。

让我们看一个例子，因为这是我学习最好的方式。

大多数应用程序需要某种数据来工作，所以让我们从定义可以加载数据的 IData 开始。

> ```
> public interface IData{ Blog LoadData(string Connectionstring);} 
> ```

我的接口定义了一个方法 LoadData，并输出一个名为 Blog 的对象(稍后我会解释为什么)

一个常见的数据源是 SQL 数据库，因此我们可以定义一个实现 IData 的 SQL 类。

> ```
> public class SQL : IData{ public Blog LoadData(string Connectionstring) { Blog blog = new Blog(); using (SqlConnection con = new SqlConnection(Connectionstring)) { con.Open(); //etc 
> ```

我们还可以从博客的 RSS 提要中获取数据(因此我之前称之为对象博客)

> ```
> public class XML : IData{ public Blog LoadData(string Connectionstring) { XmlDocument myXmlDocument = new XmlDocument(); myXmlDocument.Load(Connectionstring); Blog blog = new Blog(); foreach (XmlNode RootNode in myXmlDocument.ChildNodes) { //etc 
> ```

这两个类都实现了 IData 并有一个名为 LoadData 的方法，该方法有一个字符串参数并输出一个 blog 对象。string 参数可以是 SQL 数据库的连接字符串，也可以是 rss 提要的 URL。不确定是否有更好的方法，也许字符串的名字需要更通用。

现在我们有了一些实现接口的类，我们可以用它们做什么。让我们编写一个名为 GetData 的类，它获取数据，但不关心数据是来自 rss 提要还是 SQL 数据库。

> ```
> public class GetData    {        private IData \_repo;         public GetData(IData repo) { \_repo = repo; }         public Blog LoadData(string Connectionstring)        {            var original = \_repo.LoadData(Connectionstring);            return original;        }    } 
> ```

当我们调用 GetData 时，我们可以传入 XML 或 SQL，因为这个类不依赖于任何一个实现。出于测试目的，我们甚至可以编写实现 IData 的其他类。

我的完整代码可以在 [github](https://github.com/funkysi1701/InterfaceExample) 上找到。

以这种方式编写代码的优点包括代码更容易扩展、测试和维护。这仅仅是我理解的开始，我确信这将是我在接下来的几周内回来讨论的话题。