# 用 Swagger 记录的 Scala REST API(第 2 部分，共 2 部分)

> 原文：<https://dev.to/svinci/scala-rest-api-documented-with-swagger-part-2-of-2>

这是一篇文章的续篇，你可以在这里找到。

## 我们离开的地方...

因此，我们有一个 API，它有一个状态端点和适当的代码来用 swagger 记录它。

启动服务，我们将看到我们的文档在`http://localhost:8080/docs`可用，并且我们在`http://localhost:8080/professionals-api/status`有一个状态端点。

所以，我们继续吧！

## 专业人员 API

让我们在名为`com.svinci.professionals.api.domain.professional`的包中创建我们的模型:

*   首先，专业人士有他们擅长的工作，对吗？并且它们可能不止一个，所以我们需要一个工作的 case 类:

```
case class Job(name: String, description: String) 
```

Enter fullscreen mode Exit fullscreen mode

*   现在，联系信息将根据客户端是否经过身份验证来显示，因此我们需要一个联系信息的 case 类:

```
case class ContactInformation(phone: String, eMail: String, address: String) 
```

Enter fullscreen mode Exit fullscreen mode

*   然后，我们只需要一个专业人士。它将有一个 id，一个名字，一个年龄，一个工作列表，以及可选的联系信息:

```
case class Professional(id: String, name: String, age: Int, jobs: List[Job], contactInformation: Option[ContactInformation]) 
```

Enter fullscreen mode Exit fullscreen mode

*   此外，当客户创建一个专业人员时，id 应该由 API 生成，联系信息应该是强制的，因此我们需要一个创建请求，如果它知道如何将自己转换为一个完整的专业人员就好了:

```
case class ProfessionalCreationRequest(name: String, age: Int, jobs: List[Job], contactInformation: ContactInformation) {

  def toProfessional: Professional = Professional(
    id = UUID.randomUUID().toString,
    name = name,
    age = age,
    jobs = jobs,
    contactInformation = Option(contactInformation)
  )

} 
```

Enter fullscreen mode Exit fullscreen mode

应该可以了，让我们继续到仓库。用以下内容创建一个名为`ProfessionalRepository.scala`的文件，代码用注释解释:

```
package com.svinci.professionals.api.domain.professional

import scala.collection.concurrent.TrieMap

/**
  * We are using cake pattern to solve dependency injection without using any library. You can find a really good explanation of this pattern at http://www.cakesolutions.net/teamblogs/2011/12/19/cake-pattern-in-depth.
  *
  * This component holds the interface for professionals repository.
  */
trait ProfessionalRepositoryComponent {

  /**
    * Professional repository instance definition.
    */
  def professionalRepositoryInstance: ProfessionalRepository

  /**
    * Professional repository interface.
    */
  trait ProfessionalRepository {

    /**
      * Receives a creation request, transforms it to a Professional and stores it. Returns the created professional.
      */
    def create(professionalCreationRequest: ProfessionalCreationRequest): Professional

    /**
      * Removes the professional that has the given id. Returns the removed professional if any.
      */
    def remove(professionalId: String): Option[Professional]

    /**
      * Returns the professional that has the given id if any.
      */
    def findById(professionalId: String, displayContactInformation: Boolean): Option[Professional]

    /**
      * Returns a list with all the stored professionals.
      */
    def all(displayContactInformation: Boolean): List[Professional]

  }

}

/**
  * This component defines an implementation of ProfessionalRepository which holds the data in memory.
  */
trait InMemoryProfessionalRepositoryComponent extends ProfessionalRepositoryComponent {

  /**
    * In memory professional repository instantiation.
    */
  override def professionalRepositoryInstance: ProfessionalRepository = new InMemoryProfessionalRepository

  /**
    * In memory implementation of a professional repository.
    */
  class InMemoryProfessionalRepository extends ProfessionalRepository {

    /**
      * This is the data structure chosen to store the professionals.
      * In this Map you'll find professionals indexed by their ids.
      * TrieMap was chosen to make this class thread safe.
      */
    private[this] val storage: TrieMap[String, Professional] = TrieMap()

    /**
      * @inheritdoc
      */
    override def create(professionalCreationRequest: ProfessionalCreationRequest): Professional = {
      val professional = professionalCreationRequest.toProfessional
      storage.put(professional.id, professional)
      professional
    }

    /**
      * @inheritdoc
      */
    override def remove(professionalId: String): Option[Professional] = storage.remove(professionalId)

    /**
      * @inheritdoc
      */
    override def findById(professionalId: String, displayContactInformation: Boolean): Option[Professional] = {
      val maybeProfessional = storage.get(professionalId)

      if (displayContactInformation) {
        return maybeProfessional
      }

      maybeProfessional.map(professional => professional.copy(contactInformation = None))
    }

    /**
      * @inheritdoc
      */
    override def all(displayContactInformation: Boolean): List[Professional] = {
      val allProfessionals = storage.values.toList

      if (displayContactInformation) {
        return allProfessionals
      }

      allProfessionals.map(professional => professional.copy(contactInformation = None))
    }

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，读取操作接收到一个名为`displayContactInformation`的标志，如果它是真的，它们就返回它们所拥有的，但是如果它是假的，它们就复制专业人员，忽略联系信息。

我通常不会把逻辑放在存储库中，我会有一个服务层或其他东西，但由于这是一个非常小的 API，我更喜欢保持简单。

现在我们只需要我们的 servlet，我们需要一个名为`ProfessionalServlet.scala`的文件，其内容如下:

```
package com.svinci.professionals.api.domain.professional

import com.svinci.professionals.api.infrastructure.ServletSupport

/**
  * We are using cake pattern to solve dependency injection without using any library. You can find a really good explanation of this pattern at http://www.cakesolutions.net/teamblogs/2011/12/19/cake-pattern-in-depth.
  *
  * As this is an entry point to our application, there is no need to create an interface (it's a servlet after all, so there are no functions exposed).
  */
trait ProfessionalServletComponent {

  /**
    * As defined by cake pattern, with self type annotations we are defining that any class that extends this trait, needs to extend ProfessionalRepositoryComponent too.
    * This makes the interface and instance defined by ProfessionalRepositoryComponent available in this trait.
    */
  this: ProfessionalRepositoryComponent =>

  /**
    * Professional servlet instantiation.
    */
  def professionalServletInstance: ProfessionalServlet = new ProfessionalServlet(professionalRepositoryInstance)

  /**
    * This is the scalatra servlet that will serve our professionals management endpoints.
    */
  class ProfessionalServlet(val repository: ProfessionalRepository) extends ServletSupport {

    /**
      * This value defines the documentation for this endpoint. We are giving the endpoint a name, the return type, a description/summary and the schema for the expected boy.
      */
    private[this] val createProfessional = apiOperation[Professional]("create") summary "Insert a professional in the system" parameter bodyParam[ProfessionalCreationRequest]
    /**
      * We are routing our creation endpoint to the root of this servlet, passing the api operation for swagger to document.
      */
    post("/", operation(createProfessional)) {
      val creationRequest = parsedBody.extract[ProfessionalCreationRequest]
      repository.create(creationRequest)
    }

    /**
      * This value defines the documentation for this endpoint. We are giving the endpoint a name, the return type, a description/summary and the specifications for the id path param.
      */
    private[this] val removeProfessional = apiOperation[Option[Professional]]("remove") summary "Remove a professional by its id." parameter pathParam[String]("Id of the professional to remove")
    /**
      * We are routing our removal endpoint to the root of this servlet with the id as a path param, passing the api operation for swagger to document.
      */
    delete("/:id", operation(removeProfessional)) {
      val professionalId = params('id)
      repository.remove(professionalId)
    }

    /**
      * This value defines the documentation for this endpoint. We are giving the endpoint a name, the return type, a description/summary and the specifications for the id path parameter.
      */
    private[this] val findById = apiOperation[Option[Professional]]("findById") summary "Find a professional by its id." parameter pathParam[String]("id of the professional you are looking for.")
    /**
      * We are routing our findById endpoint to the root of this servlet with the id as a path param, passing the api operation for swagger to document.
      */
    get("/:id", operation(findById)) {
      val professionalId = params('id)
      val authenticated = request.getAttribute("authenticated").asInstanceOf[Boolean]
      repository.findById(professionalId, authenticated)
    }

    /**
      * This value defines the documentation for this endpoint. We are giving the endpoint a name, the return type and a description/summary.
      */
    private[this] val all = apiOperation[List[Professional]]("allProfessionals") summary "Retrieve all professionals."
    /**
      * We are routing our all endpoint to the root of this servlet, passing the api operation for swagger to document.
      */
    get("/", operation(all)) {
      val authenticated = request.getAttribute("authenticated").asInstanceOf[Boolean]
      repository.all(authenticated)
    }

    /**
      * This is the description of this servlet, requested by swagger.
      */
    override protected def applicationDescription: String = "Professionals management API."

  }

}

/**
  * This is the default instance of ProfessionalServletComponent. Here we define that the ProfessionalServletComponent will use the InMemoryProfessionalRepositoryComponent.
  */
object DefaultProfessionalServletComponent extends ProfessionalServletComponent with InMemoryProfessionalRepositoryComponent 
```

Enter fullscreen mode Exit fullscreen mode

关于这个 servlet 需要注意的一点是，我们从请求中检索一个属性，以检查客户机是否经过身份验证:`request.getAttribute("authenticated").asInstanceOf[Boolean]`。此属性应由过滤器或 before 处理程序填充。让我们在`before`执行中将它添加到`ServletSupport`特征中。

```
 /**
    * From the servlet request, check if the client is authenticated.
    *
    * This function can be exposed as protected as is, and let the servlets that extend ServletSupport use it,
    * but that wouldn't be convenient in a real situation, as checking if an authentication token is actually valid
    * would have performance implications (query to a database, REST API call, etc.).
    */
  private[this] def isAuthenticated()(implicit request: HttpServletRequest): Boolean = {
    val authenticationToken: Option[String] = Option(request.getHeader("X-PROFESSIONALS-API-TOKEN"))

    // Here you may perform token validation calling an authentication service or something.
    // We'll keep it simple in this example, and we'll assume the client is authenticated if the header is present.
    authenticationToken.isDefined
  }

  /**
   * Before every request made to a servlet that extends this trait, the function passed to `before()` will be executed.
   * We are using this to :
   *   - Set the Content-Type header for every request, as we are always going to return JSON.
   *   - Set the date to the request, so we can calculate spent time afterwards.
   *   - Generate a transaction identifier, an add it to the MDC, so we know which lines of logs were triggered by which request.
   *   - Check if the client is authenticated and add the flag to the request attributes.
   *   - Log that a request arrived.
   */
  before() {

    contentType = "application/json"
    request.setAttribute("startTime", new Date().getTime)
    MDC.put("tid", UUID.randomUUID().toString.substring(0, 8))

    val authenticated = isAuthenticated()
    request.setAttribute("authenticated", authenticated)

    logger.info(s"Received request ${request.getMethod} at ${request.getRequestURI}")

  } 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们创建函数`isAuthenticated`并从我们传递给`before()`的函数中调用它，这个函数已经在之前的文章中写好了。

现在，我们需要将专业 servlet 添加到我们的`Module`对象中。

```
 /**
    * Default instance of ProfessionalServlet
    */
  def professionalServlet: DefaultProfessionalServletComponent.ProfessionalServlet = DefaultProfessionalServletComponent.professionalServletInstance 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在我们的`ScalatraBootstrap`类中安装它。

```
 context.mount(Module.professionalServlet, "/professionals-api/professionals", "professionals") 
```

Enter fullscreen mode Exit fullscreen mode

是时候测试一下了，我们在项目根目录下启动运行`sbt`的 sbt 控制台，然后运行`jetty:start`。

让我们创造一个专业:

```
$ curl -v localhost:8080/professionals-api/professionals -X POST -H "Content-Type: application/json" -d '{"name": "Mario", "age": 45, "jobs": [{"name": "Plumber", "description": "Fixing your pipes broh!"}], "contactInformation": {"phone": "555-1234", "eMail": "mario@gmail.com", "address": "Check your pipes ;)"}}'

{"id":"f967b053-68c1-41af-a8b9-27135516c0fc","name":"Mario","age":45,"jobs":[{"name":"Plumber","description":"Fixing your pipes broh!"}],"contactInformation":{"phone":"555-1234","eMail":"mario@gmail.com","address":"Check your pipes ;)"}} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，API 收到了请求，并返回了创建的 Mario。现在让我们检索所有的专业人员，检查 API 是否确实在存储东西。

```
$ curl localhost:8080/professionals-api/professionals

[{"id":"f967b053-68c1-41af-a8b9-27135516c0fc","name":"Mario","age":45,"jobs":[{"name":"Plumber","description":"Fixing your pipes broh!"}]}] 
```

Enter fullscreen mode Exit fullscreen mode

在那里！你可以看到马里奥在那里，但它没有联系信息，我们要检查认证工作吗？

```
$ curl localhost:8080/professionals-api/professionals -H "X-PROFESSIONALS-API-TOKEN: 1234"

[{"id":"f967b053-68c1-41af-a8b9-27135516c0fc","name":"Mario","age":45,"jobs":[{"name":"Plumber","description":"Fixing your pipes broh!"}],"contactInformation":{"phone":"555-1234","eMail":"mario@gmail.com","address":"Check your pipes ;)"}}] 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我们现在可以看到他的邮件和电话。让我们通过他的 id `f967b053-68c1-41af-a8b9-27135516c0fc`找回马里奥。

```
$ curl localhost:8080/professionals-api/professionals/f967b053-68c1-41af-a8b9-27135516c0fc -H "X-PROFESSIONALS-API-TOKEN: 1234"

{"id":"f967b053-68c1-41af-a8b9-27135516c0fc","name":"Mario","age":45,"jobs":[{"name":"Plumber","description":"Fixing your pipes broh!"}],"contactInformation":{"phone":"555-1234","eMail":"mario@gmail.com","address":"Check your pipes ;)"}} 
```

Enter fullscreen mode Exit fullscreen mode

这很好，现在让我们删除 Mario，然后继续检查文档。

```
$ curl localhost:8080/professionals-api/professionals/f967b053-68c1-41af-a8b9-27135516c0fc -H "X-PROFESSIONALS-API-TOKEN: 1234" -X DELETE

{"id":"f967b053-68c1-41af-a8b9-27135516c0fc","name":"Mario","age":45,"jobs":[{"name":"Plumber","description":"Fixing your pipes broh!"}],"contactInformation":{"phone":"555-1234","eMail":"mario@gmail.com","address":"Check your pipes ;)"}}

$ curl localhost:8080/professionals-api/professionals -H "X-PROFESSIONALS-API-TOKEN: 1234"
[] 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们删除了它，然后 API 不再检索它。现在，是时候看看文档是如何工作的了，但是我不想继续阅读 JSON，我的意思是，JSON 是一种很好的格式，并且很容易阅读...但是用户界面不是很好吗？做起来应该没那么难...

## 霸气 UI

事实并非如此，他们真的做到了。顺便说一句，你需要 docker 继续关注这个例子。安装说明可在[这里](https://www.docker.com/)找到。

安装 docker 后，执行以下命令:

```
docker run -p 8081:8080 --name swagger-ui-instance -d swaggerapi/swagger-ui:v2.2.9 
```

Enter fullscreen mode Exit fullscreen mode

这将引导一个 docker 容器，该容器使用 NGINX 为 swagger ui 提供服务。反正也无所谓。你只需在浏览器中访问`http://localhost:8081`，在顶部导航栏中找到输入的文本，在那里键入`http://localhost:8080/docs`，然后点击`Explore`。

你有它！一个交互式图形界面来查看您的 API 文档。

**看到我们运行 docker 实例时设置的版本标签了吗？这很重要。正如我在上一篇文章中所说，scalatra swagger 提供了 Swagger V1 JSONS，这些 JSONS 已被弃用，swagger UI 的`v2.2.9`是支持 Swagger V1 的最新版本。**

## 包装独立的罐子

这是一个快速的步骤，我们只需要在`build.sbt`文件中添加两行，因为当我们开始这个项目时，大部分设置已经完成:

```
mainClass in Compile := Some("com.svinci.professionals.api.JettyLauncher")
mainClass in assembly := Some("com.svinci.professionals.api.JettyLauncher") 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我们告诉 assembly 哪个是我们的主类。这个`JettyLauncher`对象写在之前的文章中，它能够像 scalatra 插件一样启动 JettyServer。

现在我们将运行一些命令:

*   首先，为了组装 JAR，我们运行`sbt assembly`。将会有几行警告日志，但它们并不重要。
*   其次，我们到 JAR 所在的目录做`cd target/scala-2.11`。
*   在那里你会找到一个名为`professionals-api.jar`的罐子。通过执行`java -jar professionals-api.jar`，您可以使用默认配置运行 JAR。
*   `application.conf`中的配置可以用 java 选项覆盖，如`java -Dapplication.port=8082 -jar professionals-api.jar`<——该命令将运行服务器，但它将监听端口 8082。

## 结论

斯卡拉、SBT、斯卡拉特拉和斯瓦格都很棒。是的，我想搬到斯瓦格 V2，但这不是问题。用这些技术编写的 API 工作得很好，性能也很好。

你可以在这里找到这个帖子系列[的代码。](https://github.com/svinci/scala-scalatra-swagger-example)

非常感谢你的阅读！

评论里见！