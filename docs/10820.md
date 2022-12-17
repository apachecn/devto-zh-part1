# 可怜的，被误解的装饰工

> 原文：<https://dev.to/oddlyfunctional/the-poor-misunderstood-decorator>

这是我的一个老问题:我一次又一次地看到人们无法理解什么是室内设计师。我没有任何数据来支持我，但是我可以说这是最容易被误解的设计模式。我看到它被几个社区滥用，包括我心爱的 Ruby，不仅改变了它的规范，而且完全违背了它的目的。通常的“装饰者”是这样的:

```
class UserDecorator
  def initialize(user)
    @user = user
  end

  def email
    @user.email
  end

  def full_name
    "#{@user.first_name}  #{@user.last_name}"
  end

  def full_address
    "#{@user.address.number}  #{@user.address.street}, #{@user.address.city}, #{@user.address.state}"
  end
end

User = Struct.new(:first_name, :last_name, :email, :address)
Address = Struct.new(:number, :street, :city, :state)

user_decorator =  UserDecorator.new(
                    User.new(
                      "Oddly",
                      "Functional",
                      "hi@oddlyfunctional.com",
                      Address.new("123", "St. Nowhere", "New York", "NY")
                    )
                  )

user_decorator.email
# => "hi@oddlyfunctional.com" 

user_decorator.full_name
# => "Oddly Functional" 

user_decorator.full_address
# => "123 St. Nowhere, New York, NY" 
```

众所周知，装饰器是一种表示组件，它包装模型实例并公开用于表示目的的适当方法(例如，格式化完整地址或完整名称，同时将不会更改的方法委托给包装的实例，就像电子邮件的情况一样)。我有点不耐烦地说，这是*不是*室内装潢师。你可以称它为演示者或其他什么，但它的目标和用途与装饰者完全不同。这种误解被社区强化为珍宝(是的，我正看着你，[德雷柏](https://github.com/drapergem/draper)ðÿ'€)，并导致越来越少的开发人员知道装饰者到底是什么。

但到底是什么呢？

对于正式的定义，你可以查看四人组的原始[设计模式](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented-ebook/dp/B000SEIBB8)书，但简单地说，装饰器是一个类，它包装一个实例并实现该实例通用的定义良好的接口，以便以可组合的方式动态透明地向包装的实例添加行为。“哈哈，你看起来像我的老师，说起来容易做起来难”你一定在想。其实真的很简单实用。系好安全带，我要给你看一些代码！

```
# I'm using Forwardable, a standard lib module, to make it easier to delegate
# methods that don't change to their original implementation.
# Check its documentation at:
# http://ruby-doc.org/stdlib-2.3.1/libdoc/forwardable/rdoc/Forwardable.html
require 'forwardable'

class UserContactEmailDecorator
  extend Forwardable
  def_delegators :@user, :first_name, :last_name

  def initialize(user)
    @user = user
  end

  def email
    "#{full_name} <#{@user.email}>"
  end

  private

    def full_name
      "#{@user.first_name}  #{@user.last_name}"
    end
end

class UserUppercaseNamesDecorator
  extend Forwardable
  def_delegators :@user, :email

  def initialize(user)
    @user = user
  end

  def first_name
    @user.first_name.upcase
  end

  def last_name
    @user.last_name.upcase
  end
end

# I'm leaving the address out since I'm not gonna use it in this example
User = Struct.new(:first_name, :last_name, :email) 
user = User.new("Oddly", "Functional", "hi@oddlyfunctional.com")

# We can compose the decorators as we want
decorated_user = UserContactEmailDecorator.new(UserUppercaseNamesDecorator.new(user))

decorated_user.email
# => "ODDLY FUNCTIONAL <hi@oddlyfunctional.com>"

decorated_user.first_name
# => "ODDLY"

decorated_user.last_name
# => "FUNCTIONAL"

# You probably guessed that the order matters
decorated_user = UserUppercaseNamesDecorator.new(UserContactEmailDecorator.new(user))

decorated_user.email
# => "Oddly Functional <hi@oddlyfunctional.com>" # Different!

decorated_user.first_name
# => "ODDLY"

decorated_user.last_name
# => "FUNCTIONAL"

# We can also use them separately
decorated_user = UserContactEmailDecorator.new(user)
decorated_user.email
# => "Oddly Functional <hi@oddlyfunctional.com>"

decorated_user.first_name
# => "Oddly"

decorated_user.last_name
# => "Functional"

decorated_user = UserUppercaseNamesDecorator.new(user)
decorated_user.first_name
# => "ODDLY"

decorated_user.last_name
# => "FUNCTIONAL"

decorated_user.email
# => "hi@oddlyfunctional.com" 
```

与之前错误调用的 decorator 不同，真正的 decorator 允许程序员在运行时组合任意行为，受益于不知道哪个类正在被接收的间接性，并且确信原始类的任何 decorator *和*的任何实例将实现相同的公共接口。它允许无限嵌套，这是一种可怕的([机架](https://github.com/rack/rack)，有人吗？).当通过添加或删除方法来更改接口时，这是不可能实现的，因为客户端类或调用者不能将任何潜在的修饰实例视为已定义公共接口的成员。

虽然这些例子仍然实现了不同的方式来表示模型，但是在 decorator 模式中并没有提到这个类将如何被使用。为了证明这一点，下面是一个不涉及表示上下文的用例:

```
class Operator
  def run
    # Do something
  end
end

class OperationLoggerDecorator
  def initialize(operator, logger)
    @operator = operator

    # An important point to note is that having the same interface
    # doesn't mean having the same constructor. Whichever client code
    # that's instantiating the decorator *knows* what it's doing.
    @logger = logger
  end

  def run
    @logger.info "Initiating operation..."
    result = @operator.run
    @logger.info "Finished with result: #{result}"

    result # Returning the result to be used by the client
  end
end

class OperationNotifierDecorator
  def initialize(operator)
    @operator = operator
  end

  def run
    result = @operator.run
    Notification.create("Operation finished with result: #{result}")

    result
  end
end

# I can freely compose the decorators!
operator = Operator.new
operator.run

OperationLoggerDecorator.new(operator).run
OperationNotifierDecorator.new(operator).run

OperationLoggerDecorator.new(OperationNotifierDecorator.new(operator)).run
OperationNotifierDecorator.new(OperationLoggerDecorator.new(operator)).run

# Or, in a more realistic manner:
Settings = Struct.new(:log?, :notify?)

# In a real application, the settings would be
# stored somewhere, probably in the database.
settings = Settings.new(true, true)

if settings.log?
  operator = OperationLoggerDecorator.new(operator)
end

if settings.notify?
  operator = OperationNotifierDecorator.new(operator)
end

operator.run 
```

唷，这让我如释重负！长久以来，我一直被这种普遍的误解所困扰，但从未花时间写下它。感觉好极了，简直是治疗！

我希望你现在能欣赏装饰者的真实面目。你可能会认为它们导致了太多的间接性，或者对于简单的情况来说它们是过度的解决方案(你可能是对的)。你有权不喜欢它，决定不使用它。但是，请不要把一个展示者叫做装饰者。

我必须补充一点，我不讨厌主持人。它们是管理某些复杂性和避免视图膨胀的好方法，但是名称和定义很重要。

* * *

你喜欢我的文章吗？在 Twitter 上关注我，时间是 [@oddlyfunctional](https://twitter.com/oddlyfunctional)

你需要任何帮助或者想和我配对程序吗？预订在[https://www.codementor.io/mrodrigues](https://www.codementor.io/mrodrigues)T3】的会议