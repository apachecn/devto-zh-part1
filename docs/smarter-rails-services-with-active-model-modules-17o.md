# 具有活动模型模块的更智能的 Rails 服务

> 原文：<https://dev.to/sophiedebenedetto/smarter-rails-services-with-active-model-modules-17o>

## MVC 不够用！

我们熟悉 Rails 提供的 MVC(模型-视图-控制器)模式——我们的模型映射到数据库表，并将数据包装在对象中；控制器接收数据请求，并向视图提供数据；视图显示数据。一个常见的类比是餐厅——模型是食物，控制器是服务员接受你的订单并把它带给你，视图是你用餐的装饰精美的桌子。

MVC 是设计 web 应用程序的强大模式。它帮助我们将代码放在适当的位置，并构建架构良好的系统。但是我们知道 MVC 并没有为一个大规模 web 应用的所有职责和功能提供一个框架。

例如，假设你有一个成功的在线商店的 web 应用程序，你在那里销售，我不知道，让我们说一些有趣的东西，如时间旅行设备。

[![](img/05cb8b8666b71e5b72d0e502b73a8a09.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--gbttx-hr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.thegreatcodeadventure.com/conteimg/2017/04/Screen-Shot-2017-04-15-at-9.51.28-AM.png)  [来源](http://www.themindrobber.co.uk/police-box/TARDISes.png)

当有人“结账”并购买时，你的应用程序需要完成相当多的工作。您需要:

*   识别进行购买的用户
*   确定并验证他们的支付方式
*   制定或完成购买
*   创建并向用户发送确认/回执电子邮件

让我们更进一步说，我们的购买创建实际上是通过 API 端点:`/api/purchases`发生的。因此，我们购买处理的最终结果将涉及序列化一些数据。现在，我们的职责还包括:

*   序列化已完成的采购数据。

那是很大的责任！所有这些业务逻辑到底属于哪里？

而不是在控制器中，控制器的唯一职责是接收请求和获取数据。不在模型中，模型的唯一工作是从数据库中抽取数据，当然也不在视图中，视图的职责是向用户呈现数据。

进入，服务对象。

### 服务救助对象

服务对象是一个简单的 PORO 类(普通的 ruby 对象),用于包装 Rails 应用程序中复杂的业务逻辑。我们可以调用控制器中的一个服务，这样我们的`PurchasesController`就会看起来超级性感:

```
# app/controllers/purchases_controller.rb
class PurchasesController < ApplicationController
  def create
    PurchaseHandler.execute(purchase_params, current_user)
  end
end 
```

我知道，很漂亮，对吧？但是，当我们的服务类需要变得更智能、不那么“简单”时，会发生什么呢？

想想上面我们采购处理的责任列表。该列表包括*验证*一些数据(我们找到正确的用户了吗？此用户是否有与其帐户相关联的有效支付方式？)、*序列化*一些数据(将通过我们的 API 端点返回)并在购买生效(发送确认/发票电子邮件)后执行一些后处理*。*

### 有时 PORO 的服务根本不够

虽然完全有可能在 PORO 中处理所有这些职责，但 Rails 已经为某些确切的*验证、序列化、后处理*场景提供了一套强大的工具，这也是事实。这些听起来熟悉吗(我会给你提示，读这篇文章的标题)...主动模式！

主动模型为我们提供了验证、序列化和回调的工具，以便在特定方法被调用后触发特定方法*。我们最熟悉的活动记录工具箱是通过从`ActiveRecord::Base`继承我们的模型而获得的代码。你可能会猜测这就是我们的`PurchaseHandler`服务要做的事情。你错了。*

我们*不*想要*Active Record 提供的所有*工具——我们不想将我们的`PurchaseHandler`类的实例持久化到我们的数据库中，并且 Active Record 的许多模块处理这种交互。我们的服务类不是模型。它在很大程度上仍然是一种服务，其工作是包装业务逻辑以实现购买。

相反，我们将挑选提供我们感兴趣的工具的特定活动模型模块，将这些模块包含在我们的服务类中，并利用它们提供的代码来满足我们的特定需求。

让我们开始超级充电服务吧！

[![](img/df71b213b51cd7e01790a75c3a1e59f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---t-HM50---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.thegreatcodeadventure.com/conteimg/2017/12/Screen-Shot-2017-12-01-at-11.29.13-AM.png)

### 定义`PurchaseHandler`服务

首先，让我们规划出服务类的基本结构。然后，我们将处理包括我们的活动模型模块。

我们服务的 API 超级简单。看起来是这样的:

```
PurchaseHandler.execute(purchase_params, user_id) 
```

其中`purchase_params`看起来像这样:

```
{
  products: [
    {id: 1, name: "Tardis", quantity: 1},
    {id: 2, name: "De Lorean", quantity: 2}
  ],
  payment_method: {
    type: "credit_card",
    last_four_digits: "1111"
  }
} 
```

所以，我们的`PurchaseHandler`类将公开下面的方法:

```
# app/services
class PurchaseHandler
  def self.execute(params, user_id)
    # do the things
  end
end 
```

活动模型模块将让我们访问验证、序列化和回调挂钩，所有这些都可以在一个类的*实例*上获得。因此，我们的`.execute`类方法将需要初始化`PurchaseHandler`的一个*实例*。

我喜欢保持公共 API 非常简单——一个类方法——并使用`.tap`方法来保持面向公共的类方法非常干净。

```
#app/services
class PurchaseHandler
  def self.execute(params, user_id)
    self.new(params, user_id).tap(&:create_purchase)
  end
end 
```

`#tap`方法非常简洁——它产生调用它的块的实例，并在块运行后返回调用它的实例。这意味着我们的`.execute`方法将返回我们的`handler`实例，然后我们可以在控制器中序列化该实例(稍后将详细介绍)。

既然我们已经开始构建服务类，那么让我们开始引入验证处理程序所需的活动记录工具。

### 激活模型验证

我们的服务类需要什么样的验证？假设在我们尝试处理或完成购买之前，我们希望验证:

*   给定用户存在。
*   用户拥有与给定支付方式相匹配的有效支付方式。
*   要购买的产品有所需数量的库存。

如果这些验证失败，我们希望向处理程序实例的错误集合中添加一个错误。

`ActiveModel::Validations`模块将让我们访问活动模型验证方法*和*，通过它自己包含的`ActiveModel::Errors`模块，它将让我们访问`.errors` attr_accessor 和 object。

我们将使用`#initialize`方法来设置执行购买所需的数据:

*   分配用户
*   分配购买方法(即用户的信用卡)
*   分配要购买的产品

在方法`#initialize`运行之后，我们将对这个数据*进行验证。(那是不是有试镜的味道？是啊！)*

让我们一个接一个地构建我们的验证。然后，我们将编写代码，在活动模型回调的帮助下调用我们的验证。

首先，我们希望验证我们能够找到具有给定 ID 的用户。

```
# app/services
class PurchaseHandler
  include ActiveModel::Validations
  validates :user, presence: true

  def self.execute(params, user_id)
    self.new(params, user_id).tap(&:create_purchase)
  end

  def initialize(params, user_id)
    @user = User.find_by(user_id)
  end
end 
```

接下来，我们要验证我们是否能够为该用户找到与给定卡相匹配的信用卡。注意:我们的信用卡查找代码有点简单。我们只是使用用户的 ID 和卡的最后四位数字，包括在参数中。请记住，这只是一个简化的例子。

```
# app/services
class PurchaseHandler
  include ActiveModel::Validations
  validates :user, presence: true
  validates :credit_card, presence: true
  validates :products, presence: true

  attr_reader :user,:payment_method, :product_params, 
    :products, :purchase

  def self.execute(params, user_id)
    self.new(params, user_id).tap(&:create_purchase)
  end

  def initialize(params, user_id)
    @user = User.find_by(user_id)
    @payment_method = CreditCard.find_by(
      user_id: user_id, 
      last_four_cc: params[:last_four_cc]
    )
  end
end 
```

最后，我们将抓取要购买的产品:

```
#app/services
class PurchaseHandler
  include ActiveModel::Validations
  validates :user, presence: true
  validates :credit_card, presence: true
  validates :products, presence: true

  attr_reader :user, :credit_card, :product_params, :products, :purchase

  def self.execute(params, user_id)
    self.new(params, user_id).tap(&:create_purchase)
  end

  def initialize(params, user_id)
    @user           = User.find_by(user_id)
    @payment_method = CreditCard.find_by(
      user_id: user_id, 
      last_four_cc: params[:last_four_cc]
    )
    @product_params = params[:products]
    @products       = assign_products
  end

  def assign_products
    Product.where(id: product_params.pluck(:id))
  end
end 
```

关于验证，我们需要考虑的最后一件事是。我们不仅需要知道产品是否存在于数据库中，还需要知道我们是否有足够的库存来满足订单。为此，我们将构建一个定制的验证器。

#### 构建自定义验证器

我们的自定义验证器将被称为`ProductQuantityValidator`，我们将通过下面的代码行在我们的服务类中使用它:

```
#app/services
class PurchaseHandler
  include ActiveModel::Validations
  ...
  validates_with ProductQuantityValidator 
```

我们将在`app/validators/`
中定义我们的自定义验证器

```
# app/validators
class ProductQuantityValidator < ActiveModel::Validator
  def validate(record)
    record.product_params.each do |product_data|
      if product_data[:quantity] > products.find_by(id: product_data[:id])[:quantity]
        record.errors.add :base, "Not enough of product #{product_data[:id]} in stock."
     end
    end
  end 
```

它是这样工作的:

当我们调用处理程序的验证时(我保证很快就会实现)，就会调用`validates_with`方法。这反过来初始化定制的验证器，并在其上调用`validate`，使用首先调用`validates_with`的处理程序实例的参数。

我们的定制验证器为给定的记录(我们的处理程序实例)查找每个所选产品的数量，如果我们没有足够的库存，则向记录(我们的处理程序)添加一个错误。

现在我们已经构建了验证，让我们编写代码来调用它们。

#### 借助活动记录回调调用验证

在我们从`ActiveRecord::Base`继承并映射到数据库表的普通 Rails 模型中，当记录通过`.create`、`#save`或`#update`方法保存时，活动记录调用我们的验证。

您可能还记得，我们的服务类没有*而不是*映射到数据库表，也没有实现`#save`方法。

我们可以通过调用`ActiveModel::Validations`公开的`#valid?`方法来手动触发验证。我们*可以用`#initialize`的方法*这样做。

```
# app/services
class PurchaseHandler
  include ActiveModel::Validations
  validates :user, presence: true
  validates :credit_card, presence: true
  validates :products, presence: true
  validates_with ProductQuantityValidator
  ...

  def initialize(params, user_id)
    @user           = User.find_by(user_id)
    @payment_method = CreditCard.find_by(
      user_id: user_id, 
      last_four_cc: params[:last_four_cc]
    )
    @product_params = params[:products]
    @products       = assign_products
    valid?
  end 
```

让我们思考一下这个问题。`#initialize`方法的作用是什么？Ruby 的`#initialize`方法是通过调用`Klass.new`自动调用的，它的工作是构建我们类的实例。不仅*构建*实例，而且*还要验证它*，这感觉有点超出了`#initialize`的能力范围。我认为这违反了单一责任原则。

相反，我们希望我们的验证在`#initialize`方法执行之后自动为我们运行*。*

如果有一种方法可以让我们在对象生命周期的某一点自动触发某些方法，那该多好...

开玩笑的。当然有！主动模型的回调将允许我们这样做。

#### 定义自定义回调

首先，我们将在服务中包含`ActiveModel::Callbacks`模块。

```
# app/services
class PurchaseHandler
  include ActiveModel::Validations
  include ActiveModel::Callbacks
  ... 
```

接下来，我们将告诉我们的类为我们的`#initialize`方法启用回调。

```
# app/services
class PurchaseHandler
  include ActiveModel::Validations
  include ActiveModel::Callbacks

  define_model_callbacks :initialize, only: [:after]
  ... 
```

`#define_model_callbacks`方法定义了活动记录将附加回调方法列表。

现在，我们可以这样定义我们的回调:

```
# app/services
class PurchaseHandler
  include ActiveModel::Validations
  include ActiveModel::Callbacks

  define_model_callbacks :initialize, only: [:after]
  after_initialize :valid?
  ... 
```

这里，我们告诉我们的类在我们的`#initialize`方法之后触发`#valid?`(一个`Active::Model::Validations`方法)。

最后，为了让我们的定制回调真正被调用，我们需要调用`#run_callbacks`方法(由`ActiveModel::Callbacks`提供)，并将我们方法的内容封装在一个块中。

```
# app/services
class PurchaseHandler
  include ActiveModel::Validations
  include ActiveModel::Callbacks

  define_model_callbacks :initialize, only: [:after]
  after_initialize :valid?
  ...
  def initialize(params, user_id)
    run_callbacks do 
      @user           = User.find_by(user_id)
      @payment_method = CreditCard.find_by(
        user_id: user_id, 
        last_four_cc: params[:last_four_cc]
      )
      @product_params = params[:products]
      @products       = assign_products
    end
  end 
```

就是这样！

#### 生成采购

既然我们已经正确地验证了我们的服务对象，我们就可以实际执行购买了。为我们销售(非常真实的)时间旅行设备的虚构在线商店生成一个虚假购买(不幸的是)*不是*这篇文章的重点。因此，我们将假设我们有一个*附加的*服务类`PurchaseGenerator`，我们将在`#create_purchase`方法中调用它，并且我们不会担心它的实现。

```
#app/services
class PurchaseHandler
  include ActiveModel::Validations
  include ActiveModel::Callbacks

  define_model_callbacks :initialize, only: [:after]
  after_initialize :valid?
  ...
  def create_purchase
    PurchaseGenerator.generate(self)
  end 
```

那很容易！我们很擅长编程。

好了，现在我们可以创建购买了，我们需要再一次利用我们的定制回调来处理后期处理——生成发票和发送电子邮件。

### 定义自定义`after`回调

为什么在后期处理中会出现这种情况？嗯，我们可以将该逻辑放在(这里没有编码)`PurchaseGenerator`类中，或者甚至放在`Purchase`模型本身的一些助手方法中。这造成了我们无法接受的依赖程度。将采购生成与发票创建和电子邮件发送相结合意味着*每次创建采购*时，您每次都会生成发票和电子邮件*。如果您正在为测试或管理用户创建一个购买，该怎么办？如果您手动创建一个购买项目，向重要客户赠送赠品(医生*不断购买 Tardis 替换零件)会怎样？当然，有可能出现这样一种情况，您不*也不*想要实现这两种功能。保持我们的代码美观和模块化使它灵活和可重用，更不用说**简直太棒了**。**

 *既然我们已经确信这是一个多么棒的想法，那么让我们构建一个自定义回调来在之后触发*方法来处理发票和电子邮件确认。我们将定义两个私有 helper 方法来实现这一点。我们还将使用`run_callbacks`方法包装`#create_purchase`的内容，这样我们的回调就会被触发。* 

```
#app/services
class PurchaseHandler
  include ActiveModel::Validations
  include ActiveModel::Callbacks

  define_model_callbacks :initialize, only: [:after]
  define_model_callbacks :create_purchase, only: [:after]

  after_initialize :valid?
  after_create_purchase :create_invoice
  after_create_purchase :notify_user
  ...
  def create_purchase
    run_callbacks do 
      @purchase = PurchaseGenerator.generate(self)
    end
  end

  private

  def create_invoice
    Invoice.create(@purchase)
  end

  def notify_user
    UserPurchaseNotifier.send(@purchase)
  end 
```

### 序列化我们的服务对象

我们就要给我们的服务对象充好电了。最后但同样重要的是，我们希望能够利用`ActiveModel::Serializer`来序列化我们的对象，这样我们就可以用一个漂亮、整洁的 JSON 包来响应`/api/purchases`请求。

我们唯一需要添加到模型中的是包含`ActiveModel::Serialization`模块:

```
# app/services
class PurchaseHandler
  include ActiveModel::Validations
  include ActiveModel::Callbacks
  include ActiveModel::Serialization 
```

现在我们可以定义一个定制的序列化器`PurchaseHandlerSerializer`，并像这样使用它:

```
class PurchasesController < ApplicationController
  def create
    handler = PurchaseHandler.execute(purchase_params, user)
    render json: handler, serializer: PurchaseHandlerSerializer
  end
end 
```

我们的定制序列化器将会很简单，它将会取出一些我们想要序列化的属性:

```
# app/serializers
class PurchaseHandlerSerializer < ActiveModel::Serializer
  attributes :purchase, :products
end 
```

就是这样！

我们最后的`PurchaseHandler`服务看起来像这样:

```
class PurchaseHandler
  include ActiveModel::Validations
  include ActiveModel::Callbacks
  include ActiveModel::Serialization

  define_model_callbacks :initialize, only: [:after]
  define_model_callbacks :create_purchase, only: [:after]

  after_initialize :valid?
  after_create_purchase :create_invoice
  after_create_purchase :notify_user

  attr_reader :payment_method, :purchase, :products, :user, :product_params

  def self.execute(params, user_id)
    self.new(params, user_id).tap(&:create_purchase)
  end

  def initialize(params, user_id)
    run_callbacks do 
      @user           = User.find_by(user_id)
      @payment_method = CreditCard.find_by(
        user_id: user_id, 
        last_four_cc: params[:last_four_cc]
      )
      @product_params = params[:products]
      @products       = assign_products
    end
  end

  def create_purchase
    @purchase = PurchaseGenerator.generate(self)
  end

  private

  def create_invoice
    Invoice.create(purchase)
  end

  def notify_user
    UserPurchaseNotifier.send(purchase)
  end 
```

### 结论

这是对一些更常用的模块的简单介绍，但是我鼓励你深入研究活动模型工具箱。

主动模型是一种强大而灵活的工具。它的功能远远超出了我们习惯在 Rails 应用上看到的简单的“从`ActiveRecord::Base`继承你的模型”。

MVC 是一个指南。这不是板上钉钉的事。主要的想法是，我们*不想*用业务逻辑来混淆我们的模型、视图或控制器。相反，我们希望创建额外的对象来处理额外的职责。PORO 服务是一个很好的起点，但它并不总是足够的。为了给我们的服务对象充电，我们可以求助于主动模型的健壮功能。*