# 元编程的体验

> 原文：<https://dev.to/sdball/a-taste-of-metaprogramming-215n>

今天，我们从 Ruby 提供给我们的广泛的元编程能力中尝到一点甜头。我们将会看到`define_method`和`method_missing`将一类硬编码的重复方法转化为一类在运行时动态创建的方法或者从未真正存在过的方法。

# 元编程

重要的事情先来。按照 Ruby Rogues 的优良传统，让我们从定义开始。

元编程是一个松散的术语，简单地描述了编写代码本身编写代码的行为。我们 Rubyists 通常使用这个术语来描述在运行时修改自身或其他代码的代码。例如，动态定义方法的类。就个人而言，我不认为元编程与“常规”编程有什么不同。

# 基础类

```
require "test/unit"

class Food
  def taste
    'Mmm.'
  end
  def smell
    'Smells good!'
  end
  def consume
    'Delicious!'
  end
end

class TestFood < Test::Unit::TestCase
  def setup
    @food = Food.new
  end
  def test_food_can_be_tasted
    assert_equal('Mmm.', @food.taste)
  end
  def test_food_can_be_smelled
    assert_equal('Smells good!', @food.smell)
  end
  def test_food_can_be_eaten
    assert_equal('Delicious!', @food.consume)
  end
end

# Running tests:

...

Finished tests in 0.000499s, 6012.0240 tests/s, 6012.0240 assertions/s.

3 tests, 3 assertions, 0 failures, 0 errors, 0 skips 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经有了通过测试的工作代码。我们现在可以自由实验了！

目标是保持完全相同的测试通过，但是完全改变被测试的类。

# 定义 _ 方法

```
require "test/unit"

module Eatable
  ACTIONS = {
    taste: 'Mmm.',
    smell: 'Smells good!',
    consume: 'Delicious!'
  }
end

class Food
  include Eatable
  ACTIONS.each_pair do |action, result|
    define_method(action) do
      result
    end
  end
end

class TestFood < Test::Unit::TestCase
  def setup
    @food = Food.new
  end
  def test_food_can_be_tasted
    assert_equal('Mmm.', @food.taste)
  end
  def test_food_can_be_smelled
    assert_equal('Smells good!', @food.smell)
  end
  def test_food_can_be_eaten
    assert_equal('Delicious!', @food.consume)
  end
end

# Running tests:

...

Finished tests in 0.000499s, 6012.0240 tests/s, 6012.0240 assertions/s.

3 tests, 3 assertions, 0 failures, 0 errors, 0 skips 
```

Enter fullscreen mode Exit fullscreen mode

嘭！美味做作的编码例子。我们有一个`Eatable`模块，它定义了一个动作和结果的`ACTIONS`常量。然后，`Food`类使用这个常量通过`define_method`动态地创建方法。

我们甚至可以使用`ACTIONS`来驱动我们的测试(尽管这超出了我们的目标。)

```
require "test/unit"

module Eatable
  ACTIONS = {
    taste: 'Mmm.',
    smell: 'Smells good!',
    consume: 'Delicious!'
  }
end

class Food
  include Eatable
  ACTIONS.each_pair do |action, result|
    define_method(action.to_sym) do
      result
    end
  end
end

class TestFood < Test::Unit::TestCase
  def setup
    @food = Food.new
  end
  Eatable::ACTIONS.each_pair do |action, result|
    define_method(:"test_#{action}") do
      assert_equal(result, @food.send(action.to_sym))
    end
  end
end

# Running tests:

...

Finished tests in 0.000498s, 6024.0964 tests/s, 6024.0964 assertions/s.

3 tests, 3 assertions, 0 failures, 0 errors, 0 skips 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。我想我们已经达到了某种极限。我们的`Food`和`TestFood`类几乎无法区分。

因此，`define_method`允许我们编写代码，在执行时创建方法。现在我们来看看如何让我们的类拥有*从未存在过的*方法。我们不妨称他们为口头亲属。

*蟋蟀*

从*常见的疑点*？凯文·史派西的性格贯穿了电影的大部分？他从未真正存在过。所以…好了，继续。

# 方法 _ 缺失

类似于 define 方法，除了每当调用一个类来执行一个它还没有定义的方法时，它都会被调用。`method_missing`代替了被调用的方法，它的返回值被返回，就像该方法实际存在一样。

```
require "test/unit"

module Eatable
  ACTIONS = {
    taste: 'Mmm.',
    smell: 'Smells good!',
    consume: 'Delicious!'
  }
end

class Food
  include Eatable
  def method_missing(method)
    ACTIONS[method]
  end
end

class TestFood < Test::Unit::TestCase
  def setup
    @food = Food.new
  end
  def test_food_can_be_tasted
    assert_equal('Mmm.', @food.taste)
  end
  def test_food_can_be_smelled
    assert_equal('Smells good!', @food.smell)
  end
  def test_food_can_be_eaten
    assert_equal('Delicious!', @food.consume)
  end
end

# Running tests:

...

Finished tests in 0.000503s, 5964.2147 tests/s, 5964.2147 assertions/s.

3 tests, 3 assertions, 0 failures, 0 errors, 0 skips 
```

Enter fullscreen mode Exit fullscreen mode

刚刚发生了什么？嗯，`method_missing`至少有一个参数:试图被调用的方法的名称，作为一个符号传入。因为我们调用的方法与`ACTIONS`散列完全匹配，所以我们可以使用那个符号作为键来返回我们想要的结果。

我们 Rubyists 喜欢知道是否有东西像鸭子一样嘎嘎叫。

```
Duck.new.respond_to? :quack
# >> true 
```

Enter fullscreen mode Exit fullscreen mode

当这些方法实际上不存在时(也就是说，我们使用`method_missing`来假装它们在那里)，我们不能询问一个对象它们是否有这些方法！

```
module Eatable
  ACTIONS = {
    taste: 'Mmm.',
    smell: 'Smells good!',
    consume: 'Delicious!'
  }
end

class Food
  include Eatable
  def taste
    ACTIONS[:taste]
  end
end

class Food_DM
  include Eatable
  ACTIONS.each_pair do |action, result|
    define_method(action.to_sym) do
      result
    end
  end
end

class Food_MM
  include Eatable
  def method_missing(method)
    ACTIONS[method]
  end
end

puts Food.new.respond_to? 'taste'
# >> true
puts Food_DM.new.respond_to? 'taste'
# >> true
puts Food_MM.new.respond_to? 'taste'
# >> false
# D'oh!

puts Food.new.cook
# throws NoMethodError
puts Food_DM.new.cook
# throws NoMethodError
puts Food_MM.new.cook
# silently fails! D'oh! 
```

Enter fullscreen mode Exit fullscreen mode

正如您可以推断的那样，`method_missing`是一个强大的工具，可以产生令人惊讶的代码。我们 Rubyists 也不喜欢惊讶。如果你真的需要`method_missing`的能力和灵活性，你应该准备好付出额外的努力，让你的程序像好的 Ruby 公民一样运行。

```
class Food
  include Eatable

  def method_missing(method)
    if ACTIONS.has_key? method
      ACTIONS[method]
    else
      super(method)
    end
  end

  def respond_to?(method)
    ACTIONS.has_key? method || super(method)
  end
end

puts Food.new.respond_to? 'taste'
# >> true, woohoo!

puts Food.new.cook
# throws NoMethodError, woohoo! 
```

Enter fullscreen mode Exit fullscreen mode

那里。现在我们有了一个 Ruby 程序，它的行为就好像它真的拥有我们正在使用的方法一样；尽管它们并不存在。

我希望您在这次 Ruby 元编程之旅中至少有所收获。如果你想了解更多，请查看 Paolo Perrotta 的 pragprog 书籍[元编程 Ruby](http://pragprog.com/book/ppmetr/metaprogramming-ruby) 。