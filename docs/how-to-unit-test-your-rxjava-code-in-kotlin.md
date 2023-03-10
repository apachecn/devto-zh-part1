# 如何用 Kotlin 对 RxJava 代码进行单元测试

> 原文：<https://dev.to/dbottillo/how-to-unit-test-your-rxjava-code-in-kotlin>

在典型的 MVP 架构中，有不同的层:获取数据的存储库/网络客户机，附加了表示器的视图，表示器将调用交互器将请求代理到存储库/网络层。如果您使用 RxJava，您可能会从存储库层返回一个可观察对象，在表示层订阅它，交互器将决定哪个线程将执行请求。

这很常见，我也认为最好有两个不同的模型:一个用于网络响应，一个用于 UI。两个独立模型的好处通常是防止整个代码库发生变化:如果服务器改变了响应，您只需将新的模型映射到 UI 模型，整个视图/表示层根本不需要改变。两层之间有不同的名称字段也很好，这样您就不需要根据 API 定义来选择名称。例如，我认为 image 这个名字比 background _ path 这个名字更好，我很快就会给你们看这个例子。

这篇文章的主要目的是向你展示如何编写一个交互器，这个交互器在一个线程上从一个客户端获取数据，并在主线程上返回一个可观察值。
让我们假设有一个获取电影列表的网络客户端，来自网络的响应是:

```
class MoviesResponse(val page: Int, 
                     val total_results: Int, 
                     val total_pages: Int, 
                     val results: List<MovieResponse>)

class MovieResponse(val id: String, 
                    val title: String, 
                    val backdrop_path: String, 
                    val overview: String, 
                    val release_date: String) 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，一个电影响应包含结果数、页数、当前页面和电影列表；每部电影都包含 id、标题、背景路径、概述和上映日期。

在 UI 方面，我们只是对在列表中显示电影列表感兴趣，所以我们期待标题、描述和图像:

```
data class Movie(val title: String, val text: String, val _image: String){
    val image: String
        get() = "http://www.base.url/$_image"
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，让我们编写一个调用网络客户端的交互器，检索电影响应并返回一个带有电影对象列表的可观察对象。

```
class MoviesInteractor(val client: MoviesClient) {

    fun requestTopMovies(): Observable<List<Movie>> {
        return client.fetchMovies()
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .map {
                    it.results.map {
                        Movie(it.title, it.overview, it.backdrop_path)
                    }
                }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

MoviesInteractor 需要一个 MoviesClient，它有一个函数:

```
fun fetchMovies(): Observable<MoviesResponse> 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用的是改型，它支持开箱即用(添加插件)，所以我现在不会把重点放在这上面。假设 client.fetchMovies()返回一个可观察的 MoviesResponse，那么交互器可以将这个响应映射到电影列表，代码为:

```
.map {
    it.results.map {
        Movie(it.title, it.overview, it.backdrop_path)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 Kotlin 很容易实现这一点，因为 RxJava 的 map 操作符返回 MovieResponse 的实例，该实例具有包含 movie response 列表的属性“results”。因此，对于每个 MovieResponse，我们需要创建一个对应的 Movie 对象，幸运的是，Kotlin 有一个内置的函数 map，让您可以将对象 MovieResponse 映射到一系列电影，因此 map 函数中的“it”实例指的是 MovieResponse。

交互器的其余部分定义在主线程上观察，并在另一个线程上订阅，以避免阻塞主线程。
让我们看看如何测试这个:

```
 @Mock
    lateinit var client: MoviesClient

    internal lateinit var underTest: MoviesInteractor

    @Test
    fun `when movies are requested, should call client and return response`() {
        val movieResponse = MoviesResponse(0, 0, 0, listOf(
                MovieResponse("id1", "title1", "/backdrop1", "Overview1", "release date1"),
                MovieResponse("id2", "title2", "/backdrop2", "Overview2", "release date2")))
        Mockito.`when`(client.fetchMovies()).thenReturn(Observable.just(movieResponse))

        val result = underTest.requestTopMovies()

        val testObserver = TestObserver<List<Movie>>()
        result.subscribe(testObserver)
        testObserver.assertComplete()
        testObserver.assertNoErrors()
        testObserver.assertValueCount(1)
        val listResult = testObserver.values()[0]
        assertThat(listResult.size, `is`(2))
        assertThat(listResult[0].title, `is`("title1"))
        assertThat(listResult[0].image, `is`("http://www.base.url/backdrop1"))
        assertThat(listResult[0].text, `is`("Overview1"))
        assertThat(listResult[1].title, `is`("title2"))
        assertThat(listResult[1].image, `is`("http://www.base.url/backdrop2"))
        assertThat(listResult[1].text, `is`("Overview2"))
    } 
```

Enter fullscreen mode Exit fullscreen mode

在测试中，我们模拟客户端的响应，当客户端被调用时，我们返回一个包含两部电影的电影响应的假可观察对象。为了测试可观察性，我们可以使用 TestObserver 类，它允许您测试订阅的内容:我们可以检查它是否完整，是否发生错误，以及值是否是电影列表。

如果您尝试运行该测试，您可能会遇到以下错误:

```
Caused by: java.lang.RuntimeException: Method getMainLooper in android.os.Looper not mocked. See http://g.co/androidstudio/not-mocked for details. 
```

Enter fullscreen mode Exit fullscreen mode

这是一个相当隐晦的错误消息，但背后的原因是您的交互器试图在不同于主线程的线程中执行客户端调用。在测试执行期间，我们实际上对这种行为不感兴趣，我们只想确保客户端被调用并映射回我们的 UI 模型。
要修复错误信息，您可以在您的设置方法中添加:

```
RxJavaPlugins.setIoSchedulerHandler { Schedulers.trampoline() }
RxJavaPlugins.setComputationSchedulerHandler { Schedulers.trampoline() }
RxJavaPlugins.setNewThreadSchedulerHandler { Schedulers.trampoline() }
RxAndroidPlugins.setInitMainThreadSchedulerHandler { Schedulers.trampoline() } 
```

Enter fullscreen mode Exit fullscreen mode

这四行代码让你指定哪个线程用于 Io 调度程序、计算调度程序、新线程调度程序和 android 的初始主线程调度程序；基本上你是在强迫所有的人使用同一个线程！但是什么是蹦床呢？来自文档:

```
/**
 * Creates and returns a {@link Scheduler} that queues work on the current thread to be executed after the
 * current work completes.
 *
 * @return a {@link Scheduler} that queues work on the current thread
 */ 
```

Enter fullscreen mode Exit fullscreen mode

这正是我们感兴趣的！我们希望工作同步完成，因为检查我们是否使用了不同的线程不是这个单元测试的工作。我还认为你不需要测试不同的线程行为，因为你正在使用一个库(RxJava ),从我的角度来看，测试一个库是没有意义的。

这看起来很棒，但是您需要将这 4 行代码放在每个文件的每个设置方法中，这看起来很有竞争力。我建议创建一个规则，可以添加到需要这种行为的每个测试中:

```
class RxImmediateSchedulerRule : TestRule {

    override fun apply(base: Statement, d: Description): Statement {
        return object : Statement() {
            @Throws(Throwable::class)
            override fun evaluate() {
                RxJavaPlugins.setIoSchedulerHandler { Schedulers.trampoline() }
                RxJavaPlugins.setComputationSchedulerHandler { Schedulers.trampoline() }
                RxJavaPlugins.setNewThreadSchedulerHandler { Schedulers.trampoline() }
                RxAndroidPlugins.setInitMainThreadSchedulerHandler { Schedulers.trampoline() }

                try {
                    base.evaluate()
                } finally {
                    RxJavaPlugins.reset()
                    RxAndroidPlugins.reset()
                }
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以添加一条规则来代替设置中的四行代码:

```
@Rule @JvmField var testSchedulerRule = RxImmediateSchedulerRule() 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！交互器的完整实现及其测试:

```
class MoviesInteractorTest {

    @Rule @JvmField
    val rule = MockitoJUnit.rule()!!

    @Rule @JvmField var testSchedulerRule = RxImmediateSchedulerRule()

    @Mock
    lateinit var client: MoviesClient

    internal lateinit var underTest: MoviesInteractor

    @Before
    fun setUp() {
        underTest = MoviesInteractor(client)
    }

    @Test
    fun `when top movies are requested, should call client and return response`() {
        val movieResponse = MoviesResponse(0, 0, 0, listOf(
                MovieResponse("id1", "title1", "/backdrop1", "Overview1", "release date1"),
                MovieResponse("id2", "title2", "/backdrop2", "Overview2", "release date2")))
        Mockito.`when`(client.fetchMovies()).thenReturn(Observable.just(movieResponse))

        val result = underTest.requestTopMovies()

        val testObserver = TestObserver<List<Movie>>()
        result.subscribe(testObserver)
        testObserver.assertComplete()
        testObserver.assertNoErrors()
        testObserver.assertValueCount(1)
        val listResult = testObserver.values()[0]
        assertThat(listResult.size, `is`(2))
        assertThat(listResult[0].title, `is`("title1"))
        assertThat(listResult[0].image, `is`("https://image.tmdb.org/t/p/w342/backdrop1"))
        assertThat(listResult[0].text, `is`("Overview1"))
        assertThat(listResult[1].title, `is`("title2"))
        assertThat(listResult[1].image, `is`("https://image.tmdb.org/t/p/w342/backdrop2"))
        assertThat(listResult[1].text, `is`("Overview2"))
    }
}

class RxImmediateSchedulerRule : TestRule {

    override fun apply(base: Statement, d: Description): Statement {
        return object : Statement() {
            @Throws(Throwable::class)
            override fun evaluate() {
                RxJavaPlugins.setIoSchedulerHandler { Schedulers.trampoline() }
                RxJavaPlugins.setComputationSchedulerHandler { Schedulers.trampoline() }
                RxJavaPlugins.setNewThreadSchedulerHandler { Schedulers.trampoline() }
                RxAndroidPlugins.setInitMainThreadSchedulerHandler { Schedulers.trampoline() }

                try {
                    base.evaluate()
                } finally {
                    RxJavaPlugins.reset()
                    RxAndroidPlugins.reset()
                }
            }
        }
    }
}

class MoviesInteractorImpl(val client: MoviesClient) {

    override fun requestTopMovies(): Observable<List<Movie>> {
        return client.fetchMovies()
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .map {
                    it.results.map {
                        Movie(it.title, it.overview, it.backdrop_path)
                    }
                }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode