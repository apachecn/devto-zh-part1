# 没有测试-没有拉请求，对不对？代码库中应该包含的测试类型

> 原文：<https://dev.to/backendandbbq/no-tests---no-pull-request-right-types-of-tests-that-should-be-in-your-codebase-b7g>

*原载于我的博客:* **[你需要的只是后端](http://allyouneedisbackend.com/blog/2017/10/09/no-tests-no-pull-request-types-of-automated-tests-in-backend-software/)** 。

正如博文 **[所拉的好恶丑](http://allyouneedisbackend.com/blog/2017/08/24/pull-requests-good-bad-and-ugly/)** 所言:

> #### If you don't have time to write the test today-you will find time to fix the bug on Friday night

换句话说，为了在明天的生产中建立稳固的可靠性，我们需要在今天投入时间。您当前项目的测试需求取决于:

*   维护代码库的团队规模:`return True if team.size > 1 else False`。有更多的工程师意味着对同样的项目有更多的看法。测试有助于记录如何使用一个类或函数的观点。
*   代码库的大小:`return True if project.modules > 1 else False`。你不记得你两天前穿的袜子的颜色。你能记住项目中的一切吗？
*   项目开发和维护阶段的持续时间。您只运行一次的脚本在没有可靠的测试覆盖的情况下也能很好地运行。如果你正在构建一个系统几十年——请为下一代开发者准备一份好的遗产。

我有一种强烈的感觉，你认为你的代码需要测试，因为你还在读这篇文章。

在这篇博文中，我将引导你了解应该由软件工程师实现的自动化测试的类型:**单元**、**集成**、**外部**和**性能**测试。它没有涵盖质量工程师的测试工作，但是这篇文章对他们来说仍然是有价值的。

您将找到使用 Python 的代码示例，但您不必了解这种语言。

* * *

## 什么是自动化测试？

软件测试是一件消耗时间的事情，这些时间可以合理地用于不稳定特性的开发。总是问你的领导或企业主什么是产品的首选。它有助于确定适当的优先顺序。

没有经验的软件开发人员通常认为测试应该由质量保证团队专门完成。我倾向于不同意。优秀的工程师拥有自己的东西。

在一个测试中，你调用一个已经写好或者还不存在的函数(阅读更多关于 **[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)**)。您传递一些参数，并期望函数返回一个特定的值。如果值是错误的，这意味着测试失败，代码被破坏。*或者测试执行得不好。*

一些编程语言提供了像 Python 一样将测试包装到文档中的能力。它叫做。

```
def multiply(s: str, n: int) -> str:
    """Repeats a string multiple times.

    Args:
        s (str): name to repeat.
        n (int): multiplier.

    Examples:
        >>> multiply('Backend', 2)
        'BackendBackend'
        >>> multiply('Omn', 3)
        'OmnOmnOmn'
    """
    return s * n 
```

如果你测试一个 **[纯函数](https://en.wikipedia.org/wiki/Pure_function)** ，编写好的测试会更容易:一个函数的输出完全由它的输入决定。运行纯函数没有副作用。

自动化测试本身并不存在。它们由持续集成服务器执行，如 [Bamboo](https://www.atlassian.com/software/bamboo) 、 [Jenkins](https://www.g2crowd.com/products/jenkins/reviews) 或 [Travis CI](https://travis-ci.org/) 。通常，会对每个提交的 PR 执行测试。如果构建是绿色的——在代码审查之后，可以考虑将分支合并到主分支中。显然，工程师在推送代码之前会在本地运行测试。没人喜欢审查一个不工作的修道院。

在接下来的部分中，您可以找到我推荐后端软件提供的测试的概述。

## 单元测试

这种类型的测试是最受欢迎和最有名的。在一家新公司的面试中，一个合适的问题可以是“你的团队为新代码编写单元测试吗？”。

玩笑归玩笑，单元测试的目的是确保代码的原子单元按预期工作。通常，代码单元是一个函数或一个方法。单元测试必须小而快，把所有的东西都放在一个运行测试套件的过程中。并且不与任何其他东西交互。当我们需要检查代码中业务规则的正确性时，这种类型的测试是一个很好的工具。

下面是函数`parse_fullname`的单元测试示例，它解析一个人的全名以获得名字和姓氏:

```
from unittest import TestCase

from utils import parse_fullname

class ParseFullnameTestCase(TestCase):
    def test_parse_fullname(self):
        cases = [
            ('John Doe', ('John', 'Doe'), 'first and last name'),
            ('John David Doe', ('John David', 'Doe'), 'first, middle, last name'),
            ('John David van Eck de la Nova Doe', ('John David van Eck de la Nova', 'Doe'),
                'many name parts'),
            ('John', ('John', 'John'), 'single name'),
            ('John David Doe, Jr.', ('John David', 'Doe Jr'), 'Jr. suffix'),
            ('John Doe II', ('John', 'Doe II'), 'II suffix'),
            ('Mr. John Doe', ('John', 'Doe'), 'Mr. prefix'),
            ('Вячеслав Каковський', ('Вячеслав', 'Каковський'), 'unicode chars')
        ]
        for name, expected_output, description in cases:
            self.assertEqual(parse_fullname(name), expected_output, msg='Failed for {}'.format(description)) 
```

该测试检查返回值是否与每种情况下的预期值匹配，并在断言失败时提供解释。

同样，单元测试越快越好。例如，为生产软件执行数百个单元测试需要几秒钟，很少需要几分钟。

如何做好没有副作用的单元测试？
我们可以使用 **[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)** 用 **[模仿、存根或者假对象](https://martinfowler.com/articles/mocksArentStubs.html)** 来替代执行繁重操作的对象。是的，你的单元测试不应该执行 I/O 操作，比如从数据库中读/写数据，执行 HTTP 调用等等。检查`@retry` decorator 的单元测试，如果发生指定类型的异常，它会尝试重新执行。

```
from aiohttp import DisconnectedError
from asynctest import TestCase, CoroutineMock as Mock

from utils import retry

class RetryTest(TestCase):
    async def test_retry(self):
        self._func = Mock(return_value=200, 
                          side_effect=[DisconnectedError, 
                                       DisconnectedError, 200]

        @retry(DisconnectedError)
        async def get_http_status():
            return await self._func()

        res = await get_http_status()
        self.assertEqual(res, 200) 
```

我们使用`Mock`对象引入一个具有预定义行为的函数:引发`DisconnectedError`两次并返回状态代码 200，这意味着成功的 HTTP 请求。值得庆幸的是，我们不必执行对某个 web 服务器的实际请求，也不必做所有缓慢的 I/O 工作。此外，我们不需要通过配置服务器或负载平衡器来执行一些调整，以便在每次执行测试时断开连接。

我鼓励你在我的另一篇博文 **[中阅读关于`retry`功能的内容【永不放弃，重试:软件应该如何处理失败](http://allyouneedisbackend.com/blog/2017/09/15/how-backend-software-should-retry-on-failures/)** 。我发现这项技术在制作依赖于各种其他服务的后端时非常有用。

现实生活中单元测试非常适合的例子:

*   所有类型的解析:消息、文档、参数和配置
*   检查业务规则和边角案例
*   复杂 if-else 语句链的输入验证或其他验证
*   数学公式的计算，如折扣的商业规则
*   从一种格式到另一种格式的复杂数据转换
*   验证由 ORM 编译的 SQL 查询(不要与针对数据库的查询混合执行)
*   当您需要检查一个函数的调用是否会导致另一个函数的调用时；我强烈建议使用模拟
*   验证点火网络操作，但不要忘记用存根替换实际的 I/O 操作。

对于大多数现代编程语言，您可以找到快速编写良好单元测试的强大工具箱。它可能包括:

*   用于实现模拟、存根和假对象的原语
*   测试套件中测试之前/之后运行的挂钩
*   从命令行运行一组测试的实用程序
*   用于在各种环境下运行测试的工具，比如解释器/虚拟机的版本。

## 集成测试

这种测试的主要目的是验证你开发的各种模块和组件之间的合作。这里鼓励您在测试中执行 I/O 操作，因此，测试套件可能会运行缓慢。

这些测试侧重于子系统上的 API 契约以及与您使用的数据存储的集成。

> #### For me, the main feature of integration tests is that they don't have to run only in one process: the code under test can execute a system call or a query against a real database.

检查为高级方法实现数据库包装器的`SQLAlchemyEngine`类的集成测试:

*   `execute`:执行 SQLAlchemy 查询，返回受影响的行数
*   `fetchone`:获取一个数据库条目的简写
*   `fetchall`:获取所有数据库条目的简写。

```
import uuid

from asynctest import TestCase

from database import SQLAlchemyEngine, users
from utils import get_config

class DBEngineTests(TestCase):
    async def setUp(self):
        self._user_data = {'user_id': 100500,
                           'external_id': uuid.uuid4().hex,
                           'name': 'Viach'}
        self._db_engine = self._get_db_engine()
        self._user = await self._create_user(self._user_data)

    async def tearDown(self):
        async with self._db_engine.acquire() as conn:
            await conn.execute(users.delete())

    async def test_fetchone(self):
        fetched_user = await self._db_engine.fetchone(
            users.select(users.c.id == self._user['id']))
        self.assertEqual(fetched_user, self._user_data)

    async def test_execute_delete_user(self):
        rowcount = await self._db_engine.execute(
            users.delete(users.c.id == self._user['id']))
        self.assertEqual(rowcount, 1)

        fetched_user = await self._db_engine.fetchone(
            users.select(users.c.id == self._user['id']))
        self.assertIsNone(fetched_user)

    async def test_fetchone_not_exists(self):
        fetched_user = await self._db_engine.fetchone(
            users.select(users.c.id == self._user['id'] + 1))
        self.assertIsNone(fetched_user)

    async def test_fetchall(self):
        fetched_users = await self._db_engine.fetchall(
            users.select(users.c.id == self._user['id']))
        self.assertLenEqual(fetched_users, 1)
        self.assertEqual(fetched_users[0], self._user_data)

    async def _create_user(self, user_data):
        async with self._db_engine.acquire() as conn:
            await conn.execute(users.insert().values(**user_data))

            result = await conn.execute(users.select().where(
                users.c.id == user_data['id']))
            return result

    async def _get_db_engine(self):
        return await SQLAlchemyEngine.from_config(get_config()['mysql']['userbase'],
                                                  loop=self.loop) 
```

我认为当您需要验证与数据库相关的代码时，集成测试是一个完美的想法:

*   在您的代码库中嵌入第三方数据存储驱动程序；插入记录并提取记录的冒烟测试通常就足够了
*   依赖于数据库状态的复杂查询；不要忘记在预测试挂钩中设置状态
*   不复杂的查询，当您不使用 ORM 并且不能检查编译的语句时(使用 ORM，您可以通过单元测试来完成)
*   现有数据库驱动程序的自制包装/补丁。

根据我的经验，集成测试的其他可能应用:

*   测试子系统之间的契约，比如模块之间的公共接口
*   验证您的服务之间的通信；比方说，确保一个服务针对某个任务执行另一个服务的请求的测试。

请注意，您仍然可以并且应该使用模拟来替换软件的某些部分，以使测试环境的建立更容易，测试的执行更快。这有助于将运行这类测试的时间保持在几分钟到几十分钟之间。

我们回顾了单元测试和集成测试，第一类的目的是验证单个组件是否按预期工作；写第二个的原因是——检查你实现的部分的组合是作为一个团队发挥作用的。

但是，如果您的产品涉及的软件不是由您的团队编写的，并且运行不受组织的控制，该怎么办呢？是时候研究下一类测试了。

## 外部测试

当您需要跟踪您的软件和您的团队无法控制的第三方服务之间的合同时，您应该编写外部测试。它可以是由公司内部其他团队维护的服务，也可以是在您的供应商、合作伙伴甚至竞争对手的基础设施上运行的软件。

外部测试要测试的事物的真实例子:

*   贵公司团队之间的 API 合同
*   使用您的云提供商提供的服务；我的情况是 AWS
*   与服务开发者 API 的集成，如 [Stride](https://developer.atlassian.com/cloud/stride/) 、 [Hipchat](https://developer.atlassian.com/hipchat) 、 [Bitbucket](https://developer.atlassian.com/bitbucket/api/2/reference/) 、 [Trello](https://developers.trello.com/v1.0/reference) 、 [GitHub](https://developer.github.com/) 、 [Slack](https://api.slack.com/) 等。

您可能会感兴趣为什么外部测试在一个单独的类别中，而不是集成测试的一部分？

首先，你的团队没有必要修复外部测试的失败。如果系统在另一端坏掉了——你只能提交一个 bug 报告，并尝试对它进行优先级排序。

其次，由于您无法控制另一端的环境，一些故障可能是随机的:可用性问题、糟糕的部署等。

查看一个外部测试的例子，验证与亚马逊 SQS 一起工作的代码:

```
 class SQSTestCase(TestCase):
    async def setUp(self):
        config = get_config()
        self.client = await SQSClient.from_config(config, loop=self.loop)

    def tearDown(self):
        self.client.close()

    async def test_send_and_receive(self):
        msg = {'id': 100500,
               'description': 'some important SQS task'}
        await self.client.send_message(msg)
        result = await self.client.receive_messages()
        self.assertEqual(result, [msg]) 
```

在*某些情况下*忽略外部测试的失败不会阻碍部署。但是仍然需要找出红色构建的原因。

## 性能测试

> #### The purpose of performance test is to predict when we will start production.

换句话说，它有助于发现我们的算法、架构或基础设施无法正确处理负载的情况。我认为高负载系统必须进行性能测试。我发表了一篇简短的博客 **[什么是高负载项目？](http://allyouneedisbackend.com/blog/2017/08/30/what-is-highload/)** 关于我对这个术语的定义，如果你有兴趣可以看看。

查看将性能测试添加到您的工作流程中的步骤。

**确定**负载可能如何增长。可能的情况:

*   越来越多的用户开始使用你开发的软件。
*   更多的数据通过您的处理管道发送。
*   由于预算的变化，您需要缩减服务器的容量。
*   各种意想不到的边缘情况。

**定义**最繁重、最频繁的操作。示例:

*   插入数据存储器。
*   计算和其他 CPU 密集型任务。
*   调用外部服务。

**确定**如何从用户的角度触发上述操作。例如:

*   HTTP/XMPP/your-favorite-protocol 处理器。
*   REST API 端点。
*   定期处理收集的数据。

**为确定的操作设置产品指标**的收集:

*   可以使用 StatsD 收集应用程序指标。我最常使用的是计数器和计时器。
*   对于每个实例的度量，我可以推荐 CollectD。要研究的前 5 个指标:平均负载、CPU、RAM、接收/发送的字节和可用磁盘。

**创建一个工具**,它的行为就像大量客户使用你的产品并触发繁重的操作。对于 web 服务器，此类操作可以是:

*   建立网络连接。
*   发出 HTTP 请求、发送数据和检索信息。

**在启用指标收集的试运行环境中运行工具**。优雅地展开负载，研究系统的行为，注意任何峰值。如果使用的话，您还可以测试基础设施的自动伸缩。

一次成功的性能测试的可能结果:

*   您知道特定的基础设施配置每秒可以处理多少请求。
*   你知道当超过限制时系统会有什么反应。
*   你看到了平台的瓶颈。
*   你知道系统的某个部分是否可以扩展。

开始执行性能测试可能是一件好事。它用 Python 编写，运行分布在多个主机上的负载测试，并支持各种协议，包括 [HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 、 [XMPP](https://en.wikipedia.org/wiki/XMPP) 和 [XML-RPC](https://en.wikipedia.org/wiki/XML-RPC) 。

## 摘要

在博文中，我们简要介绍了四种类型的测试。根据我的经验，测试应该由积极参与产品开发的工程团队提供，而不是一个独立的质量工程团队。

查看下面关于每种测试的总结。

**单元测试:**

*   小而孤立。
*   将所有事情都放在一个进程中，测试中的代码不应该导致系统调用。
*   速度极快。
*   非常适合检查业务规则。
*   在您的开发环境中运行。

**集成测试:**

*   主要目的——验证各种组件是否能很好地协同工作。
*   可以执行输入输出操作。
*   慢点。
*   执行流可以跨进程分布。
*   在您的开发环境中运行。

**外部测试:**

*   确保达成的 API 合同得到正确实施。
*   包括调用你无法直接控制的软件。
*   在您的开发环境和第三方之间运行。服务器。
*   往往很慢。

**性能测试:**

*   如果产品处于高负荷状态，有助于挽救您企业的声誉。
*   需要额外的准备，但值得。
*   在分段环境中执行。
*   非常非常慢，应该在计划的窗口内运行。

我认为每一种成熟的编程语言都有自己的变体，类似于 xUnit 的工具集，用于编写自动化测试以获得乐趣和利润。

我希望你发现博文中的实际例子对你的团队有用。在过去的三年中，我们的团队投入了大量的资源来提供各种类型的测试，作为拉式请求的一部分。我们发现这对我们的产品很有价值。

当我们有足够的测试覆盖率时，团队会觉得在环境中工作更健康，因为我们避免了代码回归。

你为你的代码提供了什么类型的测试？

您花了多少时间来修复不久前发布的功能？