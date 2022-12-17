# 在 Ruby onÂ Rails 中进一步使用服务对象

> 原文：<https://dev.to/rob__race/going-further-with-service-objects-in-ruby-onrails>

*注意:本教程是我即将出版的书[构建 SaaS Ruby on Rails 5](https://BuildASaaSAppinRails.com) 中服务对象章节的摘录。这本书将指导你从卑微的开始，通过部署一个应用到生产。如果你觉得这类内容有价值，这本书现在就在预售中！*

* * *

作为最近一篇关于服务对象的文章的后续，我想更深入地探讨服务对象主题的细节，比如将对象保持在单一责任下、更好的错误处理和更清晰的用法。为了向您展示不同之处，我将重构我在[关于服务对象的第一篇文章](https://dev.to/rob__race/service-objects-in-ruby-on-railsand-you)中创建的服务对象。

我还想指出一些影响这一重构的博客文章，比如[我对 Rails 服务的看法](http://blog.sundaycoding.com/blog/2014/11/25/my-take-on-services-in-rails/)和[对 Rails 服务对象的剖析](http://multithreaded.stitchfix.com/blog/2015/06/02/anatomy-of-service-objects-in-rails/)。

```
class NewRegistrationService  
  def initialize(params)  
    @user = params[:user]  
    @organization = params[:organization]  
  end

  def perform  
    organization_create  
    send_welcome_email  
    notify_slack  
  end

  private

  def organization_create  
    post_organization_setup if @organization.save  
  end

  def post_organization_setup  
    @user.organization_id = @organization.id  
    @user.save  
    @user.add_role :admin, @organization  
 end

  def send_welcome_email  
    WelcomeEmailMailer.welcome_email(@user).deliver_later  
  end

  def notify_slack  
    notifier = Slack::Notifier.new "https://hooks.slack.com/services/89ypfhuiwquhfwfwef908wefoij"  
    notifier.ping "A New User has appeared! #{@organization.name} - #{@user.name} || ENV: #{Rails.env}"  
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们首先指出这样一个事实，即我们将一系列不同的职责放在一个服务类中。此外，它并不真正通过父类跟踪请求服务的控制器中的错误或成功。为了开始纠正这一点，我们将把每一项职责划分到各自的类中。

在展示这些类的代码之前，我还想谈谈新服务对象的另一个变化，即使用 build 来注入依赖的类:

```
class NewRegistration

  def self.build  
    new(OrganizationSetup.build, AdminUserSetup.build, SendWelcomeEmail.build, NotifySlack.build)  
  end

  def initialize(organization_setup, admin_user_setup,     send_welcome_email, notify_slack)  
    self.organization_setup = organization_setup  
    self.admin_user_setup = admin_user_setup  
    self.send_welcome_email = send_welcome_email  
    self.notify_slack = notify_slack  
  end

.... 
```

Enter fullscreen mode Exit fullscreen mode

这种构建/新技术之前已经由[皮奥特·索尔尼卡](https://medium.com/@_solnic_)(以 rom_rb 和 dry-rb 出名)在[http://solnic . eu/2013/12/17/the-world-needs-another-post-about-dependency-injection-in-ruby . html](http://solnic.eu/2013/12/17/the-world-needs-another-post-about-dependency-injection-in-ruby.html)的一篇博客文章中提出。其思想是可以调用服务类上的构建方法来实例化对象和任何依赖对象。如果是一个没有孩子，你只需要打电话给`new`。这样，注入的类可以被传入，而不是硬编码，并且在设置要测试的对象时可以得到好处。

事不宜迟，下面是新的子类:

…设置组织(即保存传入的记录)

```
# app/services/new_registration/organization_setup.rb 
class NewRegistration  
  class OrganizationSetup
    def self.build  
      new  
    end

    def call(organization)  
      organization.save!  
    end
  end  
end 
```

Enter fullscreen mode Exit fullscreen mode

…从新创建的组织中设置初始用户

```
# app/services/new_registration/admin_user_setup.rb 
class NewRegistration  
  class AdminUserSetup
    def self.build  
      new  
    end

    def call(user, organization)  
      user.organization_id = organization.id  
      user.save  
      user.add_role :admin, organization  
    end
  end  
end 
```

Enter fullscreen mode Exit fullscreen mode

…发送欢迎邮件

```
# app/services/new_registration/send_welcome_email.rb 
class NewRegistration  
  class SendWelcomeEmail
    def self.build  
      new  
    end

    def call(user)  
      WelcomeEmailMailer.welcome_email(user).deliver_later  
    end
  end  
end 
```

Enter fullscreen mode Exit fullscreen mode

…最后，ping 松弛时间

```
# app/services/new_registration/notify_slack.rb 
class NewRegistration  
  class NotifySlack
    def self.build  
      new  
    end

    def call(user, organization)  
      notifier = Slack::Notifier.new "[https://hooks.slack.com/services/89hiusdfhiwufhsdf89](https://hooks.slack.com/services/89hiusdfhiwufhsdf89)"  
      notifier.ping "A New User has appeared! #{organization.name} - #{user.name} || ENV: #{Rails.env}"  
    end
  end  
end 
```

Enter fullscreen mode Exit fullscreen mode

在新版本的服务中，我对子组件的划分略有不同，以更好地体现每个单独的子服务，并潜在地处理异常。现在，我们有了子服务，我们可以在父服务中调用它们

```
# app/services/new_registration.rb
def call(params)  
    user = params[:user]  
    organization = params[:organization]
    begin  
      organization_setup.call(organization)  
      admin_user_setup.call(user, organization)  
      send_welcome_email.call(user)  
      notify_slack.call(user, organization)  
    rescue => exception  
      OpenStruct(success?: false, user: user, organization: organization, error: exception)  
    else  
      OpenStruct(success?: true, user: user, organization: organization, error: nil)  
    end  
  end
... 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，与之前版本的 NewRegistration 服务相比，另一个变化是我们不再使用`.perform`方法，现在使用`.call`。为什么这很重要？首先，它实际上比 perform 更常见，甚至是这里和其他地方的评论者所指出的普遍理解的标准。此外,`.call`对 lambdas 做出响应，这可能会帮助你在其他地方使用这些对象。

我们解析出参数，就像我们在以前的版本中所做的那样，但是现在解析成常规变量而不是实例变量，因为它们被传递到同一个 public`.call`方法中的新的子服务中。不过，在两个版本中，要执行的项目的顺序和“瀑布”保持相对相同。

现在我们有了单独的子服务，我们可以从`begin...rescue..end`块中的任何服务捕捉异常。这样，我们可以捕捉到像组织不保存这样的问题，通过父对象和调用它来处理异常的控制器向上传递。此外，现在我们也将带有`OpenStruct`的结果对象传递回控制器。这个对象将保存`:success?`、被传入并被返回的对象以及来自服务的任何错误。

有了所有这些变化，我们从控制器调用服务的方式略有不同，但差别不大:

```
result = NewRegistration.build.call({user: resource, organization: @org})  
if result  
  redirect_to root_path(result.user)  
else  
  ** redirect_to last_path(result.user), notice: 'Error saving record'  
end 
```

Enter fullscreen mode Exit fullscreen mode

如果你想更深入地了解成功/错误路径、链接服务和用一个相当轻量级的 DSL 处理状态，我推荐查看来自[本杰明·罗斯](https://medium.com/@apneadiving)的[这篇文章](https://medium.com/p/chain-service-objects-like-a-boss-35d0b83606ab)。与 Ruby 一样，应用程序的大小、业务逻辑、可读性需求或样式以及复杂性会导致对 DSL 的简单类的需求发生变化。

最后，现在是一个测试新重构的服务有多容易的例子

```
# spec/services/new_registration_test.rb 
describe NewRegistration do  
   context "integration tests" do       
     before do  
      @service = NewRegistration.build  
      @valid_params = {user: Factory.create(:user), organization: Factory.build(:organization)}  
     end

     it "creates an organization in the database" do  
      expect { @service.call(@valid_params) }.to change { Organization.count }.by(1)  
     end
...etc, etc 
```

Enter fullscreen mode Exit fullscreen mode

这样，重构的注册服务对象更好地封装了单一责任原则(对于大多数虔诚的追随者来说可能并不完美)，更好的结果处理，改进的可读性，以及改进的依赖性处理。显然，几乎任何代码都可以不断改进，我期待着互联网来挑选这个版本的注册服务