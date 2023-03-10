# 使用 Swagger、Jane 和 Behat 记录和测试 Symfony API -第 2 部分

> 原文：<https://dev.to/matks/use-swagger-jane-and-behat-to-document-and-test-a-symfony-api---part-2-d47>

这篇文章是上一篇文章的第二部分[在这里](https://dev.to/mathieuks/use-swagger-to-document-a-symfony-api-790)。

# 简

现在他们可以阅读你的大摇大摆的文档，开发人员开始使用你的 API 构建很酷的应用程序:javascript web 应用程序，移动应用程序...

有一个开发人员问“你的 API 和你的文档很棒，但是我必须构建一个 HTTP 客户端来为你的 API 构建正确的请求，并解析 JSON 响应来提取我想要的数据。你知道是否有人构建了这样一个 API 客户端作为 SDK，这样我就不用全部重写了？”。

他是对的:对他来说，专注于他的应用程序业务逻辑和 UI 比编写代码为你的 API 构建预期的 HTTP 请求重要得多。他必须关注他想使用 API 的“为什么”,而不是“如何”。

有趣的是，在 Swagger 文件中提供了“如何”的所有细节:路由、请求体结构、响应格式和 http 代码、必需的 http 头...

## API 客户端生成工具

有很多工具可以从一个 Swagger 文件为多种编程语言构建一个 API 客户端。对于 PHP，有 [Jane OpenAPI](https://github.com/janephp/openapi) 。基本上，你给它你的 Swagger 文件，它生成所需的 PHP 类。相当整洁。

下面是一个生成的 PHP 代码示例，用于执行 GET HTTP 调用来获取电影:

```
 /**
     * Get movies
     *
     * @param array  $parameters {
     *     @var string $order Order criterion
     *     @var string $dir Sort criterion
     *     @var int $page Page number
     * }
     * @param string $fetch      Fetch mode (object or response)
     *
     * @return \Psr\Http\Message\ResponseInterface|\ApiCycle\Generated\ApiMoviesClient\Model\MoviesViewDTO
     */
    public function getMovies($parameters = array(), $fetch = self::FETCH_OBJECT)
    {
        $queryParam = new QueryParam();
        $queryParam->setDefault('order', NULL);
        $queryParam->setDefault('dir', NULL);
        $queryParam->setDefault('page', NULL);
        $url = '/v1/movies';
        $url = $url . ('?' . $queryParam->buildQueryString($parameters));
        $headers = array_merge(array('Host' => 'localhost', 'Accept' => array('application/json')), $queryParam->buildHeaders($parameters));
        $body = $queryParam->buildFormDataString($parameters);
        $request = $this->messageFactory->createRequest('GET', $url, $headers, $body);
        $promise = $this->httpClient->sendAsyncRequest($request);
        if (self::FETCH_PROMISE === $fetch) {
            return $promise;
        }
        $response = $promise->wait();
        if (self::FETCH_OBJECT == $fetch) {
            if ('200' == $response->getStatusCode()) {
                return $this->serializer->deserialize((string) $response->getBody(), 'ApiCycle\\Generated\\ApiMoviesClient\\Model\\MoviesViewDTO', 'json');
            }
        }
        return $response;
    } 
```

Enter fullscreen mode Exit fullscreen mode

所以步骤是:
1)使用 Jane 生成 API 客户端类
2)自动加载它们
3)开始使用它们！

我将生成的类放在我的 API 客户端的`generated/`文件夹中，并在我的 composer.json 中要求自动加载它:

```
{
  "name": "matks/movies-api-client",
  "license": "MIT",
  "type": "project",
  "autoload": {
    "psr-4": {
      "ApiCycle\\Generated\\ApiMoviesClient\\": "generated/"
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个我如何使用生成的 php 类的例子:

```
// in a Silex app
$resource = $app['app.api.api-client'];

$response1 = $resource->getMovies();

$body = new MoviesBody();
$body->setName('A new movie 2');

$response2 = $resource->createMovie($body); 
```

Enter fullscreen mode Exit fullscreen mode

并且下次 API 改变时，例如`/v1/movies`变成`/v2/api/get-movies`；或者 POST 端点的请求体需要一个新的强制项，我可以重新生成我的 API 客户机来处理新的 API 需求。`generated`代码被更新了，但是使用它的代码没有更新。“如何做”现在是全自动的(而且更可靠，因为它是由机器人而不是人写的)。

# 使用 behat 和 API 客户端测试 API

Behat 是一个 php 框架，允许你为你的应用程序编写测试场景。
而不是编写测试，例如:

```
public function testGetMoviesWithBadInput()
    {
        $client = static::createClient();

        $crawler = $client->request('GET', '/v1/movies?order=bad');
        $response1 = $client->getResponse();

        $expectedData1 = [
            'message' => 'Only allowed value for order is \'name\'',
            'error' => 'Bad query',
        ];

        $this->assertJsonResponse($response1, Response::HTTP_BAD_REQUEST);
        $this->assertJsonContent($response1, $expectedData1);

        $crawler = $client->request('GET', '/v1/movies?order=name&dir=a');
        $response2 = $client->getResponse();

        $expectedData2 = [
            'message' => 'Dir must be one of those: asc, desc',
            'error' => 'Bad query',
        ];

        $this->assertJsonResponse($response2, Response::HTTP_BAD_REQUEST);
        $this->assertJsonContent($response2, $expectedData2);
    } 
```

Enter fullscreen mode Exit fullscreen mode

你写一些人类可读的场景，比如:

```
 Scenario: Create a movie
    When I create a movie "Amazing new movie Z"
    Then I should receive a success response
    When I fetch page 1 from movies from the Api using an "asc" sorting on names
    Then I should receive a list of 3 movies with a total of 31
    And the list should contain:
      | movie name          |
      | Amazing new movie Z | 
```

Enter fullscreen mode Exit fullscreen mode

这些场景是人类可读的，这一事实造成了很大的不同:编写、维护和更新要容易得多。你甚至可以认为它是一个可运行的用户友好的文档，记录了你的应用程序的行为。

如果你想进一步发现它，有很多关于它的教程和博客帖子，比如[这个](https://blog.eleven-labs.com/fr/behat-structurez-vos-tests-fonctionnels/)。

总结一下它的工作原理:

*   您可以使用这种“When X Then Y”语法编写人类可读的场景。特征文件
*   您编写的 PHP 代码能够将这些语句翻译成 FeatureContexts 文件中的 PHP 调用
*   您使用 behat binary 运行测试

“当我从 Api 获取电影”和一个实际的 PHP 调用之间的“转换”如下:

```
 /**
     * @When I fetch movies from the Api
     */
    public function fetchMoviesBasic()
    {
        try {
            $this->latestResponse = self::getApiClient()->getMovies();
        } catch (\Exception $e) {
            $this->handleExceptionsAfterAPICall($e);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是我使用生成的 API 客户端的地方。

所以我写了一个关于我的 API
的[完整特性集](https://github.com/matks/ApiCycle/blob/master/apps/movies-api-client/features/movies.feature)

```
Feature: Movies API
  In order to manage movies
  As an API Client
  I need to be able to use the Movies API

  Scenario: Get basic movies list
    When I fetch movies from the Api
    Then I should receive a list of 3 movies with a total of 30
    And the list should contain:
      | movie name         |
      | Fast and Furious 8 |
      | Taken 3            |

  Scenario: Get advanced movies list
    When I fetch page 3 from movies from the Api using an "desc" sorting on names
    Then I should receive a list of 3 movies with a total of 30
    And the list should contain:
      | movie name            |
      | Another great movie 6 |

  Scenario: Create a movie
    When I create a movie "Amazing new movie Z"
    Then I should receive a success response
    When I fetch page 1 from movies from the Api using an "asc" sorting on names
    Then I should receive a list of 3 movies with a total of 31
    And the list should contain:
      | movie name          |
      | Amazing new movie Z |

  Scenario: Create a movie twice and receive a bad response
    When I create a movie "Spiderman"
    Then I should receive a success response
    When I create a movie "Spiderman"
    Then I should receive a bad response

  Scenario: Delete a movie
    When I delete the movie 31
    Then I should receive a null response
    When I fetch page 1 from movies from the Api using an "asc" sorting on names
    Then I should receive a list of 3 movies with a total of 31
    And the list should NOT contain:
      | movie name          |
      | Amazing new movie Z |

  Scenario: Delete a movie twice and receive a bad response
    When I delete the movie 32
    Then I should receive a null response
    When I delete the movie 32
    Then I should receive a bad response 
```

Enter fullscreen mode Exit fullscreen mode

然后我写了一个完整的 [FeatureContext](https://github.com/matks/ApiCycle/blob/master/apps/movies-api-client/features/bootstrap/FeatureContext.php) 来将所有的特性“翻译”成有意义的 PHP 函数，并且它是可运行的。

## 状态复位，用于集成测试

这里重要的一点是，这些步骤执行对我的 API 的真正的 HTTP 调用。这意味着 API 是在真实数据库(mysql 或 sqlite)的支持下运行的。这就是为什么在测试套件开始时，我需要重置 API 状态，以便之前的测试运行不会影响这次运行。

我用一个 Makefile 控制的过程来执行这个操作，在这个过程中，我删除数据库并重新创建它，然后用 DataFixtures:

```
load_fixtures: ## load fixtures
    $(APP_CONSOLE) doctrine:schema:create
    $(APP_CONSOLE) doctrine:fixtures:load --no-interaction

reset_fixtures: ## load fixtures after cleaning the database
    $(APP_CONSOLE) doctrine:schema:drop --force --no-interaction
    $(MAKE) load_fixtures 
```

Enter fullscreen mode Exit fullscreen mode

然而，要正确地完成，它应该由我的 behat 测试过程来完成，而不是在 Makefile 之外。Behat 提供了一个[挂钩过程](http://docs.behat.org/en/v2.5/guides/3.hooks.html)，在这个过程中，您可以在套件之前或每个场景之前或每个步骤之前执行一些步骤。估计我很快会根据这条线索更新项目:)。

### 结论

我能够从我的 API 生成一个 Swagger 规范文件，它可以作为文档提供给其他开发人员，或者可以帮助他们用他们需要的任何语言构建 API 客户端。这对于编写使用 API 的应用程序非常有用...或集成测试来检查 API 行为是否正确。

工具很牛逼:)