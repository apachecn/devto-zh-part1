# 在 Rails 5 中接收和解析电子邮件

> 原文：<https://dev.to/rob__race/receiving-and-parsing-email-in-rails-5-765>

[![](img/1951435dc844b254cd28b293e449b52d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qMnn19GO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AQmu6OFx86thnPRrL6pPHbw.png)

SaaS 应用程序中电子邮件的另一个方面是接收邮件。虽然与发送相比，这种方式不太常见或不常用，但它是让最终用户更快回复电子邮件或行动项目的好方法。

SaaS 应用程序中电子邮件的另一个方面是接收邮件。虽然与发送相比，这种方式不太常见或不常用，但它是让最终用户更快回复电子邮件或行动项目的好方法。

* * *

这是摘自我的书[用 Ruby on Rails 5](http://buildasaasappinrails.com/) 构建一个 SaaS 应用。我有一些样本章节和预售折扣。这本书即将完成！也就是说，你可能会在这篇文章中看到一些代码块，它们引用了与这本书相关的模型或对象，但并没有降低你在 Rails 中处理接收邮件的方式。

* * *

在我们正在构建的站立应用程序的情况下，我们可以有回复电子邮件提醒的指示，以根据他们的电子邮件回复创建一个新的站立！我们将使用的一些工具是 Mailgun 的电子邮件路由服务和 ngrok，一种 HTTP 隧道服务。`ngrok`对于贯穿本书剩余部分的几个解决方案非常有用。它允许您拥有一个 web 可访问的 URL，该 URL 通过隧道(连接)连接到本地机器中的端口。

这意味着，您将拥有一个将转发到您的计算机的`http://somesubdomain.ngrok.com`，以及一个在您启动`ngrok`时指定的特定端口。这样就可以用 Mailgun、Stripe(后来)、Github(后来)等外部服务进行测试了！

让我们从一些设置开始:

*   Download [ngrok](https://ngrok.com/)
*   解压并移动可执行文件到你想要的地方。
*   在*nix OS 中，使用`path/to/ngrok HTTP start 3000`打开 ngrok。这取决于 Rails 服务器使用的端口。ngrok 现在将使用随机生成的 URL 启动隧道服务。
*   可选地，如果你升级到`ngrok`的付费版本，你可以设置一个子域，这样你就不必在每次重启`ngrok`时都在别处更改你的设置。
*   在 Mailgun 中，转到 Routes 选项卡，创建将发送电子邮件的电子邮件路由(模仿像 Rails 这样的 MVC 路由)。输入以下设置:
*   选择了`Match Recipient`
*   在收件人字段输入`development.standup.*@app.yourdomain.com`。Mailgun 路由允许通配符匹配，这将允许您在电子邮件的`reply-to`中输入额外的字符。也就是说，添加类似用户的`hash_id`的东西，以便从收到的电子邮件中获得可识别的信息。
*   勾选`forward`并输入`http://yoursubdomain.ngrok.io/email_processor`作为该转发的目的地。
*   在按下`submit`按钮之前，您可以保留优先级并给路线起一个名字。

至此，我们可以开始修改应用程序了。应用程序更改将包括三个主要部分。首先，将添加一个新的 gem(和一个配套的适配器 gem)。`griddler`是轻松处理来信的主库；`griddler-mailgun`是专用于 Mailgun 的适配器，它允许`griddler`功能将 Mailgun 用作接收邮件路由器。接下来，Griddler 需要在应用程序范围内添加一些配置，以确保满足一些基本配置。最后，将添加一个电子邮件处理器文件来处理接收和解析电子邮件。

将 Griddler 添加到您当前的应用程序中最重要的一点是，如果您正在使用第 3 章中的 Gemfile，那么您已经安装了它。如果没有，只需将`gem 'griddler'`和`gem 'griddler-mailgun'`添加到您的 gem 文件中，并运行一个`bundle install`。

接下来，在应用程序中配置和设置 Griddler，您需要在初始化文件夹中添加一个新文件，设置一些配置值。然后，添加一个快捷行，将默认的 Griddler 路由添加到`routes.rb`文件中。

首先是煎锅配置:

```
 Griddler.configure do |config|
      config.reply_delimiter = '-- REPLY ABOVE THIS LINE --'
      config.email_service = :mailgun
    end 
```

这将设置 griddler 库将在电子邮件中查找的文本，并告诉它将使用已安装的 Mailgun 适配器。

接下来，添加一行代码，将基于库的路线安装到您的应用程序中。在`root to:`的正上方添加一行即可:

```
 Rails.application.routes.draw do
      ...

      # mount using default path: /email_processor
      mount_griddler

      root to: 'activity#mine'
    end 
```

通过使用该语法安装 Griddler，它将自动向您的应用程序添加一个路由，该路由将从指定的端点路由到基于 Griddler 的控制器:

```
 email_processor POST /email_processor(.:format)    griddler/emails#create 
```

Griddler 的 GitHub 文档中有更改默认设置的设置，但除非您想在路由路径或电子邮件处理器类名方面有所创新，否则这可能是不必要的。

默认设置期望类`EmailProcessor`存在，并使用方法`process`处理传入电子邮件的解析。然而，Griddler 并不关心实际的文件放在哪里，而是关心类是否存在并被加载。就我个人而言，我发现电子邮件处理最符合服务的定义，可以放在那里。

为了允许 Griddler 及其文件从收到的回复中获取文本，需要做一些改动。

首先，我们将更新`EmailReminderMailer`以创建一个唯一的回复地址，并将该电子邮件地址作为外发电子邮件的一部分:

```
class EmailReminderMailer < ApplicationMailer
  def reminder_email(user, team)
    @user = user
    @team = team
    reply_to = "'Standup App' <#{'development.' if Rails.env.development?}\
    standup.#{@user.hash_id}@app.yourdomain.com>"
    mail(
      to: @user.email,
      subject: "#{team.name} Standup Reminder!",
      reply_to: reply_to
    )
  end
end 
```

这里，如果当前的 Rails 环境是您的本地 Rails 应用程序，我们将通过添加`development`来构建`reply_to`字符串。这样，为开发创建的单独的 Mailgun 路由可以不同于您将在部署应用程序之前添加的生产路由。

接下来，我们将更新邮件模板，添加`##- Please type your reply above this line -##`和一些文本，让邮件收件人知道他们可以通过回复
来添加单口相声

```
doctype  html
html  xmlns="http://www.w3.org/1999/xhtml"
  head
    meta  content="width=device-width"  name="viewport"  /
    meta  content=("text/html; charset=UTF-8")  http-equiv="Content-Type"  /
    title= "#{@team.name} Reminder!"
    css:
      | *{margin:0;padding:0;font-family:"Open Sans",Helvetica,Helvetica,Arial,
      sans-serif;box-sizing:border-box;font-size:14px}img{max-width:100%}body{-
      webkit-font-smoothing:antialiased;-webkit-text-size-adjust:none;width:100
      %!important;height:100%;line-height:1.6}table td{vertical-align:top}body{
      background-color:#f6f6f6}.body-wrap{background-color:#f6f6f6;width:100%}.
      container{display:block!important;max-width:800px!important;margin:0
      auto!important;clear:both!important}.content{max-width:800px;margin:0
      auto;display:block;padding:20px}.main{background:#fff;border:1px solid #e
      9e9e9;border-radius:3px}.content-wrap{padding:20px}.content-block{padding
      :0 0 20px}.header{width:100%;margin-bottom:20px}.footer{width:100%;clear:
      both;color:#999;padding:20px}.footer a{color:#999}.footer a,.footer
      p,.footer td,.footer unsubscribe{font-size:12px}h1,h2,h3,a,th,td{font-
      family:"Open Sans",Helvetica,Arial,"Lucida Grande",sans-serif;color:#
      000;margin:40px 0 0;line-height:1.2;font-weight:400}h1{font-size:32px;
      font-weight:500}h2{font-size:24px}h3{font-size:18px}h4{font-size:14px;
      font-weight:600}ol,p,ul{margin-bottom:10px;font-weight:400}ol li,p li,ul
      li{margin-left:5px;list-style-position:inside}a{color:##3c8dbc;text-decor
      ation:underline}.btn-primary{text-decoration:none;color:#
      FFF;background-color:##3c8dbc;border:solid ##3c8dbc;border-width:5px 10px
      ;line-height:2;font-weight:700;text-align:center;cursor:pointer;display:
      inline-block;border-radius:5px;text-transform:capitalize}.last{margin-
      bottom:0}.first{margin-top:0}.aligncenter{text-align:center}.alignright{
      text-align:right}.alignleft{text-align:left}.clear{clear:both}.alert{font
      -size:16px;color:#
      fff;font-weight:500;padding:20px;text-align:center;border-radius:3px 3px
      0 0}.alert a{color:#fff;text-decoration:none;font-weight:500;font-size:16
      px}.alert.alert-warning{background:#f8ac59}.alert.alert-bad{background:#e
      d5565}.alert.alert-good{background:##3c8dbc}.invoice{margin:40px
      auto;text-align:left;width:80%}.invoice td{padding:5px 0}.invoice .
      invoice-items{width:100%}.invoice .invoice-items td{border-top:#eee 1px
      solid}.invoice .invoice-items .total td{border-top:2px solid #
      333;border-bottom:2px solid #333;font-weight:700}@media only screen and (
      max-width:640px){h1,h2,h3,h4{font-weight:600!important;margin:20px 0 5px!
      important}h1{font-size:22px!important}h2{font-size:18px!important}h3{font
      -size:16px!important}.container{width:100% !important}
      .content,.content-wrap{padding:10px !important}.invoice{width:10
      0% !important}
  body
    div  style="color: #b5b5b5;text-align:center;"
      | ##- Please type your reply above this line -##
    table.body-wrap  style="width:100%"
      tr
        td
        td.container  width="800"
          .content
            table.main  cellpadding="0"  cellspacing="0"  width="100%"
              tr
                td.content-wrap
                  table  cellpadding="0"  cellspacing="0"  style="width:100%"
                    tr
                      td.aligncenter
                        | Standup App
                    tr
                      td.content-block
                        h3= "#{@team.name} Reminder!"
                    tr
                      td.content-block
                        = "Just wanted to remind you to add your standup for \
                        the team: #{@team.name}"
                    tr
                      td.content-block.aligncenter
                        = link_to "Add Your Standup", new_standup_url(), \
                        {class:"btn-primary", style: "width:95%"}
                    tr
                      td.content-block
                        = "You can quickly submit your standup by replying to \
                          this email in the format:"
                        pre
                          pre
                          = "[d] This is a done item\n[t] This is a todo item\n\
                          [b] This is a blocker"
            .footer
              table  width="100%"
                tr
                  td.aligncenter.content-block
        td 
```

最后，我们需要向`Standups`表添加一个额外的列来跟踪来自 Mailgun 路由的电子邮件的`Message-ID`。由于您不能指望电子邮件服务提供“仅一次”递送，我们需要自己在站立台上跟踪这些唯一的 id。

```
 rails g migration AddMessageIdToStandups message_id 
```

接下来，在新创建的迁移`change`方法结束之前，您将希望添加`add_index :standups, :message_id`。随着`Standups`表的增长，这个索引将允许快速查找。最后，迁移实际变更:

```
 bin/rails db:migrate 
```

有了这些改变，我们现在可以添加新的`EmailProcessor`类来解析收到的电子邮件:

```
class EmailProcessor

    attr_reader :email

    def initialize(email)
     @email = email
    end

    TASK_TYPE_HASH = {
        '[d]' => 'Did',
        '[t]' => 'Todo',
        '[b]' => 'Blocker'
      }

    def process
        if Rails.env.development?
          Rails.logger.info '-----------EMAIL-------------'
          Rails.logger.info email.to.first[:token]
          Rails.logger.info email.body
          Rails.logger.info email.headers["Message-ID"]
          Rails.logger.info '-----------EMAIL-------------'
        end

    # Get a user hash_id from reploy-to or bail
        reply_user = email.to.first[:token]&.split('<')&.last&.split('@')&.first&.
        split('.')&.last
        return if reply_user.blank?

    # Find a user by the hash_id or bail
        user = User.find_by(hash_id: reply_user)
        return if user.nil?

    # Bail if standup with incoming message-id exists
        return if Standup.exists?(message_id: email.headers["Message-ID"])

    # Bail if a standup for today exists
        today = Date.today.iso8601
        return if Standup.exists?(standup_date: today)

    # Get content or bail
        tasks_from_body = email.body.scan(/(\[[dtb]{1}\].*)$/)
        return if tasks_from_body.blank? || tasks_from_body.empty?

    build_and_create_standup(
          user: user,
          tasks: tasks_from_body,
          date: today,
          message_id: email.headers["Message-ID"]
        )
      end

    private

    def build_and_create_standup(user:, tasks:, date:, message_id:)
        standup = Standup.new(
          user_id: user.id,
          standup_date: date,
          message_id: message_id
        )

    tasks.each do |task|
          task_type, task_body = task.first.scan(/(\[[dtb]\])(.*)$/).flatten
          standup.tasks << Task.new(type: TASK_TYPE_HASH[task_type], title: task_body)
        end

    standup.save
      end
    end 
```

这门课相对简单，但是让我们一节一节地来复习:

```
class EmailProcessor

    attr_reader :email

    def initialize(email)   
     @email = email
    end

    TASK_TYPE_HASH = {
        '[d]' => 'Did',
        '[t]' => 'Todo',
        '[b]' => 'Blocker'
      }

    ... 
```

这里，当 Griddler 调用该类时，我们正在初始化对象，将 email 设置为一个本地 email 变量。此外，我们正在创建一个散列，以便稍后在文本内容到`Task`类型的转换中使用。

```
 ...

    def process
        if Rails.env.development?
          Rails.logger.info '-----------EMAIL-------------'
          Rails.logger.info email.to.first[:token]
          Rails.logger.info email.body
          Rails.logger.info email.headers["Message-ID"]
          Rails.logger.info '-----------EMAIL-------------'
        end

    ... 
```

如果邮件是在本地
开发环境中处理的，这只是增加了一些额外的日志记录。

```
 ...

    # Get a user hash_id from reply-to or bail
        reply_user = email.to.first[:token]&.split('<')&.last&.split('@')&.first&.
        split('.')&.last
        return if reply_user.blank?

    # Find a user by the hash_id or bail
        user = User.find_by(hash_id: reply_user)
        return if user.nil?

    # Bail if standup with incoming message-id exists
        return if Standup.exists?(message_id: email.headers["Message-ID"])

    # Bail if a standup for today exists
        today = Date.today.iso8601
        return if Standup.exists?(user_id: user.id, standup_date: today)

    # Get content or bail
        safe_body = Rails::Html::WhiteListSanitizer.new.sanitize(email.body)
        tasks_from_body = safe_body.scan(/(\[[dtb]{1}\].*)$/)
        return if tasks_from_body.blank? || tasks_from_body.empty?

    ... 
```

在这里，我们获取解析中使用的一些信息，如果收到的电子邮件不足以处理和创建`Standup`,也给了`process`方法提前退出的机会。在第一部分中，解析收到的电子邮件地址以找到用户的`hash_id`。然后，该字符串用于查找用户。如果没有用户，该方法返回时不添加站立。

如果已经有一个具有当前消息 ID 的站，下一行将提前退出该方法。同样，这是为了防止电子邮件提供商不保证“只发送一次”。接下来，为当前日期生成一个变量，并确保当前用户和当前时间没有延迟。

最后，用正则表达式解析实际的电子邮件内容。正则表达式是一种编程语言，它允许你对一个字符串进行模式匹配，甚至捕获部分模式匹配。这种特殊的模式(你可以在这里得到更全面的语法解释)搜索以`[d]`、`[t]`或`[r]`开头的行。如果这些都存在，它会将内容捕获到行尾。内容主体上的`.scan`方法允许它捕获上述模式的所有出现。如果扫描的输出为空，则`process`方法退出。

```
 build_and_create_standup(
          user: user,
          tasks: tasks_from_body,
          date: today,
          message_id: email.headers["Message-ID"]
        )
      end

    private

    def build_and_create_standup(user:, tasks:, date:, message_id:)
        standup = Standup.new(
          user_id: user.id,
          standup_date: date,
          message_id: message_id
        )

    tasks.each do |task|
          task_type, task_body = task.first.scan(/(\[[dtb]\])(.*)$/).flatten
          standup.tasks << Task.new(type: TASK_TYPE_HASH[task_type], title: task_body)
        end

    standup.save
      end
    end 
```

这里的最后一部分是到目前为止保存到新的`Standup`中的所有存储信息的顶点。`user`、`tasks_from_body`、`today`和`message_id`都被传递到一个方法中，该方法将进行实际的保存。`build_and_create_standup`方法用用户的 ID、日期和`message_id`创建一个新的`Standup`。一旦创建了对象，tasks 字符串就会被迭代以构建带有类型的`Task`，并用`<<`语法指定为子对象。
最后，带有子对象`Tasks`的新`Standup`对象将与`standup.save`一起保存

最后，如果您回复一封电子邮件(通过 Mailgun SMTP 而不是 letter_opener 发送的),您可以测试这一切是否正常

```
 [d] Did a thing
    [d] And Another
    [t] Something to do
    [b] Something in the way. Some really long line about something or another 
```

测试这个将需要一个新的 spec 文件，其中有相当多的`it`块来测试`EmailProcessor`可能遇到的所有分支。

首先，如果我们创建一个能够快速生成在`EmailProcessor`规范中使用的电子邮件的工厂，那将是最好的。这样，电子邮件可以有默认值，然后我们可以使用`FactoryGirl` `.build`命令创建一个新的电子邮件对象，当需要测试处理器时，可以使用任何不同的属性。

工厂本身很简单:

```
FactoryGirl.define do
  factory :email, class: OpenStruct do
    # Assumes Griddler.configure.to is :hash (default)
    to [
      {
        full: 'to_user@email.com',
        email: 'to_user@email.com',
        token: 'to_user',
        host: 'email.com',
        name: nil
      }
    ]
    from(
      token: 'from_user',
      host: 'email.com',
      email: 'from_email@email.com',
      full: 'From User <from_user@email.com>',
      name: 'From User'
    )
    subject 'email subject'
    body '[d] Did a thing\n[t] Doing a thing\n[b] Blocked by a thing'
    headers {'Message-ID <98984d@local.mail>'}
  end
end 
```

现在，有了一个可用的工厂，`email_processor_spec`将能够根据需要通过特定的更改来轻松地旋转新的电子邮件对象，以测试所有处理器的条件分支。

```
require 'rails_helper'

describe EmailProcessor do
  subject(:email_processor) { EmailProcessor }
  let(:user) { FactoryGirl.create(:user) }
  let(:email) do
    FactoryGirl.build(:email,
      to: [
        {
          email: "standup.#{user.hash_id}@app.buildasaasappinrails.com",
          token: "standup.#{user.hash_id}@app.buildasaasappinrails.com"
        }
      ]
    )
  end

  describe 'processes incoming email' do

    it 'works as intended' do
      expect { email_processor.new(email).process }
        .to change(Standup, :count).by(1)
    end

    it 'fails on bad to' do
      bad_to = FactoryGirl.build(
        :email,
        to: [{ token: nil, email: 'standup@app.buildasaasappinrails.com' }]
      )
      expect { email_processor.new(bad_to).process }
        .to change(Standup, :count).by(0)
    end

    it 'fails on no user' do
      bad_to = FactoryGirl.build(
        :email,
        to: [
          {
            token: 'standup.o8yhiukj@app.buildasaasappinrails.com',
            email: 'standup.o8yhiukj@app.buildasaasappinrails.com'
          }
        ]
      )
      expect { email_processor.new(bad_to).process }
        .to change(Standup, :count).by(0)
    end

    it 'only saves one per message-id' do
      expect do
        email_processor.new(email).process
        email_processor.new(email).process
      end.to change(Standup, :count).by(1)
    end

    it 'only saves one per date' do
      email2 = FactoryGirl.build(:email, headers: { 'message-id': '123' })
      expect do
        email_processor.new(email).process
        email_processor.new(email2).process
      end.to change(Standup, :count).by(1)
    end

    it 'fails on empty or bad body' do
      email = FactoryGirl.build(:email, body: '90ioqwhdk.qhdu')
      email2 = FactoryGirl.build(:email, body: '')
      expect do
        email_processor.new(email).process
        email_processor.new(email2).process
      end.to change(Standup, :count).by(0)
    end
  end
end 
```

虽然很长并且包含六个例子，但是这个规范实际上非常简单。它首先测试一切都已设置好并正常工作的幸福之路。然后按照这些路径在`EmailProcessor`的`.process`方法中出现的顺序，测试每一条没有创建站立的失败路径。

整个 rspec 套件的快速运行应该显示没有失败的测试和近乎完美的测试/代码覆盖:

```
rspec spec ........................................................................................................................................................

    Finished in 25.78 seconds (files took 10.16 seconds to load) 152 examples, 0 failures Coverage report generated for RSpec to standup_app/coverage. 428 / 431 LOC (99.3%) covered. 
```