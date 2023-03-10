# 练习 YAGNI -给我看看代码

> 原文：<https://dev.to/gonedark/practicing-yagni---show-me-the-code-pjn>

在我发了[练习 YAGNI](https://dev.to/gonedark/practicing-yagni-3n1d) 的帖子后，很多人问我，在代码中，练习 YAGNI 是什么样子的*。*

简单回顾一下——YAGNI 是极限编程的一个原则。YAGNI 是你不需要它的缩写。

我认为极限编程的联合创始人之一 Ron Jeffries 很好地总结了实践 YAGNI:

> 在你真正需要的时候实施，而不是在你预见到你需要的时候。

## 为什么练 YAGNI 很难

尽管有有趣的首字母缩写词和简单明了的总结，程序员经常屈服于过度工程，实现新的闪亮技术，或添加额外的功能。即使我们克服了这些冲动，仍然不清楚在代码中练习 YAGNI 意味着什么。毕竟，我们是程序员，代码胜于雄辩。

拉斯穆斯·勒多夫有一次对我说，*“给我看看代码”*。所以我将在这篇文章的剩余部分讲述一系列的[故事](https://www.mountaingoatsoftware.com/agile/user-stories)。通过练习 YAGNI，我们将看到代码是如何自然进化的，而不是天生的。这些代码样本是用 PHP 编写的，以纪念勒多夫先生。

## 第一个故事

> 作为营销人员，我想获得所有用户的列表，以便进行分析。
> *假设*我访问一个特定的 URL
> *然后*我应该得到一个 JSON 格式的所有用户的列表

第一个故事最具挑战性。这是我们的欲望最强烈的时候。记住，YAGNI 告诉我们只实现我们真正需要的东西。或者，换句话说，尽可能写最简单的东西。

下面是代码可能的样子。

```
<?php
$mysqli = new mysqli('localhost', 'dbuser', 'dbpass', 'dbname');

$query = 'SELECT * FROM users';
$result = $mysqli->query($query);
$users = $result->fetch_all(MYSQLI_ASSOC);

header('Content-Type: application/json');
echo json_encode(['users' => $users]); 
```

Enter fullscreen mode Exit fullscreen mode

诚然，这段代码并不优雅。这可能会让你在 Reddit 或 StackOverflow 上被投票。[务实的程序员](https://pragprog.com/book/tpp/the-pragmatic-programmer)讨论写作*“足够好的软件”*，这正是本文的内容。只有 7 行代码，不可否认的简单。任何初级程序员都可以轻松地使用这些代码。最重要的是，它完成了故事。仅此而已。没什么比这更糟的了(嗯，从技术上来说，我们可以将`users`表转储为 JSON 格式，并提供一个静态文件)。

## 第二个故事

> 作为合法用户，我不希望用户的密码出现在响应中，这样我们就不会被起诉。
> *假设*我访问用户列表
> *的 URL，那么*我应该得到 JSON 格式的所有用户列表
> *它不应该包括他们的密码*

 *那么我们能做的最简单的事情是什么呢？我们可以调整 SQL 查询，只选择适当的字段。

```
<?php
$mysqli = new mysqli('localhost', 'dbuser', 'dbpass', 'dbname');

$query = 'SELECT email, role FROM users';
$result = $mysqli->query($query);
$users = $result->fetch_all(MYSQLI_ASSOC);

header('Content-Type: application/json');
echo json_encode(['users' => $users]); 
```

Enter fullscreen mode Exit fullscreen mode

## 第三个故事

> *作为*营销人员，我想通过电子邮件地址过滤用户列表，这样我可以更容易地对用户数据进行分组。
> *假设*我访问用户列表的 URL
> *和*我设置一个“email”参数
> *然后*我应该得到一个 JSON 格式的所有用户的列表，这些用户的电子邮件地址包含“email”参数值

同样，让我们基于`email`参数调整 SQL 查询。

```
<?php
$mysqli = new mysqli('localhost', 'dbuser', 'dbpass', 'dbname');

$query = 'SELECT email, role FROM users';
if (!empty($_GET['email'])) {
    $query .= 'WHERE email LIKE \'%' . $_GET['email'] . '%\'';
}

$result = $mysqli->query($query);
$users = $result->fetch_all(MYSQLI_ASSOC);

header('Content-Type: application/json');
echo json_encode(['users' => $users]); 
```

Enter fullscreen mode Exit fullscreen mode

现在，虽然故事到此结束，但还有一些事情我们不应该继续忽视。

*   **复杂性增加**。`if`语句引入了新的代码逻辑分支。
*   **变化率**。最近的每个故事都需要更改 SQL 语句。
*   **安全风险**。添加的代码容易受到 SQL 注入的攻击。

这些原因应该指导我们重构代码。一个常见的误解是 YAGNI 和重构是对立的。我发现他们的关系更加共生。YAGNI 让我的代码保持简单，这使得重构变得容易。如果我的代码很容易重构，那么我可能会随时快速返工代码。这给了我继续编写简单代码和延续循环的信心。

所以，让我们重构来提取复杂性。在这一点上更加熟悉代码，一个*存储库*对象是一个明显的候选对象。此外，我们还将确保该存储库降低我们的 SQL 注入风险。

```
<?php
require 'vendor/autoload.php';

$mysqli = new mysqli('localhost', 'dbuser', 'dbpass', 'dbname');

$userRepository = new UserRepository($mysqli);
$users = $userRepository->getUsers($_GET['email']);

header('Content-Type: application/json');
echo json_encode(['users' => $users]); 
```

Enter fullscreen mode Exit fullscreen mode

## 第四个故事

> 作为营销人员，我希望看到对“角色”的描述，以便更好地理解数据。
> *给定*我访问用户列表的 URL
> *和*用户的角色是 1 或 2
> *然后*我应该看到“角色”的“会员”或“贵宾”(分别)

有多种实现方式，我很快想到了两种。我们可以在 SQL 语句中实现它，也可以用代码实现它。虽然更新 SQL 语句可以说是最简单的，但它极大地增加了复杂性。出于这个原因，我选择用代码来实现它。

```
<?php
require 'vendor/autoload.php';

$mysqli = new mysqli('localhost', 'dbuser', 'dbpass', 'dbname');

$userRepository = new UserRepository($mysqli);
$raw_users = $userRepository->getUsers($_GET['email']);

$users = [];
foreach ($raw_users as $user) {
    $users = [
        'email' => $user['email'],
        'role' => $user['role'] == 2 ? 'VIP' : 'Member'
    ];
}

header('Content-Type: application/json');
echo json_encode(['users' => $users]); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码可以工作，但是就像之前一样，它引入了新的代码分支和业务逻辑。这也使我们的线路数翻倍。因此，我将利用我节省下来的一些时间来编写这个简单的解决方案，看看是否有更简单的选择。确实有，PHP 有一个`JsonSerializable`接口，我们可以实现它来向`json_encode()`返回值。

我可以引入一个简单的实现了`JsonSerializable`的`User`对象，并将新的逻辑移到那里。我将更新`UserRepository`来返回一个`User`对象的数组。最终的代码与我开始这个故事时一样，证明了一次成功的重构。

```
<?php
require 'vendor/autoload.php';

$mysqli = new mysqli('localhost', 'dbuser', 'dbpass', 'dbname');

$userRepository = new UserRepository($mysqli);
$users = $userRepository->getUsers($_GET['email']);

header('Content-Type: application/json');
echo json_encode(['users' => $users]); 
```

Enter fullscreen mode Exit fullscreen mode

## 第五个故事

> 作为财务人员，我需要获得 XML 格式的用户列表，这样我们就可以将它导入到我们陈旧的系统中。
> *假设*我访问用户列表的 URL
> *和*我将“接受”头设置为“应用程序/xml”
> *然后*我应该得到 XML 格式的所有用户列表

您现在已经掌握了这一点，所以让我们添加一些`if`语句来改变响应。

```
<?php
require 'vendor/autoload.php';

$mysqli = new mysqli('localhost', 'dbuser', 'dbpass', 'dbname');

$userRepository = new UserRepository($mysqli);
$users = $userRepository->getUsers($_GET['email']);

if (strpos($_SERVER['HTTP_ACCEPT'], 'application/xml') !== false) {
    header('Content-Type: application/xml');
    // output the XML...
} else {
    header('Content-Type: application/json');
    echo json_encode(['users' => $users]);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以继续沿着这条路走下去，但是我们会重新引入代码分支。如果你用 [TDD](https://en.wikipedia.org/wiki/Test-driven_development) (另一个关键的 XP 原则)测试这段代码，你可能会在添加上下文来测试不同的代码路径时注意到这一点。所以我们利用这个机会介绍一个对象。

```
<?php
require 'vendor/autoload.php';

$mysqli = new mysqli('localhost', 'dbuser', 'dbpass', 'dbname');

$userRepository = new UserRepository($mysqli);
$users = $userRepository->getUsers($_GET['email']);

ContentNegotiator::sendResponse($user); 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们将用户数据传递给一个简单的内容协商器。它甚至可能是静态的，因为它没有状态，只是输出一个响应。

## 复古

让我们看看练习 YAGNI 给我们带来了什么:

*   **速度**。由于 YAGNI 让我们保持专注，我们很快就完成了 5 个故事。
*   **简约**。我们实际上减少了初始代码的大小。我们在需要的时候引入新的对象，提高它们的内聚力。
*   **无压力**。最后，我们毫无压力地实现了这一切。我们没有被架构或范围的担忧所困扰。

希望你能和我一样从练习 YAGNI 中得到同样的好处。

* * *

***想要更多？**在 Twitter 上关注 [@gonedark](https://twitter.com/gonedark) ，获得每周编码提示、足智多谋的转发和其他随机信息。**