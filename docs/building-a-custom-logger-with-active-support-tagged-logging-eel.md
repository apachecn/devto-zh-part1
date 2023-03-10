# 构建带有活动支持标记日志记录的自定义日志记录器

> 原文：<https://dev.to/sophiedebenedetto/building-a-custom-logger-with-active-support-tagged-logging-eel>

*这个帖子是[最初发表在我的博客](http://www.thegreatcodeadventure.com/building-a-custom-logger-in-rails/)，[伟大的代码冒险](http://www.thegreatcodeadventure.com/)，*。

`ActiveSupport::Logger`类是用于 Rails 的日志记录工具——默认的 Rails 日志记录器代表了该类的一个实例。Active Support 提供了一些额外的灵活日志记录类，我们可以用它们来定制 Rails 应用程序中的日志记录功能。

默认日志记录器允许我们设置日志记录级别，例如`WARN`，或`INFO`，或`ERROR`，并记录带有该级别标签的简单语句。

例如，

```
logger.info "uploading file #{file_name} to S3..." 
```

将日志:

```
[INFO] uploading get_schwifty.jpg to S3... 
```

默认的基于环境的日志文件:`<development/staging/test/production>.log`

但是，如果我们需要一种干净、易用的方式来记录更复杂的语句，该怎么办呢？

例如，假设您为一个流行的电子商务平台维护 Rails monolith。您的应用程序的一个责任是向卖家支付通过您的站点从用户处购买的任何商品。对于发生的每一笔购买，您将向供应商支付款项。

这是一个重要而复杂的代码流——我们需要为合适的产品筹集资金，并进行银行交易。我们肯定希望尽可能详细地记录这个代码流的进度，并且我们不希望这些日志语句在整个生产日志的混合中丢失。例如，我不想看到关于银行交易错误的声明夹在关于用户登录的声明之间。

我们不希望在向里克支付另一笔销售款的过程中出现问题，而不能发现问题并迅速解决问题。

[![](img/6ff3c78cb829a320800047f91387c442.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yK7C_uVx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nkgi31f3vwvmfp4h9vgv.png) 
来源:[https://masterofallscience.com](https://masterofallscience.com)(你们知道这个 btw 吗？？一些了不起的英雄作出了里克和莫蒂 gif/热图生成器。强烈推荐。)

## 项目要求

我们有两个要求:

1.  供应商付款日志报表应该记录到自己的文件中，`log/vendor_payment.log`
2.  记录此代码流的每个日志语句都应该有相同的标题，包括:
    *   接受付款的供应商的 ID 以及我们向他们付款的产品。
    *   日期和时间。
    *   日志级别。

```
[INFO 2017-09-15 12:14:1222] < vendor_id: 77801, product_id: 6701, transaction_id: 15110 >
[INFO 2017-09-15 12:14:1222] initiating bank transfer... 
```

此外，我们希望我们的记录器易于使用。我不希望每次我们需要记录东西的时候都要写两条日志语句:

```
my_logger.info "< vendor_id: #{transaction.vendor_id}, product_id: #{transaction.product_id}, transaction_id: #{transaction.id} >"
my_logger.info "initiating bank transfer... 
```

我懒得听那些废话。

相反，我们应该能够像默认的 Rails 记录器一样调用我们的客户记录器，也就是说，只需调用一次不同的日志级方法`#info`、`#error`、`#warn`、`#debug`。

我们将编写自定义日志记录器，这样我们可以传入事务对象的第二个参数，并且每次都自动生成日志语句头:

```
my_logger.info "initiating bank transfer...", transaction

=> 
[INFO 2017-09-15 12:14:1222] < vendor_id: 77801, product_id: 6701, transaction_id: 15110 >
[INFO 2017-09-15 12:14:1222] initiating bank transfer... 
```

我们将在`ActiveSupport::TaggedLogging`的帮助下构建我们的定制日志程序来获得这个功能。

### 什么是`ActiveSupport::TaggedLogging`？

[`ActiveSupport::TaggedLogging`](http://api.rubyonrails.org/classes/ActiveSupport/TaggedLogging.html) 用于包装任何标准的 logger 实例来给日志语句添加“标签”。在这种情况下,“标记”通常描述一个子域，默认使用的是`Rails.logger`,允许您用子域、请求 id 等来标记日志语句。在您的多用户、多实例生产应用程序中。

“包装”一个标准记录器实例意味着什么？我们可以调用一个`ActiveSupport::TaggedLogging`实例的`#tagged`方法，该方法带有一个我们想要应用的“标签”参数，以及一个包含对标准记录器的适当严重性级别方法(`#info`、`#error`等)的调用的块。).

```
logger = ActiveSupport::TaggedLogging.new(Logger.new("my_log_file.log")

logger.tagged("IP: #{request.ip}") { logger.info "something important is happening" }

=> [INFO][IP: 97.14.98.11] "something important is happening" 
```

`TaggedLogging`实例将我们传递给`#tagged`的参数添加到消息头中，然后将我们传递的日志语句作为参数写入`logger.info`。

需要注意的是，`ActiveSupport::TaggedLogging`实现了 [`#tagged`](http://api.rubyonrails.org/classes/ActiveSupport/TaggedLogging.html#method-i-tagged) 方法本身，同时它将`#info`、`#error`、`#warn`和`#debug`方法委托给它的`logger`属性——我们调用`#new`时传入的日志实例。

我们将利用`#tagged`方法为每个日志语句添加我们自己的消息头标记。

### 定义`VendorPaymentLogger`

为了获得对`#tagged`方法的访问，我们将从`ActiveSupport::TaggedLogging`继承我们的自定义日志记录器。

`TaggedLogging`实例期望使用`Logger`实例的参数进行初始化。

```
VendorPaymentLogger.new(Logger.new("log/vendor_payment.log") 
```

注意，我们通过将文件名作为参数传递给`Logger.new`，告诉我们的标准 logger 实例将日志语句写入我们的定制日志文件。

#### 定义和设置自定义格式化程序

回想一下，我们希望 logger 类不仅使用自定义的“标记”来编写日志语句，还使用正确格式化的日期和时间。

活动支持记录器使用`#formatter`属性来格式化日志语句的输出。属性`#formatter`需要设置为一个响应`#call`的值。

格式化程序由调用严重性级别方法的实例调用(`#info`、`#warn`等)。).将使用严重性级别、时间戳、progname(这里我们不讨论这个属性)和消息来调用它。

我们将构建自己的自定义格式化程序，并将其保存为需要这些参数的过程。

实例将语句级方法委托给相关的标准记录器实例。因此，我们将定义我们的`#initialize`方法，将相关记录器实例的`#formatter`属性设置为我们自己的自定义格式化程序 Proc。

```
#lib/vendor_payment_logger.rb

class VendorPaymentLogger < ActiveSupport::TaggedLogging
  def initialize(logger)
    super(logger)
    logger.formatter = formatter
  end

  def formatter
    Proc.new{|severity, time, progname, msg|
      formatted_severity = sprintf("%-5s",severity.to_s)
      formatted_time = time.strftime("%Y-%m-%d %H:%M:%S")
      "[#{formatted_severity}  #{formatted_time}  #{$$}]\n  #{msg}\n"
    }
  end
end 
```

此时，我们的日志语句将看起来像这样:

```
logger = ActiveSupport::TaggedLogging.new(Logger.new("log/vendor_payment.log")

logger.info "doing a thing now."
=> [INFO 2017-09-15 12:14:2345] doing a thing now 
```

#### 压倒了`#tagged`的方法

我们希望由我们的`VendorPaymentLogger`编写的每个日志语句在单独的消息之前有以下语句:

```
<vendor_id: 1234, product_id: 5678, transaction_id: 101112> 
```

我们*可以*在每次使用我们的日志程序时写下这样的东西:

```
logger = ActiveSupport::TaggedLogging.new(Logger.new("log/vendor_payment.log")

logger.tagged("< vendor_id: #{transaction.vendor_id}, product_id: #{transaction.product_id}, transaction_id: #{transaction.id} >") { (logger.info "doing a thing now." }

=> [INFO 2017-09-15 12:14:2345] <vendor_id: 1234, product_id: 5678, transaction_id: 101112>
=> [INFO 2017-09-15 12:14:2345] doing a thing now 
```

我不知道你怎么想，但我认为这太可怕了。我对每次使用这个记录器都要写出来不感兴趣。如果我们能像这样使用我们的日志记录器，那就更好了

```
 logger.info("doing a thing now", transaction)

=> [INFO 2017-09-15 12:14:2345] <vendor_id: 1234, product_id: 5678, transaction_id: 101112>
=> [INFO 2017-09-15 12:14:2345] doing a thing now 
```

干净多了。

为了获得这个功能，我们必须重写严重性级别方法的定义，以及`#tagged`方法的定义。

```
 #lib/vendor_payment_logger.rb

class VendorPaymentLogger < ActiveSupport::TaggedLogging
  ...
  def info(msg, transaction)
    tagged(transaction) { super(msg) }
  end

  def warn(msg, transaction)
    tagged(transaction) { super(msg) }
  end

  def debug(msg, transaction)
    tagged(transaction) { super(msg) }
  end

  def error(msg, transaction)
    tagged(transaction) { super(msg) }
  end

  def tagged(transaction)
    super(message_header(transaction))
  end

  def message_header(album)
    "< vendor_id: #{transaction.vendor_id}, product_id: #{transaction.product_id}, transaction_id: #{transaction.id} >"
  end
end 
```

这里，我们重新定义了每个严重性级别的方法，以接受`transaction`实例的第二个参数。然后，这些方法中的每一个都调用我们的`#tagged`方法的实现来格式化消息头。

我们的定制`#tagged`实现简单地调用一个助手方法`#message_header`来格式化我们想要的标签，然后继续调用`super`。

每个严重性级别的方法都用一个包含对`super`的调用的块来调用`#tagged`，并带有一个传入的日志语句消息的参数。记住`ActiveSupport::TaggedLogging`将严重性级别的方法委托给它自己的`logger`实例。当我们初始化我们的`VendorPaymentLogger`时，我们传入一个标准`Logger`类的实例。因此，在这些严重性级别的方法中调用`super`将通过`#info`、`#warn`等。对标准记录器实例的方法调用。

现在我们已经有了行为正常的格式化程序和标记程序，我们差不多完成了。

#### 设置类级记录器

我希望能够在我的应用程序中使用我的自定义记录器，无论“向供应商付款”代码流将我带到哪里。我*不想*每次都必须初始化`VendorPaymentLogger`的一个实例。我也不想将我的 Rail 应用程序的默认记录器设置为一个`VendorPaymentLogger`实例，因为我希望我的应用程序的其余部分继续使用默认记录器。

我们将通过教`VendorPaymentLogger`类响应严重性级别方法来解决这个问题。这样我可以像这样调用它:

```
VendorPaymentLogger.info("doing a thing now.", transaction) 
```

我们将在类级别定义一个`logger`属性。然后，我们将把`VendorPaymentLogger`的`logger`属性设置为它自身的一个实例。最后，我们将把类级别的方法列表委托给*类的* logger 属性，即`VendorPaymentLogger`的实例。困惑了吗？我们来看看:

```
#lib/vendor_payment_logger.rb

class VendorPaymentLogger < ActiveSupport::TaggedLogging
  class << self
    attr_accessor :logger
    delegate :info, :warn, :debug, :error, :to => :logger
  end
  ...
end 
```

```
#config/initializers/vendor_payment_logger.rb 

VendorPaymentLogger.logger = VendorPaymentLogger.new(Logger.new("log/vendor_payment.log")) 
```

现在我们可以像这样使用我们的自定义日志类:

```
VendorPaymentLogger.info "initiating bank transfer", transaction

=> 
[INFO 2017-09-08 12:25:2344] <vendor_id: 1234, product_id: 5678, transaction_id: 9101112>
[INFO 2017-09-08 12:25:2344] initiating bank transfer 
```

就是这样！现在，我们可以使用一个易于使用的 API 来维护一个关于供应商付款的自定义详细日志。