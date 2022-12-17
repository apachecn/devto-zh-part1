# 使用 ActionMailerMatchers 进行更好的 ActionMailer 测试

> 原文：<https://dev.to/aaackerman/better-actionmailer-testing-with-actionmailermatchers>

[ActionMailerMatchers](https://github.com/contently/action_mailer_matchers) 为您提供了 RSpec 一行程序，帮助您在 Rails 应用程序中测试发送电子邮件。

通常通过执行以下操作来测试电子邮件是否已在控制器或服务中传递:

```
expect { some_action.execute }.to change { 
  ActionMailer::Base.deliveries.count }.by(1) 
```

但是，这并没有指定发送了什么电子邮件以及发送到哪里。 [ActionMailerMatchers](https://github.com/contently/action_mailer_matchers) 通过提供 have_received_email 匹配器解决了这个问题，它确保传递的电子邮件地址或用户(必须响应。email)是电子邮件的“收件人”地址。您还可以使用可选的主题和正文参数来检查内容是否与您期望发送的电子邮件相匹配。

```
expect("address@email.com").to have_received_email(body: "Wonderful email body") 
```

```
expect(some_user).to have_received_email(subject: "My great subject") 
```

[请查看](https://github.com/contently/action_mailer_matchers)并通过提出问题或拉式请求随意发表意见！