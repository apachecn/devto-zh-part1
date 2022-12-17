# 轨道中的强参数—落入兔子洞

> 原文：<https://dev.to/tbrisker/strong-parameters-in-rails-down-the-rabbit-hole-32gk>

这一切都是从一个 bug 报告开始的(不总是这样吗？).一个用户抱怨说，当他们错误地将一个不正确的参数传递给我们的 API 时，他们没有得到任何错误消息。听起来像是快速解决办法，对吗？

不对。

首先是一些背景知识，为了这篇文章的目的，这些知识被大大简化了。我们的应用程序和许多其他应用程序一样，也有用户。一个用户可以属于多个组织。我们的 REST API 允许通过将 JSON 对象放入相关端点来更新用户，例如:

```
{  "user":  {  "organization_ids":  [1,2,3]}} 
```

但是我们的用户打错了。这是他们发的 JSON:

```
{  "user":  {  "organization_ids":  1}} 
```

它似乎工作得很好——他们没有得到错误信息。也许它工作得太好了:当他们试图传递一个不存在的组织 ID 时，它仍然返回一个成功的响应！事实上，每当他们忘记将 ID 包装在数组中时，这个参数就会被忽略。没有错误响应，日志中没有提到有什么地方出错了，什么都没有。

在我的开发环境中重现了这个 bug 之后，我开始深入研究。在控制器内部的 pry 的帮助下，我发现虽然请求中出现了`organization_ids`参数，但是在包含允许赋值的参数的`user_params`对象中却没有。这给我指出了强参数的方向。

强参数是 Rails 的一个特性，它防止将请求参数分配给对象，除非它们被明确允许。它有自己的 DSL(特定于领域的语言，或者换句话说，它理解的预定义语法)，允许您指出应该允许哪些参数。它还可以让您指示每个参数应该是散列、数组还是标量(即整数、字符串等)。)，以及其他一些与本文无关的功能。

下面是我们在用户控制器中定义的允许参数的摘录:

```
params.permit :name, :login, :mail, :organization_ids => [], ... 
```

好的，强参数需要一个数组作为:organization_ids 参数，但是它得到的是一个整数。那么为什么我在日志中看不到任何失败的迹象呢？

是时候深入研究 Rails 代码了。好在它是开源的，我自己就能读懂！下面是进行实际过滤的[函数](https://github.com/rails/rails/blob/v5.1.4/actionpack/lib/action_controller/metal/strong_parameters.rb#L522):

```
def permit(*filters)
  params = self.class.new

  filters.flatten.each do |filter|
    case filter
    when Symbol, String
      permitted_scalar_filter(params, filter)
    when Hash
      hash_filter(params, filter)
    end
  end

  unpermitted_parameters!(params) if self.class.action_on_unpermitted_parameters

  params.permit!
end 
```

所以看起来它确实对不允许的参数进行了某种处理…让我们在代码中采取另一个[步骤](https://github.com/rails/rails/blob/v5.1.4/actionpack/lib/action_controller/metal/strong_parameters.rb#L855):

```
def unpermitted_parameters!(params)
  unpermitted_keys = unpermitted_keys(params)
  if unpermitted_keys.any?
    case self.class.action_on_unpermitted_parameters
    when :log
      name = "unpermitted_parameters.action_controller"
      ActiveSupport::Notifications.instrument(name, keys: unpermitted_keys)
    when :raise
      raise ActionController::UnpermittedParameters.new(unpermitted_keys)
    end
  end
end 
```

但是等等，这个`action_on_unpermitted_parameters`是什么东西？原来这是一个可以在应用程序初始化中定义的配置选项。所以我查看了我们的`application.rb`，这是我们定义的:

```
config.action_controller.action_on_unpermitted_parameters = :strict 
```

嗯，这解释了为什么我们什么也没看见！这实际上不是该选项期望的值！

深入研究 git 历史，发现`:strict`选项实际上是 Rails 3 天前遗留下来的——当时它被传递给了`config.active_record.mass_assignment_sanitizer`,这是以前接受了`:strict`选项的净化输入的方法。当我们为 Rails 5 升级做准备而实现强参数时，这个选项被正确地重命名了，但是它的值保持不变。这意味着在大约一年半的时间里，我们默默地丢弃了所有被过滤的参数，而不是引发一个异常。

太好了！现在我所要做的就是用`:raise`替换`:strict`，写一点错误处理代码，给用户一个友好的消息，告诉他们他们传递的哪些参数是错误的，为什么，我就完成了！应该不会花太长时间…

经过几个小时的工作，我意识到，由于强参数目前的实现方式，没有简单的方法来判断参数是因为名称不允许还是因为值不是正确的类型而被过滤掉的(这是最初的问题—当过滤器只接受一个数组时传递一个整数)。好了，不用担心，现在让我们只显示一条一般消息，通知哪个参数有问题，这应该足以帮助用户找出问题所在。

所以我在基础 API 控制器里加了这样的东西:

```
rescue_from ActionController::UnpermittedParameters do |error|
  message = "Invalid parameter: %s. " % error.params.to_sentence
  message << 'Please verify that the parameter name is valid and the values are the correct type.'
  render_error 'param_error', :status => :bad_request, :locals => { :exception => error, :message => message }
end 
```

太棒了。好一天的工作，我们开个 PR，看看 CI 怎么说！

* * *

> 大约一小时后…
> 
> (我们有针对不同 Ruby 版本和不同数据库运行的大量测试套件)

詹金斯报告说测试失败了。准确的说是 636 次测试失败。呀！我想自从我们对强参数做了修改后，在这一年半的时间里，很多代码都被修改了。

沿着轨道[代码](https://github.com/rails/rails/blob/v5.1.4/actionpack/lib/action_controller/metal/strong_parameters.rb#L868)再挖一层，引发异常的`unpermitted_keys`是这样计算的:

```
def unpermitted_keys(params)
  keys - params.keys - always_permitted_parameters
end 
```

幸运的是，`always_permitted_parameters`实际上是另一个配置选项。顾名思义，它实际上是一个被过滤掉的参数列表，但当它们被过滤掉时不会引发错误。默认情况下，它被设置为`%w( controller action )`，因为这两个参数总是由 Rails 发送，但是它可以很容易地在应用程序配置中被覆盖。这对于您想要传递但不需要分配给模型的参数非常有用，例如，在索引视图中每页显示的条目数。我给这个列表添加了一些常见的参数，比如:

```
config.action_controller.always_permitted_parameters = %w(controller action format page per_page search locale utf8 _method authenticity_token locale _ie_support apiv id) 
```

这让我减少到 237 个失败的测试。对于快速的一行修改来说，这已经不错了。

然而，现在失败的测试将需要单独处理。通过在列表中添加一些我没有想到的更常见的参数，有些可能很容易修复，但有些实际上已经损坏了一段时间——我们只是没有意识到这一点，因为我们默默地过滤掉了许多可能导致测试失败的参数。在接下来的清理中，我们甚至可能会发现一些因此而被遗漏的新 bug。

从积极的一面来看，正确解决这个问题将给我们带来几个好处:

1.  当用户向 API 发送无效参数时，他们会收到信息反馈。即使在这一点上，我们仍然不能告诉他们出了什么问题，但他们至少会知道是哪个(哪些)参数导致了问题，并且不会认为某个操作成功了，因为事实上一些参数被悄悄地过滤掉了。
2.  开发人员在调试时会轻松一些。他们将得到一个他们可以处理的明确的异常，而不是浪费几个小时试图找出为什么他们期望的参数在控制器中不可用。
3.  我们的测试会更好。测试中无声的失败会让我们误以为一切都很好。由于我们有一个覆盖良好的大型测试套件(遗憾的是还不是 100%)，我们在进行代码审查时经常依赖测试来表明新代码没有引入回归。

我认为这些中的任何一个本身都值得我付出努力，更不用说同时得到这三个了。

我的主要收获是:

*   默默地失败不是一个好主意。在这种情况下，我们忽略了一个事实，即在更改为强参数后，`:strict`选项不再产生期望的结果——在过滤掉参数时会产生错误。
*   开源很牛逼。幸亏 Rails 代码是开放的，我才能够在几个小时内找出这个 bug 的原因。
*   不要害怕深入挖掘。即使这意味着进入你的一些依赖项或它们的依赖项的代码。在我的职业生涯中，通过深入挖掘，我学到了很多东西——无论是更好地理解我们的堆栈，还是阅读一些由非常有才华的开发人员编写的伟大代码。如果你在某个依赖项的代码中发现了一个 bug 修复它并发送一个 PR！这样，每个人都将从修复中受益，并且您不必在代码中创建一个难看的变通方法。
*   测试很重要。确保他们按照你期望的方式工作更加重要。不要只检查请求是否成功完成，要检查它是否做了它应该做的事情。
*   代码审查很重要。如果这个问题是在 1.5 年前的代码审查中发现的，我们现在就不必修复数百个测试了。尽管 PR 确实经过了审查，但这是一个巨大的 PR-257 文件更改，当更改这样一个几乎触及应用程序每个部分的基本方面时，这是有意义的。但是这么大的改动量，复习的时候很容易漏掉一些东西。只要有可能，小的 PRs 总是更好，因为遗漏某些东西的可能性要小得多。
*   好的文档很重要，尤其是如果你正在编写一个别人依赖的库。多亏了 Rails 文档，我很快就能理解每个函数的作用，并确定我需要设置哪些配置选项。

感谢您阅读本文！我很乐意在评论中得到任何反馈，或者通过 DM 在 [Twitter](https://twitter.com/tbrisker_pro) 上得到反馈。

*附:感谢* [*达芙娜·罗森布鲁姆*](https://medium.com/u/68a23010fc3d) *写了这篇文章，让我在我告诉自己应该开始写博客的大约两年后写了这篇文章。*