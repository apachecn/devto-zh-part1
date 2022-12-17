# 不要写无用的 unit 测试

> 原文：<https://dev.to/phpprofi/---unit--el1>

[![PhpUnit](img/3c1e019fb87e28eba35c3b9eaf906db0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--STFrbI_r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://phpprofi.ru/resources/img/blogs/e9949af5-e0c8-4739-ad2b-955b6f1ef41e.jpeg)

前几天，我在项目中遇到了以下代码:T0

```
class Users
{
    public function __construct(PDO $pdo)
    {
        $this->pdo = $pdo;
    }

    public function getAllUsers()
    {
        $stmt = $this->pdo->prepare('SELECT * FROM users');
        return $stmt->fetchAll();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是测试这个代码的方法:t0 _ _ _ _ _ _ _ _ _ _ _ _ _ _ _)

```
class UserTest extends TestCase
{
    public function testGetAllUsers()
    {
        $pdo = m::mock(PDO::class);
        $stmt = m::mock(PDOStatement::class);

        $pdo->shouldReceive(‘prepare’)->andReturn($stmt);
        $pdoStmt->shouldReceive(‘fetchAll’)->andReturn($userArray);

        $users = new Users($pdo);
        $result = $users->getAllUsers();

        $this->assertEquals($userArray, $users);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我省略了 User 类的其余部分以及测试中返回的用户数组。t1￥1

这个测试实际上在 T0 方法中给了我们 100%的代码复盖率。但不幸的是，对于任何实际目的，这个测试都完全没有用。

* * *

http://phpprofi.ru/blogs/post/94 的читать

* * *

en:[https://www . brandonsavage . net/don-write-used-unit-tests/](https://www.brandonsavage.net/dont-write-useless-unit-tests/)