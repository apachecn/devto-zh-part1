# 从 irb 使用 Rspec

> 原文：<https://dev.to/sigu/using-rspec-from-irb-ck4>

这是一个快速指南，向您展示了使 rspec 在 irb 上工作所需的步骤

让我们从加载 rspec 核心和期望开始

```
require 'rspec/core'         #this is what really runs your tests
require 'rspec/expectations' #readable syntax for checking properties of your code 
```

Enter fullscreen mode Exit fullscreen mode

然后我们包括匹配器模块

```
include RSpec::Matchers 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 irb
，rspec 不会自动为您包含此内容。现在，我们可以测试我们的预期:

```
>> expect(1).to eq(1)
=> true 
```

Enter fullscreen mode Exit fullscreen mode

呜呜呜！你可以走了..不尽然，试试这个

```
2.3.0 :003 > array_hashes = [{lol: nil}]
 => [{:lol=>nil}] 
2.3.0 :005 > include RSpec::Matchers
 => Object 
2.3.0 :006 > expect(array_hashes).to include(have_key(:lol)) 
```

Enter fullscreen mode Exit fullscreen mode

使用 Rspec include on IRB
时出现 rspec 错误，您将遇到如下所示的错误:

```
TypeError: wrong argument type RSpec::Matchers::BuiltIn::Has (expected Module)
 from (irb):6:in `include’
 from (irb):6
 from /usr/local/rvm/rubies/ruby-2.3.0/bin/irb:11:in `<main>’ 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细阅读这个错误，它期望一个模块作为参数，这是因为当使用 irb 时，我们是在主 ruby 对象中运行的。这个主 ruby 对象有另一个名为 include 的方法，它优先于 rspec 的 include 方法
,为了解决这个问题，我们可以将 RSpec::Matcher 添加到 main 的 singleton 类上:

```
$ irb
irb(main):001:0> require â€˜rspec/expectations’
=> true
irb(main):002:0> singleton_class.prepend RSpec::Matchers
=> #<Class:#<Object:0x007ff08a8d6620>>
irb(main):003:0> array_hashes = [{lol: nil}]
=> [{:lol=>nil}]
irb(main):004:0> expect(array_hashes).to include(have_key(:lol))
=> true 
```

Enter fullscreen mode Exit fullscreen mode

我们在 [Agile Ventures](http://agileventures.org/) 的 mob 会议上遇到了这个问题

这就是我们在 rspec expectations github 页面上提出问题[https://github.com/rspec/rspec-expectations/issues/1018](https://github.com/rspec/rspec-expectations/issues/1018)的地方，我们得到了这个黑客