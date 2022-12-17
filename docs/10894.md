# 在 Rspec 中测试定制控制器的正确方法

> 原文：<https://dev.to/farisj/the-proper-way-to-test-custom-controllers-in-rspec-3968>

# 在 Rails 中测试定制控制器的正确方法

最近在工作中，我们花了一些时间重构我们的一个应用程序测试套件，以修复一些不稳定的测试，并减少套件运行所需的总时间。那天，我跑了`rspec --profile`去了解哪些测试和测试组最大程度地降低了测试套件的速度。

大多数测试都是接受或请求规范，使用 Capybara 或其他工具来模拟应用程序的真实请求。然而，有两个测试实际上是控制器测试！我很感兴趣，于是翻了翻那些文件，想了解更多。

事实证明，这两个慢速规范都是在测试从父控制器继承的具有某些特定功能的控制器的行为。在工作中，我们的主应用程序充当我们开发的一些其他应用程序的数据存储。这些不同的应用程序或上下文都涉及特定的控制器或命名空间，我们围绕这些控制器或命名空间来定制安全性和范围等。

这两个控制器测试都定义了一个新的控制器，它继承了被测试的控制器，然后在测试过程中绘制自定义的路径。测试完成后，spec 重新绘制了所有的路由，以将事情重置回之前的路由器状态。

下面是一个例子:

```
class TestController < CustomController
  def index
  end
end

describe TestController do
  before(:all) do
    Rails.application.routes.draw do
      resources :test
      end
    end

  after(:all) do    
    Rails.application.reload_routes!    
  end   

  describe "custom behavior" do
    # tests here
  end
end 
```

事实证明，在 Rspec 中绘制和重新绘制路线是非常昂贵的，因为这两个测试都在我们整个套件中最慢的前十组中！(注意:当我单独运行这些测试时，它们并没有那么慢，这仍然是个谜。也许如果我弄明白了，你将来会在博客上看到！)

我们可以通过一个内置的 Rspec 方法来测试控制器的行为，而不是定义一个新的控制器来继承我们想要测试的控制器，创建新的路由，然后在测试结束后删除这些路由！

Rspec `describe`或`context`中的`controller`块将生成一个匿名控制器，该控制器继承自父 describe 块中给定的控制器类。考虑到这一点，我们可以像这样重写我们的测试！

```
describe TestController do
  controller do
    def index
    end
  end

  describe "custom behavior" do
    # tests here
  end
end 
```

现在多干净啊！如果你问我，我会觉得很甜蜜。它还解决了每次测试都要缓慢重绘路线的问题！

向前去测试！