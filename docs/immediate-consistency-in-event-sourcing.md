# 事件采购的即时一致性

> 原文：<https://dev.to/barryosull/immediate-consistency-in-event-sourcing>

混乱事件流系列的延续(如果你可以这样称呼它的话)。你不需要先读它，但是如果你感兴趣，试一试，它会提供更多的上下文。

在[的上一篇文章](https://dev.to/barryosull/messy-event-flows-part-2---what-it-should-be)中，我们谈到了我们的域范围(交叉聚合)约束，并提到了在[事件源系统](https://dev.to/barryosull/event-sourcing-what-it-is-and-why-its-awesome)中实现它们的一种方法，但从那以后我们就没有再深入任何真正的细节。

因此，让我们看看其中的一个约束，并找出如何实现它。

# 约束

> 当一本书被创建时，它必须在一个类别中有一个唯一的编号。

我们如何确保满足上述约束？这似乎是一个简单的问题，但让我们深入探讨一下。

# 用例

这是我们创建一本书的用例，它被我们的 HTTP 控制器使用。

```
<?php
namespace App\Usecase;

use Domain\Projections\BookNumberGenerator;
use Domain\Aggregate\Book;

class AddBookToCategory
{
    private $book_repo;
    private $book_number_generator;

    public function __construct(Book\Repo $book_repo, BookNumberGenerator $book_number_generator)
    {
        $this->book_repo = $book_repo;
        $this->book_number_generator = $book_number_generator;
    }

    public function run($category_id, $book_id, $title, $author)
    {
        $number = $this->book_number_generator->generateNextNumberInCategory($category_id);

        $book = new Book($book_id);
        $book->create($category_id, $title, $author, $number);
        $this->book_repo->store($book);

        $this->book_number_generator->play($book->changes());
    }
}
?> 
```

上面在干什么？

*   通过投影生成书号
*   创建具有该编号的图书
*   存放书籍
*   将事件播放到 BookNumberGenerator 投影中，确保它是最新的。

为了满足约束条件，我们需要我们对图书数量的预测立即保持一致。这就是为什么我们立即在投影中播放事件，而不是让后台进程来处理它。

## 即刻一致

即时一致性意味着一有事情发生，每个人都被立即通知，没有延迟。即。整个系统(服务)对数据有一致的看法。这意味着分配图书编号的投影必须在图书创建后立即更新，否则它可能会分配已经分配的编号。即时一致性是一种阻塞操作，它会迫使您的应用程序锁定数据，并可能导致问题，尤其是当您有多个进程在同一个数据集上操作时。

## 域约束

让我们不要操之过急，直接跳到实现，让我们从领域的角度来看，我们希望发生什么？嗯，如果有人试图用重复的号码创建一本书，应该会失败。这是执行操作的人关心的事情，所以我们希望它明确地失败。既然是这种情况，我们将把失败建模为一个域异常，比如说“BookNumberNotUnique”，并确保每当出现这个问题时抛出它。

## 实现

我们决定把事情简单化。当事件被播放到投影“BookNumberGenerator”中时，如果有一个副本，它应该会失败并抛出域异常“BookNumberNotUnique”。我们将 MySQL 用于我们的投影实现，因此我们将在基础设施中通过添加跨图书编号和类别 id 的唯一索引来实现这条规则。当索引失败时，会发生 MySQL 异常，我们会将该异常转换为我们的域异常并抛出。很简单。

当我们正在构建一个[干净的架构](https://dev.to/barryosull/cleaning-up-your-codebase-with-a-clean-architecture)时，我们的投影通常是在相应技术中具有具体实现的哑接口。此时，我们不关心实现是 MySQL 还是 MongoDB 甚至是文件系统，我们关心的只是满足约束。

## 测试约束

为了确保满足约束，我们需要为它编写一个测试。你可以使用验收测试来做这件事，但是我宁愿把它作为一个集成测试来做，更多的控制，更少的干扰。首先，我们将编写一个测试用例，确保满足 BookNumbersProjection 的契约，仅此而已。

```
<?php
namespace Test\Integration\Domain\Projections;

use Domain\Events\BookAdded;
use Domain\Exceptions\BookNumberNotUnique;
use Domain\Projections\BookNumberGenerator;
use Domain\ValueObjects\CategoryId;
use Domain\ValueObjects\BookNumber;
use Domain\ValueObjects\BookId;

abstract class BookNumberGeneratorTest extends \PHPUnit_Framework_Testcase
{
    protected $book_number_generator;

    public function setUp()
    {
        $this->book_number_generator = $this->makeBookNumberGenerator();
    }

    protected abstract function makeBookNumberGenerator(): BookNumberGenerator;

    // Factory method to make tests easier to build
    private function makeBookedAddedEvent(CategoryId $category_id, BookNumber $book_number): BookAdded
    {
        $book_id = BookId::generate();
        return new BookAdded($book_id, $category_id, $book_number)
    }

    // Existing test
    public function test_gives_next_valid_book_number_for_category()
    {
        $category_id = CategoryId::generate();
        $book_number = new BookNumber(1);
        $event = $this->makeBookedAddedEvent($category_id, $book_number);
        $this->book_numbers->play([$event]);

        $expected_book_number = new BookNumber(2);
                $actual_book_number = $this->book_numbers->generateNextNumberInCategory($category_id);

        $this->assertEquals($expected_book_number, $actual_book_number);
    }

    /** 
     * New tests 
     **/
    public function test_cannot_have_duplicate_books_numbers_in_a_category()
    {
        $category_id = CategoryId::generate();
        $book_number = new BookNumber(1);
        $event_a = $this->makeBookedAddedEvent($category_id, $book_number);
        $event_b = $this->makeBookedAddedEvent($category_id, $book_number);

        $this->expectException(BookNumberNotUnique::class);

        $this->book_numbers->play([$event_a, $event_b]);
    }

    public function test_can_have_duplicate_book_numbers_across_categories()
    {
        $category_id_1 = CategoryId::generate();
        $category_id_2 = CategoryId::generate();
        $book_number = new BookNumber(1);

        $event_category_a = $this->makeBookedAddedEvent($category_id_1, $book_number);
        $event_category_b = $this->makeBookedAddedEvent($category_id_2, $book_number);

        $this->book_numbers->play([$event_category_a, event_category_b]);
    }
}
?> 
```

好了，这是基本的测试案例。您会注意到这是一个抽象的测试类，它没有引用 MySQL，这是因为我们希望将实现细节排除在契约测试之外。下面是 MySQL 实现的集成测试。

```
<?php
namespace Test\Integration\Domain\Projections\BookNumberGenerator;

use Test\Integration\Domain\Projections;
use Infrastructure\Domain\Projections\MySQLBookNumberGenerator;

class MySQLTest extends Projections\BookNumberGenerator
{
    protected abstract function makeBookNumberGenerator(): Projections\BookNumberGenerator
    {
        return new MySQLBookNumberGenerator();
    }
}
?> 
```

这就是考验。我们不会深入研究“MySQLBookNumberGenerator”类本身，您可以想象它在内部是如何工作的，但是值得注意的是，它在内部启动自己的 SQL 客户端，因此我们不需要处理依赖关系。现在已经够好了。

## 如果约束失败会怎样？

如果你仔细观察，你会发现一个潜在的缺陷。当我们将事件播放到投影中时，我们已经将书存储在回购中，这不会导致东西损坏吗？谢天谢地，没有。我们有一个用例运行器类，负责运行我们的用例，就像上面那个。它将一个用例的每次运行包装在一个 DB 事务中。这确保了操作是全有或全无。

# 此实现的问题

上面有一个突出的问题。如果两个或两个以上的人试图在*确切的*同一时间(彼此相差约 50 毫秒)在一个类别中创建一本书，只有一个请求会完成，其余的会失败。这真的没办法。这不太可能，但仍然应该被监控，所以每当我们看到这个特殊的异常，我们就把它发送到我们的错误跟踪器。

那么，如果这成为一个主要问题，即。不断失败？这不太可能，但如果它确实成为我们客户的一个问题，最好有一个计划。

我们有三个选择。前两个解决了问题，第三个永久地解决了问题，但是引入了一个新的领域概念。

## 1。如果失败，重试用例

最简单的解决办法。如果用例由于上面的域约束而失败，我们只需再次重试用例。这是一个简单的尝试/捕捉。这不是长久之计，但在相当长的一段时间内会奏效。

## 2。将请求排队，而不是阻止它们

我们不是强迫其中一个进程失败，而是将它们排队，强迫我们的用例在运行之前等待其他进程完成。这可以通过许多技术来实现(Redis/rabbit MQ/MySQL/等...)并且它将允许我们无故障地处理这个问题。然而它并不完美，因为它最终也会失败。在某一点上，我们将在队列中得到太多的请求，连接将超时/失败。

如我所说，前两个是权宜之计。如果我们想要一个更持久的解决方案，我们需要改变我们对约束的假设。

## 3。软化约束并使用流程管理器

我们再来看看约束条件。

> 当一本书被创建时，它必须在一个类别中有一个唯一的编号。

我们能做些什么吗？好吧，我们得坚持`it has to have a unique number within a category`，那只是一个硬约束，不然书就没法参考了。

那么第一部分呢？

这就是事情变得有趣的地方。事实证明，我们天真地对约束进行了建模，并使其过于激进。图书编号不必立即生成，它可以在图书创建后的一秒钟内生成，这样就可以了。这个数字只在引用这本书的时候使用，而这在这本书真正流通之前是不会发生的，而这发生在它被添加到系统之后很久。

BTW，以上是一个重要的见解。在软件开发中，我们倾向于天真地为我们的约束建模，使它们过于激进，凭空创建“硬约束”。大多数系统范围的约束实际上是软的，或者不是立即一致的。让它们变硬似乎是个好主意，但这会让我们的系统变得脆弱。

## 理想的实现

有了这个新的见解，我们将如何处理这个约束呢？事实证明，这些数字可以最终生成，而不是立即生成，因此我们可以使用一个“进程管理器”来处理这个问题。它监听“BookCreated”事件，每次收到一个事件，它就给该书分配一个编号。它在内部使用一个投影来跟踪每个类别分配的数字，因此它总是知道要分配的正确数字。

这是事件流中的流程管理器的样子。

[![Book Number generation process](img/d8c52ce86bfc86f8e762d9ccb1013285.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2dJTfpg5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8dwoy5jil4432yy8zupr.png)

这个流程管理器将尽可能快地处理事件，每次创建一本书时都分配编号。如果多人试图同时创建一本书，这没问题，因为单个进程正在处理这个问题，数字保证是唯一的，没有竞争条件。上面的模型可以处理同时创建的数量惊人的书籍，所以我们已经有效地消除了这个约束造成的瓶颈。

# 这是不是矫枉过正？

我们为这样一个简单的约束考虑了很多细节，大多数人会认为上面的内容有点过了。这可能会使事件采购和 DDD 看起来令人生畏，但我要说，你可能在你的 CRUD 应用程序中有这些问题，只是你没有意识到它们。

如果你的应用中有一个硬约束，比如一个唯一的号码或电子邮件地址，你将会面临我们上面讨论的问题。唯一的区别是，您将把您的实现视为约束，而不是它的表达式，事情将很快变得混乱/复杂。老实说，你很可能意识不到这些问题，直到他们咬你的屁股，我知道我是。

这是 EventSourcing 和 DDD 的优势，它迫使你考虑你的约束，而不是做出天真的假设，导致失败的实现。

# 结论

本文涵盖了几个主题，但其核心是关于事件来源的即时一致性。从上面，你可以看到这是完全可能的，而且实际上相当简单。

我个人认为在项目开始时将领域范围的约束建模为立即一致是很好的。你可能需要几年时间才会遇到上述问题，当你遇到时，有一些简单的解决方法。只有当我们认为问题会成为问题时，我们才会解决问题，所以只要你有一个监控/警告系统，你就会没事。

感谢阅读，希望你觉得有用。如果你有任何建议或想法，请在评论中告诉我，随时乐意讨论。