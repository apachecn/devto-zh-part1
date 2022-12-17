# 我从 Rspec 特性测试到 Rails 系统测试的旅程

> 原文：<https://dev.to/dstull/my-journey-from-rspec-feature-tests-to-rails-system-tests>

# 目标:

*   通过利用 javascript 的系统测试实现可靠的无头测试
*   了解新的 Rails 系统测试设置

我经常发现自己花费数小时调试(有时最终删除)一个古怪的特性测试。最后，我决定停止这样做，在看了 Rails Conf [谈论](https://www.youtube.com/watch?v=sSn4B8orX70)关于系统测试之后，我决定是时候采取行动了。

因为我的第一个目标是保持简单，所以我没有马上投入 Rails 系统测试。由于我的测试套件的其余部分是用 Rspec 编写的，所以我首先尝试不使用那个设置。

# Rspec 故障

## 尝试[无头铬带水豚](https://robots.thoughtbot.com/headless-feature-specs-with-chrome)

### spec/rails_helper.rb

```
Capybara.register_driver :headless_chrome do |app|
  capabilities = Selenium::WebDriver::Remote::Capabilities.chrome(
    chromeOptions: { args: %w(headless disable-gpu) }
  )

  Capybara::Selenium::Driver.new app,
    browser: :chrome,
    desired_capabilities: capabilities
end

Capybara.javascript_driver = :headless_chrome 
```

### 结果:

*   Selenium::WebDriver::错误::未知错误...基本上，它给了我一个“点不可点击”的错误。我试图通过对水豚探测器进行简单的修复来解决这些问题，挖掘更多的答案，最终(大约 30 分钟)，无法解决这个问题。所以我开始了下一次尝试...

## 正在尝试[水豚 webkit](https://github.com/thoughtbot/capybara-webkit)

### 结果:

*   嗯，这并没有走得很远，因为它需要一个 [Qt](https://github.com/thoughtbot/capybara-webkit#qt-dependency-and-installation-issues) 依赖关系，当我试图 brew 安装它时，编译器因为 Xcode 升级依赖关系而呕吐。由于在我的 mac 上没有管理员权限，我采取了简单的方法，暂时放弃了 webkit。

## 承欢[捣蛋鬼](https://github.com/teampoltergeist/poltergeist)

### spec/rails_helper.rb

```
require 'capybara/poltergeist'

Capybara.javascript_driver = :poltergeist 
```

### 针对已知的[问题修改一些测试](https://github.com/teampoltergeist/poltergeist/issues/530):

#### 之前:

```
click_on 'Create Engine' 
```

#### 后:

```
find("input[type=submit][value='Create Engine']").trigger('click') 
```

### 结果:

*   成功！...至少我是这么认为的。所有功能均通过。然而，当运行整个测试套件时，他们随机地失败了。因此，我尝试用不同的“拥有内容”检查等方式来修复它们。那些通过了，再次运行，他们失败了，其他的(我没有修复)通过了。在这一点上，我感到非常沮丧，因为要么我将我的测试套件与其他测试分开运行，要么我需要解决这个问题...
*   注意:我*假设*这是由于数据库清理和不同的 AR 线程以及我所读到的。我确实设置了数据库清理器来截断 js 测试，但是当在 headless 模式下运行我的测试套件的其余部分时，这是不可靠的。此外，在走这条无头路之前，它们工作得很好。

受够了 Rspec 中的古怪测试和 3 次失败的尝试，我决定现在继续尝试 Rails 系统测试...

# 轨道系统测试:

### 设置:

#### 先决条件:

*   Rails 5.1
*   [闹鬼](https://github.com/teampoltergeist/poltergeist)
*   [摩卡](https://github.com/freerange/mocha)
*   [彪马](https://github.com/puma/puma)
*   我喜欢的一些其他宝石:FactoryGirl/Capybara(当然)，标准测试设置

#### 测试/测试 _ 助手. rb

*   设置真的很简单，只增加了几个助手之类的:

```
require File.expand_path('../../config/environment', __FILE__)
require 'rails/test_help'
require 'mocha/test_unit' # for stubs
require 'minitest/spec' # for let/it/etc

# not sure about this, but rails kinda does it here...feels sloppy, but I hate
# typing require in all my test files
Dir[Rails.root.join("test/support/**/*.rb")}.each { |f| require f }

class ActiveSupport::TestCase
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
  fixtures :all

  # Add more helper methods to be used by all tests here...
  extend MiniTest::Spec::DSL
  include WaitForAjax # hack for remote js calls that waits for ajax to be done
end 
```

#### 测试/应用 _ 系统 _ 测试 _ 案例. rb

*   这个文件是 Rails 5.1 自带的，但是我把它换成了使用 poltergeist:

```
require 'test_helper'
require 'capybara/poltergeist'

class ApplicationSystemTestCase < ActionDispatch::SystemTestCase
  driven_by :poltergeist
end 
```

#### 测试/系统/应用/我的地图 _ 测试. rb

*   这只是其中一个测试文件，但足以展示设置
*   我喜欢抽象出许多标准的水豚 DOM 遍历步骤，所以这些将在下一个文件中。

```
require 'application_system_test_case'

class MyMapTest < ApplicationSystemTestCase
  class StandardType < MyMapTest
    let(:subject) { MyMapForm.new('standard_type') }

    test 'page has different types' do
      subject.visit_page

      assert has_content?('first type')
      assert has_content?('second type')
      assert has_content?('third type')
    end

    test 'page has address link' do
      subject.visit_page
      assert has_link?('Address Import')
    end
  end
end 
```

注意:这最初是用 Rspec 编写的，但是转换很简单...

*   描述/类、信息技术/测试、期望..to/assert，s/have_content/has_content？..等等

#### [T1】test/support/forms/my _ map _ form . Rb](#testsupportformsmymapformrb)

*   这是我的文件，用来保存所有关于遍历 DOM 的可重复/肮脏的细节

```
class MyMapForm
  include Capybara::DSL
  include FactoryGirl::Syntax::Methods
  include Rails.application.routes.url_helpers

  def initialize(type)
    # using factory here of course and converting to find the factory
    @item = create(type.to_sym)
    # one more...
    create(type.to_sym)
    @app = item.app
  end

  def visit_page
    # don't care about authenticating here
    # in rspec, this uses allow_any_instance_of...
    ApplicationController.any_instance.stubs(:require_long).returns(true)
    ApplicationController.any_instance.stubs(:can_edit?).returns(true)

    visit app_path(app)
    find('a', test: 'My Map').click # in non headless, click_on 'My Map' works
    self
  end

  private 

  attr_reader :item, :app
end 
```

# 结论:

## 优点:

*   到目前为止，它似乎是有效的...老实说，这是最重要的事情。
*   失败时我的终端屏幕截图的默认功能。很简单，但是很好。
*   似乎解决了多个数据库连接的问题，因此减少了这些 gui 驱动的系统测试的缺陷
*   比非无头实现运行得更快
*   我想这使得在 CI env 中运行测试更加容易，因为我们可以跳过一些浏览器设置？
*   让团队对 Rails 测试框架有所了解(这也可以看作是下面的一个缺点)
*   通过使用不同的测试套件进行系统测试，它在配置和故障排除等方面给出了清晰的界限。
*   是 Rails 核心的一部分，所以我对未来的支持和改进充满信心

## 缺点:

*   增加了团队的技术负担，因为我们在 Rspec 中做所有其他事情
*   Rspec 的文档/示例远远超出了我所能找到的 Rails 系统测试的文档/示例(这也是撰写本文的部分原因)

## 最终想法:

虽然这次 Rails 系统测试之旅今天感觉很好，但是很有可能一个月左右以后我回头看的时候会想...使用这个和多个测试套件，我在想什么...但是话说回来，我们不也总是回过头来，对我们的发展决策有那些想法吗？

值得注意的是，我还看到 [rspec-rails](https://github.com/rspec/rspec-rails) 也计划很快支持这一点，我认为，正如这个[问题](https://github.com/rspec/rspec-rails/pull/1813)所看到的。如果实现了这一点，它将值得一看，并且绝对值得整合到一个测试套件中...我们走着瞧。