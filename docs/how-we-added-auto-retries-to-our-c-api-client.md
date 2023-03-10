# 我们如何向 C# API 客户端添加自动重试功能

> 原文：<https://dev.to/rogerjin12/how-we-added-auto-retries-to-our-c-api-client>

*想了解更多类似的内容，请在推特上关注 [ButterCMS，并订阅我们的](https://twitter.com/ButterCMS)[博客](https://buttercms.com/blog/)。*

网络是不可靠的。在某些时候，我们都经历过连接 Wi-Fi 的问题或突然掉线的情况。

连接我们服务器的网络通常比蜂窝网络和家庭 ISP 更可靠，但是如果有足够多的信息通过网络传输，它们仍然会以不可预测的方式出现故障。停机、路由问题和其他间歇性故障在统计上可能很少发生，但在某种环境背景下仍然会一直发生。

为了克服这种固有的不可靠环境，重要的是要设计出在出现故障时仍然健壮的 API 和客户机。一个简单的策略是让客户端针对远程服务重试操作。让我们看看我们的 [C# API 客户端](https://github.com/buttercms/buttercms-csharp)和增加自动重试的演练:

## 潜入 C# API 客户端

[ButterCMS](../) 是一个“内容管理系统即服务”——CMS 的数据库、逻辑和管理仪表板作为托管服务提供，其内容通过 web API 提供。您可以通过其 API 客户端检索内容，并将其插入您的网站。在 C#中，API 方法可以通过一个[单个类](https://github.com/ButterCMS/buttercms-csharp/blob/master/ButterCMS/ButterCMSClient.cs)来调用。

让我们来看看这个类的结构。它有许多公共方法，通过私有的 Execute(string queryString)和 ExecuteAsync(string queryString)方法发送 API 请求。为了简单起见，我们只处理 Executemethod 和它的同步调用者。下面是一个公共方法，用于检索博客文章列表:

```
private string authToken; // Authorization token set in the ButterCMSClient constructor
private const string retrievePostsEndpoint = "v2/posts/{0}"; // Base URL for blog posts on the API

// ... Code excluded for brevity ...

public PostResponse RetrievePost(string postSlug)
{
    var queryString = new StringBuilder();
    queryString.Append(string.Format(retrievePostEndpoint, postSlug));
    queryString.Append("?");
    queryString.Append(authTokenParam);
    var postResponse = JsonConvert.DeserializeObject<PostResponse>(Execute(queryString.ToString()), serializerSettings);
    return postResponse;
} 
```

简单明了。正如您所看到的，它采用了一个 postSlug 参数(这只是标识我们想要加载的博客文章的唯一 URL 段)，将它组装到 ButterCMS 服务器上的文章的 URL 中，并将其传递给 Execute(string queryString)方法，该方法获取一个 JSON 响应并将其返回，以便封送到我们的 PostResponseclass 中。然后，我们可以获取这些数据，并将其呈现在我们公共网站的页面模板中。

让我们更深入地了解一下 Execute 方法内部发生了什么:

```
private HttpClient httpClient; // System.Net.Http.HttpClient instance, set in the ButterCMSClient constructor

// ... Code excluded for brevity ...

private string Execute(string queryString)
{
    try
    {
        var response = httpClient.GetAsync(queryString).Result;
        if (response.IsSuccessStatusCode)
        {
            return response.Content.ReadAsStringAsync().Result;
        }
        if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
        {
            throw new InvalidKeyException("No valid API key provided.");
        }
        if (response.StatusCode >= System.Net.HttpStatusCode.InternalServerError)
        {
            throw new Exception("There is a problem with the ButterCMS service");
        }
    }
    catch (TaskCanceledException taskException)
    {
        if (!taskException.CancellationToken.IsCancellationRequesâ€Œâ€‹ted)
        {
            throw new Exception("Timeout expired trying to reach the ButterCMS service.");
        }
        throw taskException;
    }
    catch (HttpRequestException httpException)
    {
        throw httpException;
    }
    catch (Exception ex)
    {
        throw ex;
    }
    return string.Empty;
} 
```

这个方法只是向给定的 URL 发出一个 HTTP GET 请求，并以字符串的形式返回响应主体，调用者可以将响应主体解析为 JSON、XML 等。它有一些内置的错误检查，用于在错误响应的情况下抛出异常。这可以防止调用方意外地试图将它们解析为合法数据。

现在您已经看到了 API 客户机是如何工作的，让我们添加自动重试功能吧！Â

## 实现自动重试

因为我们的示例 API 客户机实际上是只读的(只发出 GET 请求)，所以我们可以使用一种“哑”重试机制，简单地重新发送请求，直到一个请求成功或者我们超过了允许的最大重试次数

为此，我们需要“监视”Execute 方法，以便在它抛出异常时可以重新执行它。这可以通过捕捉异常的简单包装方法来完成。首先，让我们将旧的 Execute 方法重命名为 ExecuteSingle，以便更准确地表达其目的。

然后，让我们构建我们的包装方法。我们将调用它 Execute，这样我们现有的公共方法将调用它，而不是我们刚刚重命名的函数。现在，我们只做一个简单的包装器，不添加任何功能:

```
private string Execute(string queryString)
{
    return ExecuteSingle(queryString);
} 
```

API 客户端现在应该完全像以前一样工作，所以我们实际上还没有完成任何事情。让我们从编写一个简单的循环开始，在一定的次数内重试请求。为了在 ExecuteSingle 抛出异常的情况下“继续循环”,我们需要在循环内部捕获这些异常。

```
private string Execute(string queryString)
{
    // maxRequestTries is a private class member set to 3 by default, 
    // optionally set via a constructor parameter (not shown)
    var remainingTries = maxRequestTries;  

    do 
    {
        --remainingTries;
        try 
        {
            return ExecuteSingle(queryString);
        }
        catch (Exception) 
        {

        }
    }
    while (remainingTries > 0)
} 
```

如果请求成功，该代码将通过 return 语句退出循环。如果 ExecuteSingle 抛出一个异常，它将被吞掉，循环将继续到 maxRequestTries 次。do {...} while()语法确保请求总是至少执行一次，即使 maxRequestTries 配置错误并设置为 0 或-10 之类的值。

当然，这段代码有一个明显的问题——它包含了所有的异常。如果所有的请求都失败了，它将返回一个空字符串。但是我们该如何处理呢？我们不能从 catch (Exception) { }块内部抛出异常，否则执行将会脱离循环，从而违背整个方法的目的。我们应该在所有请求失败后抛出异常，并且只有当所有请求失败时才抛出异常。我们可以通过将它们聚集在一个列表中并在方法末尾抛出一个 AggregateException 来做到这一点。

```
private string Execute(string queryString)
{
    var remainingTries = maxRequestTries;  
    var exceptions = new List<Exception>();

    do 
    {
        --remainingTries;
        try 
        {
            return ExecuteSingle(queryString);
        }
        catch (Exception e) 
        {
            exceptions.Add(e);
        }
    }
    while (remainingTries > 0)

    throw new AggregateException(exceptions)
} 
```

如果所有请求都失败了，这个方法将抛出一个 AggregateException，其中包含每个请求中抛出的所有异常的列表。如果任何请求成功，将不会抛出异常，我们将只得到我们的响应字符串。这绝对够用。但是让我们把它变得更好一点——大多数重复的失败都是由一个持久的问题引起的，所以每个请求都会抛出完全相同的异常。如果我们所有的请求都抛出一个 InvalidKeyException(当我们的 API auth 令牌无效时就会发生这种情况)，我们真的想要返回一个包含 3 个相同 InvalidKeyException 的 AggregateException 吗？只抛出一个 InvalidKeyException 不是更符合人机工程学吗？为此，我们需要将异常列表中的任何重复项“折叠”成一个“代表性”异常。我们可以使用 Linq 的 Distinct 方法来做到这一点，但默认情况下它不会折叠异常，因为它们是...良好的...distinct 对象和 Distinct 将通过引用对它们进行比较。我们可以使用它的重载，该重载接受一个自定义的 IEqualityComparerthat，我们可以使用它来标识可以被视为重复的异常。下面是我们的实现:

```
private class ExceptionEqualityComparer : IEqualityComparer<Exception>
{
    public bool Equals(Exception e1, Exception e2)
    {
        if (e2 == null && e1 == null)
            return true;
        else if (e1 == null | e2 == null)
            return false;
        else if (e1.GetType().Name.Equals(e2.GetType().Name) && e1.Message.Equals(e2.Message))
            return true;
        else
            return false;
    }

    public int GetHashCode(Exception e)
    {
        return (e.GetType().Name + e.Message).GetHashCode();
    }
} 
```

如果两个异常共享相同的类型和消息属性，则此相等比较器认为它们相等。就我们的目的而言，这是“重复”的一个足够好的定义。

现在，我们可以折叠由请求尝试引发的重复异常:

```
private string Execute(string queryString)
{
    var remainingTries = maxRequestTries;  
    var exceptions = new List<Exception>();

    do 
    {
        --remainingTries;
        try 
        {
            return ExecuteSingle(queryString);
        }
        catch (Exception e) 
        {
            exceptions.Add(e);
        }
    }
    while (remainingTries > 0)

    var uniqueExceptions = exceptions.Distinct(new ExceptionEqualityComparer());

    if (uniqueExceptions.Count()) == 1)
        throw uniqueExceptions.First();

    return new AggregateException("Could not process request", uniqueExceptions);
} 
```

这更符合人体工程学。简而言之，我们只抛出由请求尝试产生的不同异常。如果只有一个，要么是因为我们只做了一次尝试，要么是因为多次尝试都因为同样的原因失败了，我们抛出这个异常。如果有多个异常，我们抛出一个 AggregateException，每个类型/消息组合一个

## 包装完毕

我们都完了！我们的 API 客户端现在更加健壮，能够承受与网络不可靠性相关的事件。如果您有兴趣进一步探索，请查看 Github 上的[完整代码和](https://github.com/buttercms/buttercms-csharp) [ButterCMS API 文档](../docs/api)。

*此文原帖[此处](https://buttercms.com/blog/adding-retries-to-csharp-api-client)。*