# 用 Kotlin 从网络响应到代数数据类型

> 原文：<https://dev.to/danielw/from-network-response-to-algebraic-data-type-10co>

或者，我从试图停止将 HTTP 错误代码作为异常处理中学到了什么。

前几天，我终于明白了几个月来一直困扰我的事情:我们如何避免使用异常来处理 HTTP 错误代码？更具体地说，在它们被作为异常抛出之前，我们如何将它们转换成不同的东西？以下是我学到的东西。

* * *

如果您正在使用 Kotlin，您可能已经听说过代数数据类型(ADT)的概念。
如果没有，还有一个很酷的[帖子](https://medium.com/car2godevs/kotlin-adt-74472319962a)来自 [Dmitry Zaytsev](https://medium.com/@dmitry.zaicew) 和[另一个](http://engineering.pivotal.io/post/algebraic-data-types-in-kotlin/)来自 [Mike Gehard](http://engineering.pivotal.io/authors/mikegehard/) 。

简而言之，这就是我们在这篇文章中需要了解的内容:
想法是将一堆对象和数据类封装在一个密封的类中，它们都继承自这个类。

```
typealias Second = Int

sealed class PowerUp {

    object FireFlower : PowerUp()

    object SuperMushroom : PowerUp()

    data class SuperStar(val duration: Second) : PowerUp()
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，你可以非常有效地将它们与科特林的`when`语句:
结合使用

```
fun Mario.collect(powerUp: PowerUp) { 
    // thanks to the 'return' a non-exhaustive 'when' will result in a compiler error
    return when(powerUp) {
        is FireFlower -> enableFlameThrowing()
        is SuperMushroom -> grow()
        // 'powerUp' is smart cast to 'SuperStar' so we can access duration on it
        is SuperStar -> startGodMode(powerUp.duration) 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里很酷的一点是，编译器可以推断出一个 *when* 表达式是否是穷举的，因为被求值的类型是一个密封类。
密封类只能在它们自己的`.kt`文件中扩展，因此编译器可以保证`FireFlower`、`SuperMushroom`和`SuperStar`是`powerUp`变量唯一可能采用的值。
如果我们在时使用[表达式](https://kotlinlang.org/docs/reference/control-flow.html#when-expression)语法而不是*语句语法，即。`val result = when(..) {}`或`return when(..) {}`，编译器会将此视为错误，不编译您的程序。*

当您向您的密封类添加更多对象，但忘记实现如何在 *when* 表达式中对它们做出反应时，这将非常有用。你的应用程序不会崩溃或者进入一个未定义的状态，而是会在编译时被通知错误。

另一个强大的功能，[智能施法](https://kotlinlang.org/docs/reference/typecasts.html#smart-casts)，可以在最后一个分支上看到。这里，分支右侧的`powerUp`被自动转换为分支左侧正在检查的类型。所以我们可以使用 SuperStar 的*持续时间*属性，而无需显式编写`(powerUp as SuperStar).duration`。

现在，正如科林·弗林 T2 解释的那样，这种 T4 代数数据结构对错误处理非常有用，不需要借助异常。

## 网络请求的错误处理

当我们发出网络请求时，有许多事情可能出错，许多网络库对 200 以外的任何 HTTP 响应代码都有异常反应。这很奇怪，因为在大多数应用程序的上下文中，代码 404(未找到资源)、401(未授权)或 403(禁止)实际上对我们的应用程序的业务逻辑有意义，应该相应地处理以增强用户体验，例如:

*   重定向至“登录 401”
*   为 403 上的某些订购服务提供高级升级
*   显示可爱的 404 页

传统上，我们必须捕捉网络调用导致的任何异常，并处理它们以实现这些情况。这可能会使我们的网络调用代码变得混乱，难以适应不断变化的需求。

假设您在程序的许多地方都有网络调用代码，并且必须添加一个新的 HTTP 代码来响应。例如，你的团队现在还想在 503 上显示一个可爱的*服务不可用的*页面。
T3![503 status page of the university of oregon. The title says 'Service is unavailable'. The text below it says 'We are working to clear the ducks and get the service available again. Click the 'Service Status' button for more information. Below it there is a picture of a lot of cartoon rubber ducks sitting on and around a server.](img/b94ab8e4615a4dbf2eb55d7218587a3d.png)T5】

这可能会成为一个有趣的错误来源，尤其是当不熟悉代码库的新开发人员进行更改时(甚至是 3 个月后的你)😉).

也有一些人对通过异常驱动控制流有自己的看法👀。

* * *

*语境转换。我们现在从马里奥切换到一个简单的黑客新闻阅读器*

使用代数数据类型，我们可以通过在一个密封的响应类中定义任何对我们的应用程序有意义的响应来避免这种情况:

```
sealed class NetworkResult

sealed class Payload : NetworkResult() {

    data class AggregateArticles(val articleIds: List<Long>) : Payload()

    data class SingleArticle(val article: HackerNewsItem): Payload()
}

sealed class HttpError : NetworkResult() {

    object ResourceNotFound : HttpError()

    object ServiceUnavailable : HttpError()

    data class UnknownError(val code: Int, val message: String) : HttpError()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像这样在网络调用站点使用 when 表达式:

```
 fun handleNetworkResult(result: NetworkResult) {
        return when(result) {
            is Payload -> handleResult(result)
            is HttpError -> handleError(result)
        }
    }

    fun handleResult(result: Payload) {
        return when(result) {
            is AggregateArticles -> loadArticleDetails(result.articleIds)
            is SingleArticle -> showArticle(result.article)
        }
    }

    // this would probably live in an injected global error handler
    fun handleError(result: HttpError) {
        return when(result) {
            is ResourceNotFound -> show404Screen()
            is ServiceUnavailable -> show503Screen()
            is UnknownError -> showError("Unknown error ${result.code}: ${result.message}")
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这很酷，因为正如我们上面所展示的，你现在不能忘记实现一个 case，因为你会在`when`表达式上得到一个编译器错误，在那里你丢失了一个分支。

太好了！但是已经有很多文章在讨论这些了。
我们实际上是如何从一个网络层响应对象得到我们的密封类成员的！？

## 如何与网络层集成

*下面的代码将是 Android 特有的，并假设 Dagger 2 设置为*

在 Android 开发中，我们经常使用 *OkHttp* + *改型*来定义我们的网络层，使用 *RxJava* 来处理网络调用的异步特性。
典型的改装初始化如下图所示

```
 @Provides
    @Singleton
    fun provideRetrofit(): Retrofit {
        val rxCallAdapter = RxJava2CallAdapterFactory.createWithScheduler(Schedulers.io())
        return Retrofit.Builder()
                .baseUrl("https://hacker-news.firebaseio.com/v0/")
                .addConverterFactory(GsonConverterFactory.create(gson))
                .addCallAdapterFactory(rxCallAdapter)
                .build()
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们添加了一个调用转换器，使我们能够将每个网络调用包装在 RxJava 可观察类型中，并添加了一个转换器来自动将网络响应的 JSON 解析为数据类型。
一个典型的 API 接口定义了改进，一个使用它的服务看起来像这样:

```
interface NewsAggregateApi {
    @GET("topstories.json")
    fun loadTopStories(): Single<List<Long>>

    @GET("newstories.json")
    fun loadNewStories(): Single<List<Long>>
}

class NewsAggregateService(private val aggregateApi: NewsAggregateApi) {

    fun loadTopStories(): Single<List<Long>> = aggregateApi.loadTopStories()

    fun loadNewStories(): Single<List<Long>> = aggregateApi.loadNewStories()
} 
```

Enter fullscreen mode Exit fullscreen mode

如果调用`/topstories.json`时一切顺利，我们的 Observable 将得到一个`articleId: Long`列表，但是如果我们遇到任何不在 200s 内的 HTTP 代码，Observables `onError`方法将被触发。这很糟糕，因为它发生在我们可以将任何东西包装在我们漂亮的密封类类型中之前。
在对[翻新问题#1218](https://github.com/square/retrofit/issues/1218) 的旧回答中，杰克·沃顿对我们的问题有一个解决方案:
[![Jake Wharton says "There are three ways to construct your observable: Observable<BodyType>, Observable<Response<BodyType>>, or Observable<Result<BodyType>>. For the first version, there's nowhere to hang non-200 response information so it is included in the exception passed to onError. For the latter two, the data is encapsulated in the Response object and can be accessed by calling errorBody()."](img/8498d07aaa5e2ce4cb0a351b56cd6c2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jA3o8t5r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1r63jq0lqnl140gyd8q2.png)

因此，我们现在可以相应地修改我们的 API 接口，如下所示:

```
interface NewsAggregateApi {
    @GET("topstories.json")
    fun loadTopStories(): Single<Response<List<Long>>>

    @GET("newstories.json")
    fun loadNewStories(): Single<Response<List<Long>>>
} 
```

Enter fullscreen mode Exit fullscreen mode

并对我们的服务和我们的密封类做一些更改，这样服务的返回类型总是一个`NetworkResult`，我们可以将我们的`Response`映射到一个`NetworkResult`子类型。

```
class NewsAggregateService(private val aggregateApi: NewsAggregateApi) {

    fun loadTopStories(): Single<NetworkResult> {
        return aggregateApi.loadTopStories()
                .map { it.toAggregateResult() }
    }

    fun loadNewStories(): Single<NetworkResult> {
        return aggregateApi.loadNewStories()
                .map { it.toAggregateResult() }
    }

    private fun Response<List<Long>>.toAggregateResult() =
            if (this.isSuccessful) {
                NetworkResult.fromAggregateResponse(this.body() ?: emptyList())
            } else {
                NetworkResult.fromErrorResponse(this.code(), this.message())
            }
}

sealed class NetworkResult {

    // unmodified code omitted

    companion object {
        fun fromAggregateResponse(payload: List<Long>): NetworkResult {
            return Payload.AggregateArticles(payload)
        }

        fun fromErrorResponse(code: Int, message: String?): NetworkResult {
            return when (code) {
                404 -> HttpError.ResourceNotFound
                503 -> HttpError.ServiceUnavailable
                else -> HttpError.UnknownError(code, message ?: "")
            }
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这就是全部了！现在，每个有效的网络响应都将包装在您的 ADT 类中。尽管如此，还是有一些警告。这将只处理成功解析的有效响应。JSON 反序列化期间的任何错误或网络堆栈异常(如超时和 SSL 协议异常)仍然会被抛出，并且必须在其他地方处理。

但是我们现在有了一个很好的(并且容易测试的！)处理对我们的业务逻辑有意义的非 200 服务器响应的方法。

感谢阅读:)

附:如果你想看更多的上下文代码样本，请查看这个报告。