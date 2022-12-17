# 适配器设计模式

> 原文：<https://dev.to/sarfraznawaz2005/adapter-design-pattern-4lhh>

## 概述

顾名思义，适配器设计模式就像现实生活中的适配器一样，将一种东西转换成另一种东西。类似地，它可以帮助我们将一些对象功能包装到适配器中，为我们提供一致的方法/API。通过例子来理解永远是最好的学习方式。

## 举例

假设您开发了一个网站，客户要求您允许用户在脸书上发布状态更新。为了做到这一点，我们使用脸书 PHP 客户端库，它看起来像这样:

```
class Facebook {    
    public function getUserToken($userId) {
        // code to get user token
    }

    public function postUpdate($message) {
        // code to post status update
    }
} 
```

然后你用它来更新脸书:

```
$statusUpdate = new Facebook;
$statusUpdate->getUserToken($someUserId);
$statusUpdate->postUpdate('some message'); 
```

现在让我们假设客户端突然要求用户在 Twitter 上发布更新，而不是脸书，Twitter 的 PHP 客户端库如下所示:

```
class Twitter {    
    public function checkUserToken($userId) {
        // code to get user token
    }

    public function setStatusUpdate($message) {
        // code to post status update
    }
} 
```

这就是问题所在。我们可以看到 Twitter 的库有不同的方法名，这意味着我们将不得不在使用状态更新的任何地方修改代码。在这一点上，作为开发人员，我们也不应该否决这样的事实，即客户端可能会再次要求使用脸书，或者可能是其他一些服务，以允许用户发布状态更新，但我们不想一次又一次地改变我们的代码。

那么，我们如何确保:

*   当客户要求时，我们应该能够轻松地添加任何新的服务
*   我们应该能够不修改代码一次又一次，同样的代码应该发布状态更新，而不修改它

这就是适配器模式作为救生器出现的地方。为了实现这一点，我们首先设置所有状态更新服务应该遵循的规则/接口，所以让我们创建一个:

```
interface iStatusUpdate {
    function getUserToken($userId);
    function postUpdate($message);
} 
```

现在我们创建我们的 Twitter 适配器类:

```
class TwitterAdapter implements iStatusUpdate {

    protected $twitter;

    public function __construct(Twitter $twitter){
        $this->twitter = $twitter;
    }

    public function getUserToken($userId) {
        $this->twitter->checkUserToken($userId);
    }

    public function postUpdate($message) {
        $this->twitter->setStatusUpdate($message);
    }
} 
```

注意我们是如何通过构造函数传递`Twitter`对象的。当然，我们也可以使用 setter 或接口注入。

我们可以看到，我们现在对脸书和 Twitter 使用了相同的方法名，这意味着我们不必修改太多代码，因为我们需要为用户发布状态更新。现在我们需要做的就是使用新的适配器类，为它提供实际的 Twitter 对象:

```
$statusUpdate = new TwitterAdapter(new Twitter);
$statusUpdate->getUserToken($someUserId);
$statusUpdate->postUpdate('some message'); 
```

由于适配器模式，我们可以添加一行或几行代码，但大多数状态更新代码保持不变。作为开发人员，我们应该预见到客户可能需要的这种变化，在这种情况下，我们甚至可以为我们的第一个脸书服务创建适配器类，然后我们甚至不需要修改一行代码。

好的，客户端要求使用其他服务，没问题:

```
class SomeOtherServiceAdapter implements iStatusUpdate {

    protected $otherService;

    public function __construct(SomeOtherService $otherService){
        $this->otherService = $otherService;
    }

    public function getUserToken($userId) {
        $this->otherService->authenticate($userId);
    }

    public function postUpdate($message) {
        $this->otherService->postMessage($message);
    }
} 
```

然后:

```
$statusUpdate = new SomeOtherServiceAdapter(new SomeOtherService);
$statusUpdate->getUserToken($someUserId);
$statusUpdate->postUpdate('some message'); 
```

同样，大部分代码保持不变。

所以这些简单的例子显示了适配器模式是多么有用和省时。它为我们提供了一致的 API，并允许我们轻松地使用新服务。

[![](img/bffdc911e052c06da93154ccbd125799.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KZ0yqiEq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/LI4t6NsNRCo)