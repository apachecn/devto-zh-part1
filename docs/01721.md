# Laravel 5.5，测试，你呢

> 原文：<https://dev.to/edmilsonrobson/laravel-55-testing-and-you-8hl>

如果你是一个程序员，很可能你喜欢写代码。这很棒，让你感觉棒极了(至少在它工作的时候！).每一行代码都转化为你当前任务的更多进展。然而，同样的想法可能会让你放弃构建高质量软件的一个非常重要的步骤:编写测试。

作为一个 Laravel 的粉丝(如果你使用 PHP 并且从未听说过它，认真地去看看)，我不禁注意到用它来编写开箱即用的测试是多么的棒。然而，我们需要负责任地去做:保持项目代码的高质量意味着你的测试代码也应该是好的。

考虑到这一点以及 Laravel 5.5 附带的一些新技巧，我决定编写一些关于如何提高测试代码质量的技巧，以帮助您保持项目无 bug 且出色。所以让我们开始吧！

## 1。使用 setUp()和 tearDown()

我怎么强调都不为过。我们都知道重复的代码(通常)是糟糕的代码，这当然也适用于测试代码。让我们举一个简单的例子来测试一个社交网络应用:

```
<?php

class ExampleTest extends TestCase {
use DatabaseTransactions;

    /** @test */
    public function users_can_follow_each_other() {
        $user1 = factory(User::class)->create();
        $user2 = factory(User::class)->create();

        $this->actingAs($user1);

        $this->post('/follow', [
            'user_id' => $user2->id
        ]);

        $this->assertTrue($user2->isFollowedBy($user1));
    }

    /** @test */
    public function users_can_message_each_other() {
        $user1 = factory(User::class)->create();
        $user2 = factory(User::class)->create();

        $this->actingAs($user1);

        $this->post('/messages', [
            'user_id' => $user2->id
        ]);

        $this->assertDatabaseHas('messages', [
            'from' => $user1->id,
            'to' => $user2->id
        ]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们每次测试都在创造新的用户。我们可以设置两个并重复使用它们。而拯救我们的`setUp()`方法来了:

```
<?php

class ExampleTest extends TestCase {
    use DatabaseTransactions;

    private $user1;
    private $user2;

    protected function setUp() {
        parent::setUp(); // this is important!

        $this->user1 = factory(User::class)->create();
        $this->user2 = factory(User::class)->create();
        $this->actingAs($this->user1);
    }

    /** @test */
    public function users_can_follow_each_other() {
        $this->post('/follow', [
            'user_id' => $this->user2->id
        ]);

        $this->assertTrue($this->user2->isFollowedBy($this->user1));
    }

    /** @test */
    public function users_can_message_each_other() {
        $this->post('/messages', [
            'user_id' => $this->user2->id
        ]);

        $this->assertDatabaseHas('messages', [
            'from' => $this->user1->id,
            'to' => $this->user2->id
        ]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意现在的测试有多好，多切题。我们所要做的就是覆盖`setUp()`方法，并在那里创建我们的用户，然后将他们指定为类属性。我们甚至在那里抛弃了`actingAs`方法，因为它对那个测试类更有意义。只是不要忘记在`setUp()`方法的开头加上`parent::setUp()`。

(专业提示:如果你正在使用 PhpStorm，点击`CTRL+O`快速覆盖方法)。

现在，每当这个类中的任何测试运行时，`setUp()`方法将被首先调用，给我们新的用户来玩。非常好。你能想象如果我们有十几个使用用户或者需要更复杂设置的方法吗？这将在重构或增加测试代码时节省大量时间。

也可以以同样的方式使用`tearDown()`方法，但是它在测试结束后立即运行。例如，如果您想要撤消某项操作，这将非常有用。

## 2。对时间敏感的场景使用碳

[碳牛逼](http://carbon.nesbot.com/docs/)。用 PHP 的原生类处理时间可能有点烦人，但是 Carbon 为我们提供了一个很好的、直观的语法来满足所有的日期/时间需求。

如果您有对时间敏感的场景(例如，如果用户出生 10 天，您希望有不同的行为)，测试可能听起来很难。一种普通的方法是在您试图测试的特定方法中注入当前时间，但是这很烦人。

幸运的是，Carbon 提供了一个很酷的方法来模仿它的`now()`方法，本质上是为你的测试伪造当前时间！

```
<?php

/** @test */
    public function only_10_days_old_users_can_upvote_a_post() {
        $user = factory(User::class)->create();
        $post = factory(Post::class)->create();

        $result = $user->likePost($post);
        $this->assertFalse($result);

        $tenDaysInTheFuture = Carbon::now()->addDays(10);
        Carbon::setTestNow($tenDaysInTheFuture);

        $result = $user->likePost($post);
        $this->assertTrue($result);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这里的技巧是通过传递期望的日期来使用`Carbon::setTestNow`进行模拟(这也是一个碳对象！).

**一个非常重要的警告:**这也将模拟所有未来碳物体的当前时间，当你在一个批处理中运行所有测试时，这可能会导致问题。为了避免这种情况，使用技巧 1，用`tearDown()`方法:
来“取消”模拟

```
<?php

protected function tearDown() {
        parent::tearDown();

        Carbon::setTestNow(); // this will clear the mock!
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 3。聪明对待你的断言

尽量让你的断言语义化。它们以不同的形式出现是有原因的，当您的测试不可避免地失败时，它们会给出更好的失败消息。

当然，你可以总是使用`$this->assertTrue`，但是这会降低你的测试的可读性。我们来重构一个例子:

```
<?php

$group->addUsers([$user1, $user2]);
        $group->save();

        $this->assertTrue($group->users->count() === 2); 
```

Enter fullscreen mode Exit fullscreen mode

当然这是可行的，但是为什么不能用一种更加语义化的方法来处理`$this->assertCount`甚至`$this->assertContains`？

```
<?php

$group->addUsers([$user1, $user2]);
        $group->save();

        $this->assertCount(2, $group->users->toArray()); 
```

Enter fullscreen mode Exit fullscreen mode

那更好。

记住你有许多断言，所以试着为你的测试场景选择一个最好的。想看看数据库中是否记录了某些东西？`$this->assertDatabaseHas()`和`$this->assertDatabaseMissing()`是你的朋友。

另外，请记住，当您测试 HTTP 路由时(例如，使用`$this->post`)，它将返回一个结果对象。那个对象也有自己的断言，像`$response->assertRedirect`和`$response->assertSessionHasErrors`。

最后一个提示:避免复杂的逻辑断言！千万不要断言`$this->assertTrue(!$condition)`。那基本上就是在说“我们断言它是真的，而`$condition`是假的”。用`$this->assertFalse($condition)`读起来好多了。你的团队会感激:)

## 4。例外:处理它们(或者不处理)

在 5.4 之前，Laravel 在测试中的异常处理上有一点问题——它们被忽略了，以我的经验来看，这导致了太多的假阴性测试。好在 Laravel 5.5 有了解决这个问题的新招。

有时候你想让 PHP 在你的代码中抛出异常，有时候你不想。当您试图测试中间件时，比如说，需要您登录的路由，但是您试图以访客身份强行进入，您可能希望使用`$this->expectException`来检查是否抛出了 AuthenticationException。

```
<?php

/** @test */
    public function guest_cant_see_skills() {
        $this->expectException(AuthenticationException::class);

        $response = $this->get('/skills');
        $response->assertRedirect('/login');
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 Laravel 5.5 中，这将返回`Failed asserting that exception of type "Illuminate\Auth\AuthenticationException" is thrown`，因此，失败。发生这种情况是因为 laravel 在幕后自动“处理”异常，并且从不在我们的测试中抛出它。为了自己处理这个问题，我们需要使用 L5.5 的一个新方法:

```
<?php

    /** @test */
    public function guest_cant_see_skills() {
        $this->expectException(AuthenticationException::class);
        $this->withoutExceptionHandling(); // <-- this!

        $response = $this->get('/skills');
        $response->assertRedirect('/login');
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在它会运行得很好。您还可以使用$this->withExceptionHandling()随时重新启用异常处理。

## 5。隐藏的验证错误是一个问题

每当您在带验证的路由上运行 HTTP 测试时，请确保正确断言响应无误。

```
<?php

/** @test */
    public function guest_cant_see_skills() {
        $user = factory(User::class)->create();
        $this->actingAs($user);

        $response = $this->post('/skills', [
            'name' => 'PHP',
            'proficiency' => 20,
        ]);

        $response->assertSessionMissing('errors');

        $this->assertTrue($user->hasSkill('PHP'));
    } 
```

Enter fullscreen mode Exit fullscreen mode

这样做可能听起来很愚蠢，特别是因为您知道您正在将正确的请求传递到您的 POST 路由，但是请相信我:一旦需求发生变化，您的路由验证规则也发生变化，您可能会盯着您的屏幕查看您的测试，并试图理解为什么它们不工作(我知道我过去曾经这样做过...)当最后只是因为您的 post 请求不再有效时(假设您在表单中添加了另一个必填字段)。

我确实想知道为什么 laravel 有一个用于 HTTP 请求的`assertSessionHasErrors()`方法，而没有一个`assertSessionDoesntHaveErrors()`。有比使用`assertSessionMissing('errors')`更好的主意吗？请在评论中与我们分享:)

## 6。嘲笑是你的朋友

如果你正在使用 Laravel 的内置事件、通知和邮件解决方案，测试它们再简单不过了。Laravel 的正面有助手，开箱即用，所以你不用去招惹嘲弄。

你所需要做的就是用`fake()`方法初始化(可能在你的`setUp()`中)，任何邮件、通知或事件调用都将是“伪造的”,你实际上可以断言新的模拟！

这里有一个使用通知的简单例子:

```
<?php

/** @test */
    public function guest_cant_see_skills() {
        Notification::fake();

        $user = factory(User::class)->create();
        $this->actingAs($user);

        $response = $this->post('/skills', [
            'name' => 'Laravel',
            'proficiency' => 85,
        ]);

        $response->assertSessionMissing('errors');
        $mentor = $user->mentor;

        Notification::assertSentTo([$mentor], StudentLearnedNewSkill::class);
    } 
```

Enter fullscreen mode Exit fullscreen mode

Laravel 在它的文档中有一个所有可用模拟的列表，所以去看看吧！

好了，暂时就这样了。希望你学到了新东西:)

你还有什么有用的建议可以分享，或者有什么想法可以改进我提到的话题吗？请在评论中告诉我——并在 [Twitter](//twitter.com/edmilson_rocha_) 上关注我；)