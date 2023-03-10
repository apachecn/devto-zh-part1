# Ruby on Rails 中的 OOP 和系统测试

> 原文：<https://dev.to/bpohoriletz/oop-and-system-tests-in-ruby-on-rails-4nnj>

*   时间:30-40 分钟
*   级别:中级/高级
*   代码: [GitHub](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/oop_and_system_tests)

在本帖中，我们将看看如何使用 POROs、合作者、委托和模块来改进 sample `Rails 5.1.3 System Test`。

# 第 0 步

重构前的基本系统测试

```
# test/system/users_test.rb
require "application_system_test_case"

class UsersTest < ApplicationSystemTestCase
  test "visiting the index" do
    visit users_url

    assert_selector "h1", text: "User"
  end

  test 'creating new user' do
    visit users_url
    click_on 'New User'
    fill_in 'First name', with: 'Bill'
    fill_in 'Last name', with: 'Bird'
    click_on 'Create User'
    visit users_url
    assert_text 'Bill Bird'
  end

  test 'editing existing user' do
    User.new(first_name: 'Bill', last_name: 'Bird').save
    visit edit_user_url(User.first)
    fill_in 'First name', with: 'First'
    fill_in 'Last name', with: 'Last'
    click_on 'Update User'
    assert_text 'First Last'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

它验证了三件事:

1.  如果我们可以访问索引页面，如果它具有我们期望的结构
2.  如果我们可以创建一个新用户并在索引用户页面上看到它
3.  如果我们可以更新用户信息并在索引用户页面上看到变化

# 第一步

在这一步中，我们将:

1.  引入一个抽象类来帮助我们描述页面结构和功能
2.  添加一个页面类来测试显示用户页面
3.  在测试中使用新的页面类

作为第一步，让我们引入一个具有单一方法的抽象类，它将帮助我们指定页面上的元素，这一步的操作可以在[对应的提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/3803a56838360529898c6522d44c6cecccec2a20#diff-25437258f2fe39fc774476f923daa186)
中找到

```
# test/support/pages/base.rb
module Pages
  class Base
    Error = Class.new(StandardError)
    attr_reader :current_session
    attr_reader :url

    def self.has_node(method_name, selector, default_selector = :css, options = {})
      case default_selector
      when :css
        define_method(method_name) do
          css_selector = @css_wrapper + ' ' + selector
          current_session.first(default_selector, css_selector.strip, options)
        end
      when :xpath
        # XPATH accessor
        define_method(method_name) do
          current_session.first(default_selector, selector, options)
        end
      else
        fail Error, "Unknown selector #{default_selector}"
      end
    end

    private

    # initialize with Capybara session
    def initialize(url:, css_wrapper: ' ', current_session: Capybara.current_session)
      @current_session = current_session
      @url = url
      @css_wrapper = css_wrapper
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细看看`initilaize`方法和实例变量:

*   `@current_session` -默认为`Capybara.current_session`，允许我们在`has_node`方法中使用驱动程序的协作器对象
*   `@url` -必需参数，被测页面的 URL
*   `@css_wrapper` -默认为空字符串，当所有被测试的元素都在一个具有特定 CSS 类的元素中时很有用

现在让我们引入一个描述显示用户页面的新类

```
# test/support/pages/users/show.rb
require_relative '../base'

module Pages
  module Users
    class Show < Pages::Base
      has_node :notice, '#notice'
      has_node :edit_user_link, 'a', :css, text: 'Edit'
      has_node :back_link, '//a[text()="Back"]', :xpath
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以在这里看到三种识别页面元素的方法:

1.  按 CSS id
2.  按类型和文本
3.  通过 xpath

要记住的事情:

*   `has_node`只是围绕[水豚::Node::Finders#first](http://www.rubydoc.info/github/jnicklas/capybara/Capybara%2FNode%2FFinders:first) 的一个包装器，所以同样的事情可能以几种方式实现
*   `has_node`结果等于[Capybara::Node::Finders # first](http://www.rubydoc.info/github/jnicklas/capybara/Capybara%2FNode%2FFinders:first)，如果找到该元素，则结果为 [Capybara::Node::Element](http://www.rubydoc.info/github/jnicklas/capybara/Capybara/Node/Element) 的实例

现在让我们用`Pages::Users::Show`来测试`UsersController#show`

```
 test 'creating new user' do
    visit users_url
    click_on 'New User'
    fill_in 'First name', with: 'Bill'
    fill_in 'Last name', with: 'Bird'
    click_on 'Create User'

    page = ::Pages::Users::Show.new(url: user_path(User.last))
    assert page.notice.text == 'User was successfully created.'
    assert page.edit_user_link.text == 'Edit'
    assert page.back_link.text == 'Back'

    visit users_url
    assert_text 'Bill Bird'
  end 
```

Enter fullscreen mode Exit fullscreen mode

这是更好地理解如何使用页面类的第一步

# 第二步

在这一步中，我们将:

1.  介绍一种新的`Pages::Base#visit`方法
2.  将`Rails.application.routes.url_helpers`包含在`Pages::Base`中，以便访问班级内的路线
3.  添加`Pages::Users::New`、`Pages::Users::Edit`、`Pages::Users::Index`类
4.  使用新的类来重构我们的样本测试

我不会在这里包含新页面的代码，你可以在[对应的提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/0bb7eee0d824007b7719893b1bdc2a4913f9ff78#diff-25437258f2fe39fc774476f923daa186)中找到。让我们来看看现在的考试是怎样的:

```
# test/system/users_test.rb
require 'application_system_test_case'
require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'users', 'show')
require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'users', 'new')
require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'users', 'index')
require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'users', 'edit')

class UsersTest < ApplicationSystemTestCase
  test "visiting the index" do
    visit users_url

    assert_selector "h1", text: "User"
  end

  test 'creating new user' do
    ::Pages::Users::Index.new.instance_eval do
      visit
      new_user_link.click
    end

    ::Pages::Users::New.new.instance_eval do
      visit
      first_name.set( 'Bill' )
      last_name.set( 'Bird' )
      create_user_button.click
    end

    page = ::Pages::Users::Show.new(url: user_path(User.last))
    assert page.notice.text == 'User was successfully created.'
    assert page.edit_user_link.text == 'Edit'
    assert page.back_link.text == 'Back'

    ::Pages::Users::Index.new.visit
    assert_text 'Bill Bird'
  end

  test 'editing existing user' do
    User.new(first_name: 'Bill', last_name: 'Bird').save

    ::Pages::Users::Edit.new(url: edit_user_url(User.first)).instance_eval do
      visit
      first_name.set( 'First' )
      last_name.set( 'Last' )
      update_user_button.click
    end

    ::Pages::Users::Index.new.visit
    assert_text 'First Last'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们还剩下三步，但是让我们看看我们已经完成了什么:

1.  现在我们使用类方法代替原始选择器，所以如果页面结构改变，我们只需要改变相应的类
2.  因为我们使用了 collaborator 对象，所以我们有很好的块，并且在每一行的哪一页都很清楚

# 第三步

在这一步中，我们将:

1.  添加验证页面类中是否存在元素的功能
2.  向`Pages::Users::Show`添加一个方法来验证页面结构

先来看看测试中的变化([对应提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/255e1f0c96a20ac570f8049720663c66a78bcfc6#diff-25437258f2fe39fc774476f923daa186))

#### 之前

```
 # test/system/users_test.rb
  test 'creating new user' do
    # Not important piece
    page = ::Pages::Users::Show.new(url: user_path(User.last))
    assert page.notice.text == 'User was successfully created.'
    assert page.edit_user_link.text == 'Edit'
    assert page.back_link.text == 'Back'

    ::Pages::Users::Index.new.visit
    assert_text 'Bill Bird'
  end 
```

Enter fullscreen mode Exit fullscreen mode

#### [后](#after)

```
 # test/system/users_test.rb
  test 'creating new user' do
    # Not important piece
    ::Pages::Users::Show.new(test: self, url: user_path(User.last)).instance_eval do
      check_main_elements_presence
      assert notice.text == 'User was successfully created.'
    end

    ::Pages::Users::Index.new.visit
    assert_text 'Bill Bird'
  end 
```

Enter fullscreen mode Exit fullscreen mode

`Pages::Users::Show#check_main_elements_presence`定义

```
 # test/support/pages/users/show.rb
  def check_main_elements_presence
    notice_present?
    edit_user_link_present?
    back_link_present?
  end 
```

Enter fullscreen mode Exit fullscreen mode

为了完成这一步，我们:

1.  更改了`Pages::Base#initialize`以接受新的 collaboratorobject `test:`
2.  更改了`Pages::Base#has_node`以定义访问器和`*_present?`方法

# 第四步

在这一步中，我们将把功能提取到一个模块中([对应 _ 提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/1efd516747c9ddac87024e38867b4f984ed2ed05#diff-25437258f2fe39fc774476f923daa186))

我们先来对比一下`Pages::User::Edit`和`Pages::User::New`

```
 # pages/user/edit.rb
  require_relative '../base'
  module Pages
    module Users
      class Edit < Pages::Base
        has_node :first_name, '#user_first_name'
        has_node :last_name, '#user_last_name'
        has_node :update_user_button, '//input[@value ="Update User"]', :xpath
      end
    end
  end

  # pages/user/new.rb
  require_relative '../base'
  module Pages
    module Users
      class New < Pages::Base
        has_node :first_name, '#user_first_name'
        has_node :last_name, '#user_last_name'
        has_node :create_user_button, '//input[@value= "Create User"]', :xpath

      private

        def http_path
          new_user_path
        end
      end
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

它们都有两个相同的节点`first_name`和`last_name`，这并不奇怪——我们在两个页面上都呈现了相同的部分`form`。除了在测试这些页面时，我们填写这个表单，让我们将这两部分提取到一个模块中。

#### `Pages::Users::Partials::UserForm`模块

```
# test/support/pages/users/partials/user_form.rb
module Pages
  module Users
    module Partials
      module UserForm
        def self.included(clazz)
          clazz.has_node :first_name, '#user_first_name'
          clazz.has_node :last_name, '#user_last_name'
        end

        def fill_out_user_form(first: 'Bill', last: 'Bird')
          first_name.set(first)
          last_name.set(last)
        end
      end
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 重构后的页面

```
 # pages/user/edit.rb
  require_relative '../base'
  module Pages
    module Users
      class Edit < Pages::Base
        include Partials::UserForm

        has_node :update_user_button, '//input[@value ="Update User"]', :xpath
      end
    end
  end

  # pages/user/new.rb
  require_relative '../base'
  module Pages
    module Users
      class New < Pages::Base
        include Partials::UserForm

        has_node :create_user_button, '//input[@value= "Create User"]', :xpath

      private

        def http_path
          new_user_path
        end
      end
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

# 第五步

在这一步中，我们将:

1.  向页面类添加截图功能
2.  比较我们在步骤#1 之前和步骤#5 之后的测试

第一项非常简单，因为我们已经在`Pages::Base`中作为合作者进行了测试，我们只需要将`take_screenshot`添加到我们委托的方法列表中，您可以在[相应的提交](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/470815de082bbb57a23bdbfaaccb15af3b95374f#diff-25437258f2fe39fc774476f923daa186)中找到变化

现在让我们比较一下开始时的情况

#### 之前

```
# test/system/users_test.rb
require "application_system_test_case"

class UsersTest < ApplicationSystemTestCase
  test "visiting the index" do
    visit users_url

    assert_selector "h1", text: "User"
  end

  test 'creating new user' do
    visit users_url
    click_on 'New User'
    fill_in 'First name', with: 'Bill'
    fill_in 'Last name', with: 'Bird'
    click_on 'Create User'
    visit users_url
    assert_text 'Bill Bird'
  end

  test 'editing existing user' do
    User.new(first_name: 'Bill', last_name: 'Bird').save
    visit edit_user_url(User.first)
    fill_in 'First name', with: 'First'
    fill_in 'Last name', with: 'Last'
    click_on 'Update User'
    assert_text 'First Last'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在测试看起来怎么样

#### [后](#after)

```
# test/system/users_test.rb
require 'application_system_test_case'
require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'users', 'show')
require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'users', 'new')
require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'users', 'index')
require File.join(Rails.root.to_s, 'test', 'support', 'pages', 'users', 'edit')

class UsersTest < ApplicationSystemTestCase
  test "visiting the index" do
    visit users_url

    assert_selector "h1", text: "User"
  end

  test 'creating new user' do
    ::Pages::Users::Index.new(test: self).instance_eval do
      visit
      new_user_link.click
      take_screenshot
    end

    ::Pages::Users::New.new.instance_eval do
      visit
      fill_out_user_form
      create_user_button.click
    end

    ::Pages::Users::Show.new(test: self, url: user_path(User.last)).instance_eval do
      check_main_elements_presence
      assert notice.text == 'User was successfully created.'
    end

    ::Pages::Users::Index.new.visit
    assert_text 'Bill Bird'
  end

  test 'editing existing user' do
    User.new(first_name: 'Bill', last_name: 'Bird').save

    ::Pages::Users::Edit.new(url: edit_user_url(User.first)).instance_eval do
      visit
      fill_out_user_form(first: 'First', last: 'Last')
      update_user_button.click
    end

    ::Pages::Users::Index.new(test: self).instance_eval do
      visit
      assert_text 'First Last'
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

后一版本有几个优点，它们将被列在一个总结部分

# 总结

面向对象方法的优势:

1.  测试不那么脆弱——如果页面结构/逻辑改变，你只需要改变相应的页面类
2.  测试更具可读性——因为有了`instance_eval`块，你总是知道你在哪个页面
3.  定义页面上存在的元素要容易得多
4.  可以提取相同的功能
5.  其他团队成员可能会在他们的测试中使用页面类
6.  页面是 POROs，所有 Ruby 的美丽/功能都可以在那里使用

代码:

*   [应用](https://github.com/bpohoriletz/bpohoriletz.github.io/tree/master/samples/oop_and_system_tests)
*   [第一步](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/3803a56838360529898c6522d44c6cecccec2a20#diff-25437258f2fe39fc774476f923daa186)
*   [第二步](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/0bb7eee0d824007b7719893b1bdc2a4913f9ff78#diff-25437258f2fe39fc774476f923daa186)
*   [第三步](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/255e1f0c96a20ac570f8049720663c66a78bcfc6#diff-25437258f2fe39fc774476f923daa186)
*   [第四步](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/1efd516747c9ddac87024e38867b4f984ed2ed05#diff-25437258f2fe39fc774476f923daa186)
*   [第 5 步](https://github.com/bpohoriletz/bpohoriletz.github.io/commit/470815de082bbb57a23bdbfaaccb15af3b95374f#diff-25437258f2fe39fc774476f923daa186)

# 引人深思

1.  我对`Pages::Base`有`include Rails.application.routes.url_helpers`很不满意。这样做只是为了说明，如果页面 URL 是静态的，它可以成为 page 类的一部分，应该有更好的方法来实现它
2.  `has_node`只对单个元素有效，如果集合中有`has_nodes`会很酷。再说一次，页面类是 POROs，所以它们可以并且应该被修改以适应你的需要
3.  带有页面类的文件夹可能是自动加载路径的一部分，但不是每个人都喜欢自动加载
4.  根据测试框架的不同，`Pages::Base`中的委托方法会有所不同，但是它也可以用于其他测试框架(比如 RSpec)
5.  不是有多个测试可能有一个测试，你不会截断数据库，你可能有测试分组的用户登录，数据库中的额外数据可能会帮助你发现错误或让你讨厌你的生活:)