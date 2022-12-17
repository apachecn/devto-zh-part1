# 用 Ruby 试验显式契约

> 原文：<https://dev.to/lucasmazza/experimenting-with-explicit-contracts-with-ruby-4g0g>

几个月前，José Valim [开始了一场关于过度使用模拟和组件间耦合的对话。这让我有兴趣重新审视我是如何设计我的代码的，这也改变了我在我们当前的一个 Ruby 项目中的一点测试方法。](http://blog.plataformatec.com.br/2015/10/mocks-and-explicit-contracts/)

## 两个适配器的故事

回到去年 11 月，我通过一个 gem 将一个支付网关从零开始集成到我们的一个客户项目中，这个 gem 抽象了这个外部服务的 HTTP 接口。在这个支付流程中，我们必须首先向网关授权支付数据，网关将返回交易数据，以便我们在后台捕获支付，并继续执行依赖于成功支付流程的业务逻辑。

如果您曾经从事过类似的工作，您可能会记得我们在这种情况下需要处理的一些粗糙的边缘:对于每个可能的结果，如何使用正确的信用卡号来测试集成？我们有一个可用于开发和测试环境的沙盒吗？我们如何控制这种外部依赖性可能给我们的应用程序带来的性能和稳定性成本，以及支持这种核心特性的代码和这种 gem 之间的耦合？

我们处理这种依赖性的耦合和维护成本的尝试是将所有集成代码推到一个抽象层之后，该抽象层负责在一个`Payments`名称空间下处理这种支付流逻辑。

```
require 'gateway-xyz-gem'

module Payments
  class GatewayXYZ
    def authorize(order, credit_card)
      # Uses the `Order` details and the user `CreditCard` data to authorize
      # a new transaction on the XYZ Payment Gateway through the
      # `gateway-xyz-gem` classes.
    end

    def capture(payment_id)
      # Capture the payment information for a transaction that was previously
      # authorized.
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`orders#create`动作下面的某个地方(但不是直接在控制器方法本身中)，我们用`order`记录和`credit_card`值对象调用`GatewayXYZ#authorize`，我们与外部服务的集成就完成了。

我们可能在`GatewayXYZ`类上有一套很好的定义好的方法，但是我们在这些抽象上的工作还远远没有完成。我们可能会用类似于 [WebMock](https://github.com/bblimke/webmock) 或 [VCR](https://github.com/vcr/vcr) 的东西对其进行单元测试，以处理外部服务依赖性，但是我们系统中与该抽象交互的其他部分也将依赖于外部 API 来正常工作——即`OrdersController`、捕获付款的后台作业以及可能触发初始`authorize`调用的`Order`模型本身。我们是不是应该把现有的存根撒满我们的测试套件，然后就到此为止？

我们添加了一个网关实现，它模拟了`GatewayXYZ`的预期行为(具有与真实网关相同的方法签名)，并且不依赖于外部资源。它还为特定的输入提供了预定义的行为，因此我们可以基于测试输入测试他们的合作者的不同代码路径。

```
module Payments
  class Memory
    def authorize(order, credit_card)
      if BAD_CREDIT_CARD_NUMBERS.include?(credit_card.number)
        bad_response
      else
        ok_response
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 处理环境特定设置

现在我们需要使我们的`Payments::Memory`成为依赖于我们的支付抽象的测试用例的首选实现。在 Rails 应用程序上，有几种不同的方法可以做到这一点。

### `Rails.application.config`

我们可以在 app 中公开一个配置设置，说明它应该使用哪个实现，类似于`Action Mailer`如何为您的电子邮件选择交付方法，或者`Active Job`如何为您的后台作业选择不同的队列适配器。

```
# config/application.rb
Rails.application.config.x.payment_gateway = Payments::GatewayXYZ

# config/environments/test.rb
Rails.application.config.x.payment_gateway = Payments::Memory

# app/models/order.rb
class Order
  def authorize(credit_card)
    gateway = build_gateway
    gateway.authorize(self, credit_card)
  end

  private

  def build_gateway
    klass = Rails.application.config.x.payment_gateway
    klass.new
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### `Module.mattr_accessor`宏

您可以在依赖于可配置值的类上设置类级宏，并根据您的需要在代码中进行更改。如果您希望让配置更接近依赖于它的实现，而不是在应用程序代码和配置代码之间跳转(如果您希望调试某些东西或能够在运行时更改它)，这种方法会很有用。

```
# app/models/order.rb
class Order
  cattr_accessor :payment_gateway do
    Payments::GatewayXYZ
  end

  def authorize(credit_card)
    gateway = payment_gateway.new
    gateway.authorize(self, credit_card)
  end
end

# test/test_helper.rb
Order.payment_gateway = Payments::Memory 
```

Enter fullscreen mode Exit fullscreen mode

## 工厂法

当您想隐藏如何创建网关实现的实例时，这种方法很有用，这样依赖于它的其他类就可以有办法只请求网关对象，而不用担心如何创建它。

```
# app/models/payments.rb
module Payments
  matt_accessor :gateway do
    Payments::GatewayXYZ
  end

  def build_gateway
    gateway.new
  end

  module_function :build_gateway
end

# test/test_helper.rb
Payments.gateway = Payments::Memory 
```

Enter fullscreen mode Exit fullscreen mode

我不认为有一种单一的方法可以实现这种依赖注入，所以您应该自由选择一种适合您正在构建的接口和您团队的编码风格的策略——我个人是工厂方法和`cattr_accessor`方法的粉丝，因为它们感觉更远离配置，更接近应用程序代码，尽管配置方式感觉更符合第三方 gems 的全局 API。

## 跳过哈希驱动开发

我们的`GatewayXYZ`和`Memory`实现有相同的方法和相同的参数，但是我们需要考虑的第二件事是制作一个统一的 API:这些方法应该返回什么？

我们的`authorize`需要返回不止一个`truthy` / `falsy`值，因为我们需要收集更多关于我们这边的支付交易的信息，比如来自交易的`payment_id`，或者失败的原因(信用卡被拒绝了吗？请求中有无效数据)、日志记录或检测的详细信息等。如果我们考虑为多个服务实现这个 API(例如，假设我们现在需要一个`Payments::PayPal`，这些服务将以不同的格式返回这些数据，我们需要对这些数据进行规范化，这样这些差异就不会泄露给系统的其他部分。

有人可能会说，包含所有这些垃圾的`Hash`可以做到这一点，但是走这条路会为不一致和错误打开太多的大门，因为哈希是一个弱抽象，可以在任何地方变异，并且不会对返回值强制任何特定的格式或要求。

为此，我们可以实现一个`Payments::Result` struct/value 对象来表示我们的`authorize`动作的结果，并从我们系统中的每个网关实现返回它，实现我们想要的接口。

```
module Payments
  class Result < Struct.new(:payment_id, :errors)
    def ok?
      errors.blank?
    end

    def failed?
      !ok?
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们的`Result`类拥有我们的客户端代码所需的最少信息，每个网关负责从自己的数据中构造一个`Result`。`Memory`网关可以做像这样简单的事情:

```
module Payments
  class Memory
    def authorize(order, credit_card)
      Result.new(
        payment_id: SecureRandom.hex,
        errors: SAMPLE_ERRORS[credit_card.number])
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这种方法不仅有助于实现我们想要的接口，也有助于改进我们代码的其他区域，这些区域可以使用比简单的`Hash`实例更具体的抽象。

## 继续使用合同和宏

这种在我们的应用程序和这个外部服务之间建立更好契约的自制方法可以走很长的路，但是如果你愿意，你可以在你的 API 之上建立严格的检查，以确保你的对象如你所期望的那样协作。我们还没有尝试过，但是如果你想要那种 Ruby 上缺乏的类型约束，那么[契约](https://github.com/egonSchiele/contracts.ruby) gem 看起来非常有趣。

您甚至可以通过将方法包装到类型检查代理中来编写自己的检查，就像 [`refile`](https://github.com/refile/refile) 对其 [`Refile::BackendMacros`](https://github.com/refile/refile/blob/v0.6.2/lib/refile/backend_macros.rb) 模块所做的那样。当后端实现扩展[时，它提供宏来验证像`#upload(uploadable)`或`#delete(id)`这样的方法的输入，所以定制实现不需要担心自己验证这些参数。](https://github.com/refile/refile/blob/v0.6.2/lib/refile/backend/file_system.rb)

> 本文最初发表于[http://blog . platafomatec . com . br/2016/02/experimenting-with-explicit-contracts-with-ruby/](http://blog.plataformatec.com.br/2016/02/experimenting-with-explicit-contracts-with-ruby/)