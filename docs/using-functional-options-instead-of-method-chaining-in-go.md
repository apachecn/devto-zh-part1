# 在 Go 中使用函数选项代替方法链

> 原文：<https://dev.to/joncalhoun/using-functional-options-instead-of-method-chaining-in-go>

> *本帖原帖发布于[calhoun . io](https://www.calhoun.io/using-functional-options-instead-of-method-chaining-in-go/)T3】*

在 Java 中，使用方法链(也称为构建器模式)来构造资源的库是很常见的。例如，我们可以通过如下方式构造一个用户:

```
User user = new User.Builder()
  .name("Michael Scott")
  .email("michael@dundermifflin.com")
  .role("manager")
  .nickname("Best Boss")
  .build(); 
```

Enter fullscreen mode Exit fullscreen mode

由于各种原因，构建器很方便，但是在上面的例子中，我们使用构建器是为了在构建用户对象之前定义用户属性的子集。这在像 Java 这样的语言中非常方便，因为在这种语言中，您必须单独定义每个构造函数。

```
public class User {
  public User() {}
  public User(String name) {
    this.name = name
  }
  public User(String name, String email) {
    this.name = name
    this.email = email
  }
  // ... plus many more
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这个构造函数列表很快就会变得令人讨厌，因此使用了构造函数。

Go 没有这个问题，因为它有[结构文字](https://tour.golang.org/moretypes/5)。在构造时，我们可以很容易地定义一个类型属性的子集，如果没有什么新奇的事情发生，这使得构造器变得毫无用处。

当您需要在正在定义的字段之外设置额外的数据时，构建器模式确实会在 Go 中出现。例如，当使用任何 SQL 构建库时，通常将多个`WHERE`、`OR`、`NOT`和其他类似的语句链接在一起，以便创建完整的 SQL 查询。在这里使用 struct literals 等不会很好，因为调用每个函数的幕后都有很多复杂性，而且在调用所有链接的方法之前，不可能总是解决所有问题。

为了做到这一点，您的 Go 代码需要在每次方法调用后返回“builder”对象，否则链接根本不起作用。下面是 Go 中的一个简单例子。

注意:我并不提倡使用这种模式，这是一个非常不自然的例子，但是它的目的是说明方法链接是如何工作的。

```
package main

import "fmt"

func main() {
  ub := &UserBuilder{}
  user := ub.
    Name("Michael Scott").
    Role("manager").
    Build()
  fmt.Println(user)
}

type User struct {
  Name      string
  Role      string
  MinSalary int
  MaxSalary int
}

type UserBuilder struct {
  User
}

func (ub *UserBuilder) Build() User {
  return ub.User
}

func (ub *UserBuilder) Name(name string) *UserBuilder {
  ub.User.Name = name
  return ub
}

func (ub *UserBuilder) Role(role string) *UserBuilder {
  // verify the role is valid
  if role == "manager" {
    ub.User.MinSalary = 20000
    ub.User.MaxSalary = 60000
  }
  ub.User.Role = role
  return ub
} 
```

Enter fullscreen mode Exit fullscreen mode

*[在围棋操场上跑起来](https://play.golang.org/p/q_JCE9Qnxb)*

上面的例子很不自然，因为我们使用的是相对简单的资源，但是正如我之前所说的，情况并不总是这样。例如，像 [GORM](https://github.com/jinzhu/gorm) 和 [pop](https://github.com/markbates/pop) 这样的库都依赖于方法链来构建更复杂的 SQL 查询。

虽然这种模式在这些库中工作得足够好，但肯定有一些时候我们并不觉得是在按部就班地编写代码，我相信这通常源于错误处理的方式。

在像 Java 这样的语言中，builder 模式非常普遍，您可以从任何地方抛出异常。虽然乍一看这似乎无关紧要，但它最终意味着构建器链中的任何一个方法都可以选择抛出异常，但是如果没有异常，方法仍然可以简单地返回构建器类，这样方法链就可以继续。

在围棋中这是不可能的。在 Go 中通知最终用户错误的唯一方法是返回一个错误，但是当我们所有的构建器方法只返回构建器来允许方法链接时，这是不可能的。相反，我们必须推迟通知用户任何潜在的错误，直到过程的后期。

例如，如果我们要调用类似下面的查询(它缺少一个表示要查询的 ID 的参数)，我们不能在 Go 中立即得到通知。

```
db.Where("id = ?")... 
```

Enter fullscreen mode Exit fullscreen mode

正如我前面说过的，在 Java 中我们只是抛出一个异常。处理这个问题的最好方法是返回一个错误，但是通过引入第二个返回变量(错误),我们最终得到的代码不再支持方法链接。

```
var db *gorm.DB
var err error
db, err = db.Where("id = ?", 123)
if err != nil { ... }
db, err = db.Where("email = ?", "jon@calhoun.io")
if err != nil { ... }
// ... and so on 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这不是一个非常有趣的 API。

现在，如果你*真的*想使用方法链接，有几种方法可以返回错误。例如，GORM 通过在`*gorm.DB`类型上附加一个`Error`字段来解决这个问题，只要方法链中有错误，这个字段就会被设置。通过这样做，GORM 现在可以在一个错误发生时设置一个错误，然后后续的方法调用可以提前终止，因为已经有一个错误了。它并不完美，但它是可行的，并且您最终会得到如下代码。

```
// ignore this error for a simpler example
db, _ := gorm.Open(...)
var user User
err := db.Where("email = ?", "jon@calhoun.io").
  Where("age >= ?", 18).
  First(&user).
  Error 
```

Enter fullscreen mode Exit fullscreen mode

虽然这样做是可行的，但是这段代码中的一些东西使它不是最理想的。

首先，我们调用的方法(`Where`和`First`)都不会返回错误，所以我们甚至不知道是否需要担心错误。

这段代码的第二个问题是很容易写出错误的代码并遗漏错误。例如，如果我们改为编写下面的代码，我们将*总是*得到`nil`的错误，因为我们实际上并没有检查`gorm.DB`的正确实例。

```
// ignore this error for a simpler example
db, _ := gorm.Open(...)
var user User
db.Where("email = ?", "jon@calhoun.io").
  Where("age >= ?", 18).
  First(&user)
// db.Error will ALWAYS be nil!
if db.Error != nil {
  // Handle the error
} 
```

Enter fullscreen mode Exit fullscreen mode

为了让这种模式工作，我们要么在链的末端调用`Error`，要么需要捕获由`First`方法返回的结果`gorm.DB`实例，这样我们就可以检查它是否有错误。

这是 GORM 有意为之的，因为如果您在使用进行任何查询之前必须手动克隆您的`gorm.DB`实例，那么这个库将更加难以使用，所以相反，GORM 会在您每次调用像`Where`或`First`这样的链接方法时处理克隆。最终的结果是您编写的代码更少，但是如果您没有完全理解这一点，就更容易遗漏错误。

*注意:解决这个问题的一个方法是更新 GORM 中的`First`和`Find`等方法，以便在被调用时返回`gorm.DB`和`error`，但是我不能 100%确定这将如何影响库的其余部分。相反，我们将探索一种我更喜欢的替代方法

## 一种替代方法-功能选项

我发现使用[函数选项](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)比使用方法链接要容易得多，这个术语我第一次听到是由戴夫·切尼创造的(在链接的博客文章中)。

在高层次上，函数选项基本上只是碰巧是函数的方法或函数调用的参数。它们可能是动态创建的闭包，也可能是静态函数；这基本上是不相关的。重要的是，我们不是传入数据，而是传入函数，这些函数将执行一些工作来实现您想要的结果。

实际上，在 GORM 这样的案例中演示函数选项的好处要容易得多，所以让我们继续这样做，看看我们能否创建一个更友好的 API。

注意:我不是在抨击 GORM，事实上我是这个包的忠实粉丝。我甚至不确定整个包是否可以被重写以使用功能选项，所以请不要认为这意味着这个包不好。

### 重新创建 GORM 的子集以演示功能选项

我们不会重写 GORM(这会花很长时间)，而是将重点放在一个方法上——`First`方法。为了重写这一点，我们甚至不打算改变 GORM，而是要在现有的 GORM 包之上创建一个包装器，它将演示我的观点，而不需要深究 GORM 的具体细节。这些代码足以说明方法链和函数选项之间的区别，同时也展示了我们的新版本作为 API 消费者使用起来有多简单。

我们需要的第一件事是定义选项的类型。从技术上来说，我们不需要*T4 这样做，但是我倾向于发现这让我的代码更容易阅读、编写和理解。我们将把它命名为`QueryOption`，它将是一个接受`*gorm.DB`并返回`*gorm.DB`和错误的函数。这样，当我们的查询选项遇到错误时就很清楚了。* 

```
type QueryOption func(db *gorm.DB) (*gorm.DB, error) 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们需要一种定义`QueryOption`的方法。我现在只介绍 GORM 中的`Where`方法，但是你可以用同样的方式介绍它的任何方法。

```
func Where(query interface{}, args ...interface{}) QueryOption {
  return func(db *gorm.DB) (*gorm.DB, error) {
    ret := db.Where(query, args)
    return ret, ret.Error
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我们接受一个查询和 args(这部分是从 GORM 的源代码中复制的[，然后我们将返回一个`QueryOption`。然后我们继续创建我们想要返回的闭包，它将在调用 GORM 的`Where`方法时使用所提供的参数，并将捕获结果的`*gorm.DB`。最后，这将返回`*gorm.DB`以及分配给它的`Error`字段的任何内容。](https://github.com/jinzhu/gorm/blob/master/main.go#L172)

在这一点上，代码看起来并不清楚，但是请耐心听我说。现在我们本质上是在重写 GORM，直到我们开始使用新的 API，简单性才会到来。

接下来是`First`方法。我们需要一种查询方式，我们将把这些功能选项(我们刚刚创建的`Where`方法)传递给我们新的`First`方法。在此期间，我们将创建一个新的`DB`类型来包装`*gorm.DB`类型，这样我们就可以定义这个新方法。

```
// gorm.DB wrapper
type DB struct {
  *gorm.DB
}

func (db *DB) First(out interface{}, opts ...QueryOption) error {
  // Get the GORM DB
  gdb := db.DB
  var err error = nil
  // Apply all the options
  for _, opt := range opts {
    gdb, err = opt(gdb)
    if err != nil {
      return err
    }
  }
  // Execute the `First` method and check for errors
  if err := gdb.First(out).Error; err != nil {
    return err
  }
  return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码主要是获取`*gorm.DB`实例，然后遍历提供的每个 QueryOption，将它应用到 gorm DB，并捕获更新后的值和过程中出现的任何错误。如果确实发生了错误，继续下去就没有什么意义了，所以我们的`First`方法会立即返回错误。否则，我们的代码最终将使用`out`变量(目标对象，类似于[GORM 的第一个方法如何工作](https://github.com/jinzhu/gorm/blob/master/main.go#L267))调用 GORM 的`First`方法，并返回遇到的任何错误。

我们现在已经准备好看到这一点，并测试这是否真的更友好。

### 使用带有功能选项的 GORM 包装器

现在我们有了更新的代码，我们需要看一个如何使用它的例子。我们将使用一个虚构的`User`类型来查询我们的数据库，如果您想在本地运行它，您可以通过获取最终的源代码(链接在代码下面)并调整数据库连接字符串来实现。

```
var user User
err = db.First(&user,
  Where("email = ?", "jon@calhoun.io"),
  Where("id = ?", 2),
)
if err != nil {
  panic(err)
}
fmt.Println(user) 
```

Enter fullscreen mode Exit fullscreen mode

为了使用我们的 GORM 包装器，我们必须使用一个真实的数据库。这意味着我们的代码不会在 Go Playground 上运行，但我仍然会包含一个链接，指向代码的完整副本，供您参考-[https://play.golang.org/p/3alDpVkPp6](https://play.golang.org/p/3alDpVkPp6)T3】

虽然使用我们的新 API 并没有减少要使用的代码，但我发现这段代码更容易使用，并且有助于明确用户何时需要检查错误。我们不需要担心用户了解了`Error`字段，或者在这样做之前忘记捕获更新的`*gorm.DB`实例。如果在执行过程中出现错误，我们创建的`First`方法将会返回错误。太棒了。

## 你喜欢这篇文章吗？加入我的邮件列表！

如果你喜欢这篇文章，请考虑将[加入我的邮件列表](https://www.calhoun.io/using-functional-options-instead-of-method-chaining-in-go/#subscribe)。

我大概每周会给你发一封邮件，让你知道我最近正在写的或者已经发表的新文章或者视频(比如这篇)。没有垃圾邮件。不要出售你的电子邮件。我会像对待自己的收件箱一样对待你的收件箱。

作为对您加入的特别感谢，我还将向您发送我的课程 [Web Development with Go](https://www.usegolang.com) 的截屏和电子书样本。