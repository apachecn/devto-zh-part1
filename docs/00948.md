# 事件驱动的 Laravel 应用程序

> 原文：<https://dev.to/mezie/event-driven-laravel-applications-12a6>

在这篇文章中，我们将看看什么是事件驱动编程，以及如何开始在 Laravel 中构建事件驱动的应用程序。我们还将看看如何通过使用事件驱动编程来分离应用程序的各个方面。

作为一个提示，本文假设您对 Laravel 事件有一定的了解，并且不会涉及基础知识。如果有必要的话，你可能想去参观一下 [Laravel Events](https://laravel.com/docs/5.5/events) 。

## 什么是事件驱动应用？

在我们深入了解什么是事件驱动的应用程序之前，让我们先来定义一下什么是事件驱动编程。根据维基百科:

> 事件驱动编程是一种编程范式，其中程序的流程由用户操作(鼠标点击、按键)、传感器输出或来自其他程序/线程的消息等事件决定。事件驱动编程是在图形用户界面和其他应用程序(例如，JavaScript web 应用程序)中使用的主要范例，其集中于响应于用户输入执行某些动作。
> 
> [事件驱动编程](https://en.wikipedia.org/wiki/Event-driven_programming)

根据上面的定义，事件驱动的应用程序是一个主要响应用户或应用程序生成的动作的应用程序。然后，应用程序执行其他代码来响应这些动作。

## 什么是 Laravel 事件？

简单地说，事件是发生在应用程序中的动作。来自 JavaScript 世界的您可能会将事件理解为用户动作，比如鼠标点击、鼠标悬停、按键等等。在 Laravel 中，事件可以是应用程序中发生的各种动作，如电子邮件通知、日志、用户注册、CRUD 操作等。Laravel 事件提供了一个简单的 observer 实现，允许您订阅和监听应用程序中发生的各种事件。

当某些活动发生时，框架(Laravel)会自动触发其中的一些事件。例如，当在一个雄辩模型上执行创建、保存、更新或删除操作时，Laravel 将分别触发`created`、`saved`、`updated`和`deleted`事件。我们可以挂钩这些事件，并在应用程序中执行其他活动。除了 Laravel 自动触发的那些事件之外，您还可以触发自己的定制事件来适应您的应用程序。例如，您可以触发一个`userRegistered`事件，向注册您的应用程序的用户发送一封欢迎邮件。

激发事件本身不会做任何事情；我们必须监听已经触发的事件，并做出相应的响应。Laravel 事件由两部分组成:事件处理程序和事件监听器。事件处理程序包含与触发的事件相关联的信息。另一方面，事件侦听器只是侦听事件实例，并相应地对其做出响应。在侦听器中，我们将实现事件实现逻辑。事件类通常存储在`app/Events`目录中，而它们的侦听器存储在`app/Listeners`中。

## 为什么是事件驱动的 Laravel 应用？

您已经了解了什么是事件驱动的应用程序和 Laravel 事件，并且您想知道为什么您应该使用这种事件驱动的方法来构建您的应用程序。好吧，让我们来看看它的一些好处。

首先，事件是分离应用程序各个方面的好方法，因为一个事件可以有多个相互不依赖的侦听器。通过解耦，您不会因为不一定符合领域逻辑的代码而污染您的代码库。其次，因为您的应用程序是松散耦合的，所以您可以轻松地扩展应用程序的功能，而无需中断/重写应用程序或至少应用程序的一些其他功能。

## 实际实施

我们能看看一些代码吗😎。假设我们有一个应用程序，我们希望向新注册的用户发送欢迎电子邮件，同时为他们注册每周简讯。通常，我们可能会使用类似于下面的代码片段来实现这一点:

```
// without event-driven approach

public function register(Request $request)
{
    // validate input
    $this->validate($request->all(), [
      'name' => 'required',
      'email' => 'required|unique:users',
      'password' => 'required|min:6|confirmed',
    ]);

    // create user and persist in database
    $user = $this->create($request->all());

    // send welcome email
    Mail::to($user)->send(new WelcomeToSiteName($user));

    // Sign user up for weekly newsletter
    Newsletter::subscribe($user->email, [
      'FNAME': $user->fname,
      'LNAME': $user->lname
    ], 'SiteName Weekly');

    // login newly registered user
    $this->guard()->login($user);

    return redirect('/home');
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，发送欢迎邮件和时事通讯注册与`register`方法紧密耦合。根据关注点分离的原则，`register`方法是否应该关注发送欢迎电子邮件甚至时事通讯注册的实际实现？虽然你可能会想，它只是发送邮件和时事通讯注册，这是很小的，在`register`方法中很好。如果除了发送邮件，您还想发送短信呢？您可能会得到如下结果:

```
// without event-driven approach

public function register(Request $request)
{
    // validate input

    // create user and persist in database

    // send welcome email
    Mail::to($user)->send(new WelcomeToSiteName($user));

    // send SMS
    Nexmo::message()->send([
      'to' => $user->phone_number,
      'from' => 'SiteName',
      'text' => 'Welcome and thanks for signup on SiteName.'
    ]);

    // Sign user up for weekly newsletter
    Newsletter::subscribe($user->email, [
      'FNAME': $user->fname,
      'LNAME': $user->lname
    ], 'SiteName Weekly');

    // login newly registered user

    return redirect('/home');
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到代码库是多么容易变得臃肿。但是我们如何解决这个问题呢？事件驱动编程拯救世界！因此，让我们看看使用这种方法实现与上面相同的功能时，代码会是什么样子。

```
// with event-driven approach

public function register(Request $request)
{
    // validate input
    $this->validate($request->all(), [
      'name' => 'required',
      'email' => 'required|unique:users',
      'password' => 'required|min:6|confirmed',
    ]);

    // create user and persist in database
    $user = $this->create($request->all());

    // fire event once user has been created
    event(new UserRegistered($user));

    // login newly registered user
    $this->guard()->login($user);

    return redirect('/home');
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，一旦创建了用户，就会触发`UserRegistered`事件。回想一下，我们之前提到过，激发一个事件本身不会做任何事情；我们需要监听`UserRegistered`事件并采取必要的行动。让我们首先创建`UserRegistered`事件以及`SendWelcomeMail`和`SignupForWeeklyNewsletter`监听器:

```
artisan make:event UserRegistered
artisan make:listener SendWelcomeMail --event=UserRegistered
artisan make:listener SignupForWeeklyNewsletter --event=UserRegistered 
```

Enter fullscreen mode Exit fullscreen mode

打开`app/Events/UserRegistered.php`并更新构造函数，如下所示:

```
// app/Events/UserRegistered.php

public $user;

public function __construct(User $user)
{
  $this->user = $user;
} 
```

Enter fullscreen mode Exit fullscreen mode

通过声明`$user`变量`public`，它将被传递给监听器，然后监听器可以对它做任何必要的事情。接下来，事件侦听器将在它们的`handle`方法中接收事件实例。在`handle`方法中，我们可以执行任何必要的动作来响应事件:

```
// app/Listeners/SendWelcomeMail.php

public function handle(UserRegistered $event)
{
  // send welcome email
  Mail::to($event->user)->send(new WelcomeToSiteName($event->user));
}

// app/Listeners/SignupForWeeklyNewsletter.php

public function handle(UserRegistered $event)
{
  // Sign user up for weekly newsletter
  Newsletter::subscribe($event->user->email, [
    'FNAME': $event->user->fname,
    'LNAME': $event->user->lname
  ], 'SiteName Weekly');
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到我们如何能够得到松散耦合的代码，并尽可能保持我们的`register`方法代码最少。现在，假设我们要向新注册的用户发送短信。我们所要做的就是创建一个新的事件监听器来监听何时触发`UserRegistered`事件:

```
artisan make:listener SendWelcomeSMS --event=UserRegistered 
```

Enter fullscreen mode Exit fullscreen mode

并将 SMS 发送实现放在`SendWelcomeSMS`的`handle`方法中:

```
// app/Listeners/SendWelcomeSMS.php

public function handle(UserRegistered $event)
{
  // send SMS
  Nexmo::message()->send([
    'to' => $event->user->phone_number,
    'from' => 'SiteName',
    'text' => 'Welcome and thanks for signup on SiteName.'
  ]);
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。请注意，没有必要对`register`方法进行修改或添加任何东西。

## 结论

在这篇文章中，我们已经能够理解什么是事件驱动编程，什么是事件驱动应用程序，什么是 Laravel 事件。我们还研究了事件驱动应用程序的可能优势。但是就像几乎所有有正面影响的编程概念一样，它也有负面影响。事件驱动应用程序的主要缺点是很难真正理解应用程序的流程。以我们上面的实现为例，某个人(比如一个新的开发人员)通过`register`方法可能不知道我们正在给新用户发送一封欢迎邮件，并且还让他们签署了一份时事通讯。

所以我认为你应该在你的 Laravel 应用程序中尽可能创造性地使用事件驱动的方法，不要过度。一般来说，我认为事件驱动的 Laravel 应用程序很酷😎。你怎么想呢?让我们在下面的评论中分享你的想法。

这篇文章最初由作者发布在 [Pusher 博客](https://blog.pusher.com/event-driven-laravel-applications)上。