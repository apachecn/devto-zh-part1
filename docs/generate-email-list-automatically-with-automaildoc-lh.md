# 使用 automaildoc 自动生成电子邮件列表

> 原文：<https://dev.to/ohbarye/generate-email-list-automatically-with-automaildoc-lh>

你好，勤奋的开发人员谁管理系统发送电子邮件到某个地方。在本文中，我将介绍一个名为 **[automaildoc](https://github.com/ohbarye/automaildoc)** 的 Ruby gem，它可以从 RSpec 自动生成邮件列表。

[![image](img/1b6e26653cabec3cb3ca7c205827e32e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---NhcCFtF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/1811616/30522089-771f2474-9c05-11e7-8f9f-ed8f828c18b8.png)

## 如何使用

安装完 gem 后，从命令行运行`AUTOMAILDOC = 1 rspec`生成 HTML 来列出邮件。轻松点。

[![](img/1314961af8784b0cd4c535a0f5b656af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vFaMHqJf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/1811616/29994112-c6dacbc6-9002-11e7-812f-a346d415d6c4.gif)

让我为初学者详细说明一下。

将这一行添加到应用程序的 Gemfile:

```
gem 'automaildoc', group: :test 
```

Enter fullscreen mode Exit fullscreen mode

然后执行:

```
$  bundle 
```

Enter fullscreen mode Exit fullscreen mode

给你的邮件添加一个`automaildoc`标签。(你没有吗？那么现在正是写它们的好机会！)

```
RSpec.describe 'Sign up mail', automaildoc: true do
  let!(:user) { create(:user) }
  let!(:mail) { SignupMailer.mail(user) }

  it 'should send to a user' do
    expect(mail.to).to eq user
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

用`AUTOMAILDOC=1`运行 Rspec 测试。

```
$  AUTOMAILDOC=1 bundle exec rspec 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。然后 automaildoc 生成一个 HTML 文件给`./doc/mails`。文件应该是你想要的。

## automaildoc 背景

您是否收到过来自产品经理、业务部门、客户支持等利益相关方的此类询问。？

> "我可以确认从我们的法律/品牌检查系统发出的电子邮件吗？"
> 
> “你能出示一封类似的过去的电子邮件的文本来思考新发送的电子邮件的句子吗？”

然后开发者每次通过检查代码或者 I18n 翻译文件来响应。这样的情况我经历过很多次。

然而，由于 A/B 测试和 CS 请求，电子邮件的内容被频繁更新。在日常开发过程中，邮件总数会在不知不觉中增加或减少。此外，还有一个问题是，即使邮件是相同的，它也会根据上下文或条件而变化。我最终发现这个任务是可重复的、烦人的，而且并不比我想象的更舒服。

作为这个问题的解决方案，同事给了我一个想法，就是像`autodoc`一样从 RSpec 自动生成会很方便。之后，当我再次收到类似“我想看看邮件列表”的请求时，然后我就想起了这一点，很用力地试了试。

* * *

我希望这篇文章和 gem 也能对你有所帮助。