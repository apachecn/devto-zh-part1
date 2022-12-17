# 节点中的 HTTP 请求记录。射流研究…

> 原文：<https://dev.to/brightdevs/http-request-logging-in-nodejs-42od>

每个后端应用程序都应该拥有的一种最基本的日志记录是对所有传入 HTTP 请求的跟踪日志记录。然而，让它变得正确和有用并不容易。如今，我们在 Bright 创建的大多数后端都是 Node。基于 [Express](https://expressjs.com/) 的 JS 应用。尽管有大量的[库](https://www.npmjs.com/search?q=logging)为你处理日志，但是如果我们没有尝试自己构建一些东西(即使只是为了更好地了解内部)，我们就不是我们自己。让我向您展示我们学到了什么，以及我们做了什么来确保我们的日志是有意义和有用的。

## 记录请求和响应

对传入 HTTP 请求的处理可能包含许多我们希望后端执行的任务，包括数据库查询、第三方服务调用和各种数据处理。根据节点的性质。JS，Express 异步处理。通过将 [`ClientRequest`](https://nodejs.org/api/http.html#http_class_http_clientrequest) 与 [`ServerResponse`](https://nodejs.org/api/http.html#http_class_http_serverresponse) 对象分离，正在处理的 HTTP 请求的传入数据和传出结果也在节点的`http`模块代码中解耦。

这就是我们应该始终分别关注处理管道的开始和结束并记录它们的原因。在 Express 中，很容易使用[中间件](https://expressjs.com/en/guide/using-middleware.html)来隐藏到处理的开始，并在那里执行我们的日志代码。让我们从这个简单的方法开始，用打字稿写成:

```
const logRequestStart = (req: Request, res: Response, next: NextFunction) => {
    console.info(`${req.method}  ${req.originalUrl}`)
    next()
}

app.use(logRequestStart) 
```

Enter fullscreen mode Exit fullscreen mode

它肯定缺少了许多有用的通用身份识别材料，但这是一个开始。我们正在记录请求的方法(GET、POST 等)。)和它的[原始 URL](http://expressjs.com/en/api.html#req.originalUrl)——注意`req.url`可能不一定保持相同的值，因为它可能被我们基于 Express 的路由器操纵。

[![Node.JS logging](img/3ad2964c00381afcd621f1f9094c1cb9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QUHR0d6F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/29ekstjcbdw322s2mv36.jpeg)

记录处理管道的另一端怎么样？我们没有通用的方法将中间件附加到处理的末尾，并且在每个路由定义的末尾手动附加`logRequestEnd`中间件将是非常重复和麻烦的。但幸运的是，`ServerResponse`是一个[`EventEmitter`](https://nodejs.org/api/events.html#events_class_eventemitter)——它在完成时发出事件，我们可以在那里用我们的代码订阅。这是我们更新后的代码(又是打字稿):

```
const logRequestStart = (req: Request, res: Response, next: NextFunction) => {
    console.info(`${req.method}  ${req.originalUrl}`) 

    res.on('finish', () => {
        console.info(`${res.statusCode}  ${res.statusMessage}; ${res.get('Content-Length') || 0}b sent`)
    })

    next()
}

app.use(logRequestStart) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们现在将一个函数作为订阅者附加到由 HTTP 响应对象发出的`finish`事件。它记录状态代码和信息(`200 OK`、`404 Not Found`等)。)和响应体的长度。虽然最初记录整个响应数据可能很有吸引力，但如果我们将隐私数据包含在纯文本日志的响应中，通常会因为太长而没有用，并且可能不受我们了解隐私的用户的欢迎。如果我们只对请求的一般结果感兴趣，我们应该通过 HTTP 状态码更好地传达它[。](http://racksburg.com/choosing-an-http-status-code/)

## 请求完成时？

上面的代码(至少)有一个问题。并不是所有的响应都能真正完成——当客户端中止请求或者抛出内部未处理的错误时，`ServerResponse`相应地发出`close`和`error`事件，我们也应该订阅它们。然而，这里的问题是，我们不能期望在这些情况下正确设置`res.statusCode`。考虑到处理由于某种原因突然中断的事实，这本身是相当明显的。然而，令人惊讶的是，当我们实际读取它时，例如假设`statusCode`将是未定义的或虚假的，我们反而得到了`200`(成功状态代码)。这在过去欺骗了我们，因为如果我们照原样记录它，而在之后阅读日志时，我们可能会忽略这个事实，即请求肯定不是那么成功。我认为这是一个相当奇怪的设计决策，Node 的`http`模块创建者最初将`statusCode`设置为 200，并在响应不成功的情况下将其覆盖——如果结果还不知道，它不应该错误地指示它是成功的。

正确处理这些情况的类型脚本代码可能如下所示:

```
const getLoggerForStatusCode = (statusCode: number) => {
    if (statusCode >= 500) {
        return console.error.bind(console)
    }
    if (statusCode >= 400) {
        return console.warn.bind(console)
    }

    return console.log.bind(console)
}

const logRequestStart = (req: ApiRequest, res: Response, next: NextFunction) => {
    console.info(`${req.method}  ${req.originalUrl}`) 

    const cleanup = () => {
        res.removeListener('finish', logFn)
        res.removeListener('close', abortFn)
        res.removeListener('error', errorFn)
    }

    const logFn = () => {
        cleanup()
        const logger = getLoggerForStatusCode(res.statusCode)
        logger(`${res.statusCode}  ${res.statusMessage}; ${res.get('Content-Length') || 0}b sent`)
    }

    const abortFn = () => {
        cleanup()
        console.warn('Request aborted by the client')
    }

    const errorFn = err => {
        cleanup()
        console.error(`Request pipeline error: ${err}`)
    }

    res.on('finish', logFn) // successful pipeline (regardless of its response)
    res.on('close', abortFn) // aborted pipeline
    res.on('error', errorFn) // pipeline internal error

    next()
}

app.use(logRequestStart) 
```

Enter fullscreen mode Exit fullscreen mode

这里还有两件事需要注意。

首先，由于成功的(2xx)和不成功的(4xx 和已处理的 5xx)都经过“普通的”`finish`事件路径，我添加了代码来确定要使用的最正确的控制台记录器级别。这对纯文本日志输出有一点影响，但是在查看运行 it dev 的应用程序的终端时，警告和错误被涂成红色，因此它们更容易引起注意。

第二个是`cleanup`函数，它确保不存在挂起的侦听器，并且不管采用哪条代码路径，所有的侦听器都会被清除，没有什么会阻止正确的垃圾收集。

## 将你的请求与回应联系起来

到目前为止，我们在记录管道两端的日志方面做得很好，但是如果我们的请求开始日志与响应结束日志完全不相关，那么它就没有任何用处。当我们同时处理许多请求时，我们没有办法计算出请求处理需要多长时间，或者不成功响应所请求的 URL 是什么。让我们通过为正在处理的请求生成一个临时标识符来修复它，并确保它包含在我们创建的所有日志条目中。

我们通过运行一个中间件来做到这一点，该中间件将我们的自定义属性添加到请求对象中，并将其值设置为由 [`gen-uid`](https://www.npmjs.com/package/gen-uid) 库生成的令牌，尽管我们生成值的方式一点也不重要，并且您可以使用任何其他方法来提供一个足够唯一的值，以便将单个特定请求与其他请求区分开来。

```
import {token} from 'gen-uid'

req.requestId = token(true).substr(0, 8) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们只需在每次记录内容时添加这个值。最简单的情况可能是这样的:

```
console.info(`[${req.requestId}] ${req.method}  ${req.originalUrl}`) 
```

Enter fullscreen mode Exit fullscreen mode

两个重叠请求的示例输出现在看起来像这样:

```
[4e33fab09] GET /users?phrase=Adam
[8cc01bd69] GET /users?phrase=Jane
[4e33fab09] 200 OK; 235b sent
[8cc01bd69] 204 No Content; 0b sent 
```

Enter fullscreen mode Exit fullscreen mode

## 收集所有元数据

这开始变得有用，但是我们仍然错过了很多。我们不知道请求是何时发生的，也不知道请求者是谁。但是我们应该能够从请求头中读取其中的大部分。我们收集的内容包括:客户端版本、运行平台、用户设备标识和区域信息，以及一些特定于应用程序的用户标识(ID、状态、角色等)。)，我们根据请求中提供的(或未提供的)身份验证令牌来加载它。下面是在请求对象中设置元数据的中间件代码。

```
interface RequestApiClient {
    version: string
    platformVersion: string
    device: string
    locale: string
}

interface ApiRequest extends Request {
    requestId: string
    apiClient: RequestApiClient
    user: ApiUser
}

const enhanceRequestWithMetadata = (req: ApiRequest, res: Response, next: NextFunction) => {
    req.requestId = token(true).substr(0, 8)

    req.apiClient = {
        version: req.get('X-ClientVersion'),
        platformVersion: req.get('X-ClientPlatformVersion'),
        device: req.get('X-ClientDevice'),
        locale: req.get('X-ClientLocale')
    }

    req.user = loadUser(req) // app-specific implementation

    next()
} 
```

Enter fullscreen mode Exit fullscreen mode

为了在记录这些事情时消除重复，让我们为控制台 API 引入一个包装器，该包装器总是以相同的方式自动为所有消息添加所有相关的元数据，以确保我们的日志既可供人类阅读，也可供机器处理。

```
export interface Logger {
    info(...args)
    log(...args)
    warn(...args)
    error(...args)
}

class LoggerImpl {
    private _req: ApiRequest
    private _requestImprint: string = ''

    constructor(req?: ApiRequest) {
        if (req) {
            this._req = req

            let imprintParts = [req.requestId]

            if (req.apiClient) {
                imprintParts.push(`${req.apiClient.device || ''}  ${req.apiClient.platformVersion || ''}`)

                if (req.apiClient.version) {
                    imprintParts.push(`#${req.apiClient.version}`)
                }
                if (req.apiClient.locale) {
                    imprintParts.push(`${req.apiClient.locale} locale`)
                }
            }

            this._requestImprint = imprintParts.filter(x => !!x).join(', ')
        }
    }

    private get _userImprint() {
        return this._req && this._req.user ? ` ${(this._req.user.role || '').toLowerCase()}  ${this._req.user.id}` : ''
    }

    private _log(level, ...args) {
        return console[level](new Date().toISOString(), level, `[${this._requestImprint},${this._userImprint}]`, ...args)
    }

    info(...args) {
        return this._log('info', ...args)
    }

    log(...args) {
        return this._log('log', ...args)
    }

    warn(...args) {
        return this._log('warn', ...args)
    }

    error(...args) {
        return this._log('error', ...args)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们不再直接使用控制台 API，而是实例化一个`Logger`实例，将它猴子式地修补到请求对象中，并在整个处理管道中使用它(对于管道内部发生的任何常规日志消息，我们在这里根本不涉及)。这样，我们的日志看起来像这样:

```
2017-11-05T16:10:49.570Z info [350a7a0e, iPhone8,1 ios11.0.3, #622, de-de locale, no auth] GET /api/v1/user
2017-11-05T16:10:49.571Z info [6e79bd36, SM-G920F android7.0, #331, en-US locale, user 208ee38f-c636-4180-8dd4-dde48a04a4d1] GET /api/v1/entries
2017-11-05T16:10:49.704Z log [350a7a0e, iPhone8,1 ios11.0.3, #622, de-de locale, no auth] 401 Unauthorized; 0b sent
2017-11-05T16:10:50.351Z log [6e79bd36, SM-G920F android7.0, #331, en-US locale, user 208ee38f-c636-4180-8dd4-dde48a04a4d1] 200 OK; 4732b sent 
```

Enter fullscreen mode Exit fullscreen mode

够好了！

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

由亚当吧、网络佬@光明发明
[个人博客](https://whatwebcando.today/) [推特](https://twitter.com/NOtherDev) [邮箱](//adam.bar@brightinventions.pl) [Github](https://github.com/NOtherDev)