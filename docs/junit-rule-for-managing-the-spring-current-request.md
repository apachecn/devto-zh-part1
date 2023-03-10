# 管理 Spring 当前请求的 JUnit 规则

> 原文：<https://dev.to/grahamcox82/junit-rule-for-managing-the-spring-current-request>

有时在您的代码中，您需要处理当前的请求。我经常做的一个很好的例子是生成 URL。您并不总是知道应用程序部署在哪里，它甚至可能在多个地址上可用。将部署 URL 配置到应用程序中并不容易，但是 Spring 有一些非常有用的实用工具。(


 and

```MvcUriComponentsBuilder```

). These are powered off of the current request, which is itself stored in a utility called

```RequestContextHolder```

.

When you've written some code to use this, you invariably want to then test it. And that's fine, but setting up the

```RequestContetxHolder```

 in a test isn't always as trivial as it should be. You end up using a

```MockHttpServletRequest```

 and needing to dig into how this needs to be set up correctly - which parts of the "Current URI" go into which fields. And because I find myself doing this over and over, I've put together a JUnit rule to do it for me. Here it is. Note, it's written in Kotlin because that's my preference, but it would be trivial to translate.

```kotlin
import org.junit.rules.TestRule
import org.junit.runner.Description
import org.junit.runners.model.Statement
import org.slf4j.LoggerFactory
import org.springframework.mock.web.MockHttpServletRequest
import org.springframework.web.context.request.RequestContextHolder
import org.springframework.web.context.request.ServletRequestAttributes
import org.springframework.web.util.UriComponentsBuilder

/**
 * JUnit Rule to set up the current request context
 * @property uri The URI to act as the current request
 */
class CurrentRequestRule(private val uri: String) : TestRule {
    companion object {
        /** The logger to use */
        private val LOG = LoggerFactory.getLogger(CurrentRequestRule::class.java)
    }

    /** The request that we are claiming is current */
    private val request: MockHttpServletRequest

    init {
        val uriComponents = UriComponentsBuilder.fromUriString(uri).build()
        request = MockHttpServletRequest("GET", uriComponents.path)
        request.scheme = uriComponents.scheme
        request.serverName = uriComponents.host
        request.serverPort = uriComponents.port
        request.queryString = uriComponents.query
    }
    /**
     * Ensure that the Spring [RequestContextHolder] contains a request for the "Current URI" for the duration of the test
     * @param base The [Statement] to be modified
     *
     * @param description A [Description] of the test implemented in `base`. Not used.
     *
     * @return a new statement which sets up the RequestContextHolder before the `base` statement is called, and clears
     * it aftereards
     */
    override fun apply(base: Statement, description: Description): Statement {
        return object : Statement() {
            override fun evaluate() {
                LOG.debug("Setting current URI to {}", uri)
                val requestAttributes = ServletRequestAttributes(request)
                try {
                    RequestContextHolder.setRequestAttributes(requestAttributes)
                    base.evaluate()
                } finally {
                    RequestContextHolder.resetRequestAttributes()
                    LOG.debug("Clearing current URI")
                }
            }

        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请随意使用它，在您的代码中使用它，做您想做的事情。你甚至不需要信任我——虽然如果你这样做，我不会反对；)

我很好奇现在人们对他们的项目还有什么有用的 JUnit 规则。:)