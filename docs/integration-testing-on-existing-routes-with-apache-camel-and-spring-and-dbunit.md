# 在现有路线上使用 Apache Camel(以及 Spring 和 DBUnit)进行集成测试

> 原文：<https://dev.to/matthieusb/integration-testing-on-existing-routes-with-apache-camel-and-spring-and-dbunit>

### 主体和目标受众

本文旨在展示如何在 Spring 应用程序中使用 Apache Camel 进行集成测试，因为文档中并没有对此进行精确描述。熟悉 Apache Camel 特性的初学者和开发人员都可以阅读。尽管如此，一点点知识让它更容易理解。

## 简介

使用这种方法，您将能够从头到尾启动一个现有的路由(有或没有您的真实数据库)，拦截路由各部分之间的交换，并检查您的头或正文是否包含正确的值。

我一直在一个经典的 Spring 项目中使用 xml 配置和 DBUnit 进行数据库模拟。希望这能给你一些线索。

## 设置

如果您的项目中还没有这样做，那么您应该为 camel 添加测试依赖项。maven 用户见下图:

```
<dependency>
  <groupId>org.apache.camel</groupId>
  <artifactId>camel-test</artifactId>
  <version>${camel.version}</version>
  <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-test-spring</artifactId>
    <version>${camel.version}</version>
     <scope>test</scope>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

## 骆驼路线举例

以下路线有一个简单的目标:

*   首先，它检查数据库中是否存在 ImportDocumentProcess 对象，并将其作为交换头添加
*   然后，它在数据库中添加一个 ImportDocumentTraitement(它链接到前面的 ImportDocumentProcess)

这条路线的代码是:

```
@Component
public class TestExampleRoute extends SpringRouteBuilder {

    public static final String ENDPOINT_EXAMPLE = "direct:testExampleEndpoint";

    @Override
    public void configure() throws Exception {
        from(ENDPOINT_EXAMPLE).routeId("testExample")
            .bean(TestExampleProcessor.class, "getImportDocumentProcess").id("getImportDocumentProcess")
            .bean(TestExampleProcessor.class, "createImportDocumentTraitement").id("createImportDocumentTraitement")
            .to("com.pack.camel.routeshowAll=true&multiline=true");
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

路由上的 *id* 不是强制的，您也可以在之后使用 bean 字符串。然而，我认为使用 ids 可以被认为是一个好的实践，以防将来您的路由字符串发生变化。

## Camel 处理器示例

处理器只包含路由所需的方法。它只是一个包含几个方法的经典 Java Bean。您也可以实现 Processor 并重写 process 方法。

参见下面的代码:

```
@Component("testExampleProcessor")
public class TestExampleProcessor {

    private static final Logger LOGGER = LogManager.getLogger(TestExampleProcessor.class);

    @Autowired
    public ImportDocumentTraitementServiceImpl importDocumentTraitementService;

    @Autowired
    public ImportDocumentProcessDAOImpl importDocumentProcessDAO;

    @Autowired
    public ImportDocumentTraitementDAOImpl importDocumentTraitementDAO;

    // ---- Constants to name camel headers and bodies
    public static final String HEADER_ENTREPRISE = "entreprise";

    public static final String HEADER_UTILISATEUR = "utilisateur";

    public static final String HEADER_IMPORTDOCPROCESS = "importDocumentProcess";

    public void getImportDocumentProcess(@Header(HEADER_ENTREPRISE) Entreprise entreprise, Exchange exchange) {
        LOGGER.info("Entering TestExampleProcessor method : getImportDocumentProcess");

        Utilisateur utilisateur = SessionUtils.getUtilisateur();
        ImportDocumentProcess importDocumentProcess = importDocumentProcessDAO.getImportDocumentProcessByEntreprise(
                entreprise);

        exchange.getIn().setHeader(HEADER_UTILISATEUR, utilisateur);
        exchange.getIn().setHeader(HEADER_IMPORTDOCPROCESS, importDocumentProcess);
    }

    public void createImportDocumentTraitement(@Header(HEADER_ENTREPRISE) Entreprise entreprise,
            @Header(HEADER_UTILISATEUR) Utilisateur utilisateur,
            @Header(HEADER_IMPORTDOCPROCESS) ImportDocumentProcess importDocumentProcess, Exchange exchange) {
        LOGGER.info("Entering TestExampleProcessor method : createImportDocumentTraitement");

        long nbImportTraitementBefore = this.importDocumentTraitementDAO.countNumberOfImportDocumentTraitement();
        ImportDocumentTraitement importDocumentTraitement = this.importDocumentTraitementService.createImportDocumentTraitement(
                entreprise, utilisateur, importDocumentProcess, "md5_fichier_example_test", "fichier_example_test.xml");
        long nbImportTraitementAfter = this.importDocumentTraitementDAO.countNumberOfImportDocumentTraitement();

        exchange.getIn().setHeader("nbImportTraitementBefore", Long.valueOf(nbImportTraitementBefore));
        exchange.getIn().setHeader("nbImportTraitementAfter", Long.valueOf(nbImportTraitementAfter));
        exchange.getIn().setHeader("importDocumentTraitement", importDocumentTraitement);
    }
// Rest of the code contains getters and setters for imported dependencies
} 
```

Enter fullscreen mode Exit fullscreen mode

这里不多说，只说我们用交换把对象从一个部分转移到另一个部分。这是我的项目通常采用的方式，因为我们有非常复杂的过程要处理。

## 骆驼测试类

测试类将在示例路由上触发并运行测试。我们还使用 DBUNit 用一些预定义的值模拟数据库。

首先，我们使用一个抽象类，以便在每个 Camel 集成测试之间共享公共注释。参见下面的代码:

```
@RunWith(CamelSpringRunner.class)
@BootstrapWith(CamelTestContextBootstrapper.class)
@ContextConfiguration(locations = { "classpath:/test-beans.xml" })
@DbUnitConfiguration(dataSetLoader = ReplacementDataSetLoader.class)
@TestExecutionListeners({ DependencyInjectionTestExecutionListener.class, DirtiesContextTestExecutionListener.class,
        DbUnitTestExecutionListener.class })
@DirtiesContext(classMode = ClassMode.AFTER_EACH_TEST_METHOD) // Not always relevant,  See explanation below
public abstract class AbstractCamelTI {

} 
```

Enter fullscreen mode Exit fullscreen mode

注意不要忘记任何注释,否则你的 DAOs 将不会被正确地注入，这将导致你运行测试时出现 NullPointer 异常。如果使用另一个数据库模拟系统，可以删除 DBUnit 注释。

**重要提示:**`@DirtiesContext(classMode = ClassMode.AFTER_EACH_TEST_METHOD)`注释在这里为*每次测试*重新加载 camel 上下文。这样，每个测试方法都有一个新的重新初始化的上下文。但是您也需要为每个测试重新定义模拟端点。如果您需要重构大型 camel 处理器并独立测试每个部分，这可能会有所帮助。否则，只需**删除**该注释，因为它有一些缺点:

*   这些测试不再是真正的集成测试，并且您必须为每个测试移除和编织您的路由部分，这可能有点沉重
*   为每个测试重新加载上下文需要一段时间，并且会显著增加测试的总运行时间。从长远来看，这可能会令人沮丧

### 测试类配置

```
@DatabaseSetup(value = { "/db_data/dao/common.xml", "/db_data/dao/dataForImportDocumentTests.xml" })
public class TestExampleProcessorTest extends AbstractCamelTI {

    @Autowired
    protected CamelContext camelContext;

    @EndpointInject(uri = "mock:catchTestEndpoint")
    protected MockEndpoint mockEndpoint;

    @Produce(uri = TestExampleRoute.ENDPOINT_EXAMPLE)
    protected ProducerTemplate template;

    @Autowired
    ImportDocumentTraitementDAO importDocumentTraitementDAO;

    // -- Variables for tests
    ImportDocumentProcess importDocumentProcess;

    @Override
    @Before
    public void setUp() throws Exception {
        super.setUp();

        importDocumentProcess = new ImportDocumentProcess();
        //specific implementation of your choice
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第一次测试

这个测试触发了路由的第一部分，并将其引向一个`mockEndpoint`，这样我们就可以测试 *ImportDocumentProcess* 是否被正确选择并放入了头:

```
@Test
public void processCorrectlyObtained_getImportDocumentProcess() throws Exception {
    camelContext.getRouteDefinitions().get(0).adviceWith(camelContext, new AdviceWithRouteBuilder() {

        @Override
        public void configure() throws Exception {
            weaveById("getImportDocumentProcess").after().to(mockEndpoint);
        }
    });

    // -- Launching the route
    camelContext.start();
    template.sendBodyAndHeader(null, "entreprise", company);

    mockEndpoint.expectedMessageCount(1);
    mockEndpoint.expectedHeaderReceived(TestExampleProcessor.HEADER_UTILISATEUR, null);
    mockEndpoint.expectedHeaderReceived(TestExampleProcessor.HEADER_IMPORTDOCPROCESS, importDocumentProcess);
    mockEndpoint.assertIsSatisfied();

    camelContext.stop();
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第二次测试

这个测试简单地测试了整个路线:

```
@Test
public void traitementCorrectlyCreated_createImportDocumentTraitement() throws Exception {
    camelContext.getRouteDefinitions().get(0).adviceWith(camelContext, new AdviceWithRouteBuilder() {

        @Override
        public void configure() throws Exception {
            weaveById("createImportDocumentTraitement").after().to(mockEndpoint);
        }
    });

    // -- Launching the route
    camelContext.start();

    Exchange exchange = new DefaultExchange(camelContext);
    exchange.getIn().setHeader(TestExampleProcessor.HEADER_ENTREPRISE, company);
    exchange.getIn().setHeader(TestExampleProcessor.HEADER_UTILISATEUR, null); // No user in this case
    exchange.getIn().setHeader(TestExampleProcessor.HEADER_IMPORTDOCPROCESS, importDocumentProcess);

    long numberOfTraitementBefore = this.importDocumentTraitementDAO.countNumberOfImportDocumentTraitement();

    template.send(exchange);

    mockEndpoint.expectedMessageCount(1);
    mockEndpoint.assertIsSatisfied();

    camelContext.stop();

    long numberOfTraitementAfter = this.importDocumentTraitementDAO.countNumberOfImportDocumentTraitement();
    assertEquals(numberOfTraitementBefore + 1L, numberOfTraitementAfter);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 好的做法

如果你想避免在简单的错误上浪费时间，可以遵循一些指导方针。

#### 路线 id

在这个例子中，我使用下面的代码行来获取我的路线并编织我的测试端点:

```
camelContext.getRouteDefinitions().get(0).adviceWith(camelContext, new AdviceWithRouteBuilder() { [...] }); 
```

Enter fullscreen mode Exit fullscreen mode

**然而**一个更好的方法是将 id 影响到您的路线，并使用下面的行来获取和编织它们:

```
camelContext.getRouteDefinition("routeId").adviceWith(camelContext, new AdviceWithRouteBuilder() {  [...]  }); 
```

Enter fullscreen mode Exit fullscreen mode

这种方法更简洁，因为您不必手动确认要测试哪条路由。重构路由也稍微容易一些，因为您可以更改路由的字符串，而无需更改它们的 id。