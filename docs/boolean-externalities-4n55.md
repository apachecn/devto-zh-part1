# 布尔外部性

> 原文：<https://dev.to/narnach/boolean-externalities-4n55>

这是由 Avdi Grimm 关于布尔外部性的帖子激发的

[http://devblog.avdi.org/2014/09/17/boolean-externalities/](http://devblog.avdi.org/2014/09/17/boolean-externalities/)

在他的文章中，他提出了这样一个问题:如果一个谓词返回 false，它为什么会这样做？如果你链了很多谓词，就很难搞清楚为什么会得到你得到的答案。

考虑这个例子。它实现简单的链式谓词逻辑来确定对象是否可怕。

```
class SimpleBoo
  def scary?
    ghost? || zombie?
  end

  def ghost?
    !alive? && regrets?
  end

  def zombie?
    !alive? && hungry_for_brains?
  end

  def alive?
    false
  end

  def regrets?
    false
  end

  def hungry_for_brains?
    false
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

按照逻辑链，如果是鬼或僵尸，那就很可怕。他们两个都不是活人，只是一个鬼有遗憾，一个僵尸饿着脑浆。这是我可能会为生产应用程序编写的代码。它很简单，非常容易阅读。

不利的一面是，如果你想知道*为什么*某事令人害怕，你必须去读代码。你不能问物体*为什么*会得出它的结论。

## 为什么

下面是代码发展的逻辑下一步:我修改了代码，这样它可以解释*为什么*一个谓词返回 true 或 false，尽管在长度和易读性上有巨大的“代价”。

```
class WhyNotBoo
  # The object is scary if there is a reason for it to be scary.
  def scary?
    why_scary.any?
  end

  # Why is this object scary?
  def why_scary
    reasons = []

    # Early termination if this object is *not* scary.
    return reasons unless ghost? || zombie?

    # Recursively determine why this object is scary.
    reasons.concat([:ghost => why_ghost]) if ghost?
    reasons.concat([:zombie => why_zombie]) if zombie?
    reasons
  end

  # For the "why not" question we re-implement the "why" logic in reverse.
  def why_not_scary
    reasons = []
    return reasons if ghost? || zombie?
    reasons.concat([:not_ghost => why_not_ghost]) unless ghost?
    reasons.concat([:not_zombie => why_not_zombie]) unless zombie?
    reasons
  end

  def ghost?
    why_ghost.any?
  end

  def why_ghost
    return [] unless !alive? && regrets?

    [:not_alive, :regrets]
  end

  def why_not_ghost
    reasons = []
    return reasons if ghost?

    reasons << :alive if alive?
    reasons << :no_regrets unless regrets?
    reasons
  end

  def zombie?
    why_zombie.any?
  end

  def why_zombie
    return [] unless !alive? && hungry_for_brains?

    [:not_alive, :hungry_for_brains]
  end

  def why_not_zombie
    reasons = []
    return reasons if zombie?

    reasons << :alive if alive?
    reasons << :not_hungry_for_brains unless hungry_for_brains?
    reasons
  end

  def alive?
    true
  end

  def regrets?
    false
  end

  def hungry_for_brains?
    false
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

是的，这是更多的代码。所有复合谓词都有一个“why _[谓词]”和一个“why _ not _[谓词]”版本。现在你可以问某事是否可怕，为什么(或者为什么不可怕)。

这种方法有几个问题:

1.  逻辑不在`scary?`中，不在你所期望的地方。
2.  逻辑在`why_scary`和`why_not_scary`之间重复。不要重复自己，否则你会有逻辑错误。
3.  还有很多代码。很多样板代码，但是在同一个方法中也有多个关注点:簿记和实际逻辑。

## 清洁剂代码

让我们看看是否可以让代码再次清晰易读，同时保留“为什么”和“为什么不”的功能。

```
class ReasonBoo < EitherAll
  def scary?
    either :ghost, :zombie
  end

  def ghost?
    all :not_alive, :regrets
  end

  def zombie?
    all :not_alive, :hungry_for_brains
  end

  def alive?
    false
  end

  def regrets?
    false
  end

  def hungry_for_brains?
    false
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，一切顺利。代码*非常*合法，但是有一个神秘的超类`EitherAnd`。在我们看*T4 如何工作之前，让我们看看*它允许我们做什么:** 

```
boo = ReasonBoo.new
boo.scary? # => false
boo.why_scary # => []
boo.why_not_scary # => [{:not_ghost=>[:not_regrets]}, {:not_zombie=>[:not_hungry_for_brains]}]

boo.ghost? # => false
boo.why_ghost # => []
boo.why_not_ghost # => [:not_regrets]

boo.zombie? # => false
boo.why_zombie # => []
boo.why_not_zombie # => [:not_hungry_for_brains] 
```

Enter fullscreen mode Exit fullscreen mode

对于每个使用`either`或`all`的谓词，我们可以问它为什么或为什么不为真，响应是一系列的谓词检查。

## 我们如何获得更干净的代码

如果你想让你的代码清晰可辨，通常会有一些脏代码。在这个例子中，我们将它隐藏在一个超类中，但是它也可以是一个模块，不需要太多的努力。

为了让代码更容易阅读，我选择不将重复的逻辑提取到帮助器方法中。

这个类实现了两个方法:`either`和`all`。

两种方法具有相同的结构:

1.  设置 why _[谓词]和 why _ not _[谓词]方法。
2.  评估每个谓词，直到达到终止条件。
3.  跟踪哪些谓词为真/假，以解释*为什么*我们得到了我们所得到的结果。

```
class EitherAll
  # This method mimics the behavior of "||". These two lines are functionally equivalent:
  #
  # ghost? || zombie? # => false
  # either :ghost, :zombie # => false
  #
  # The bonus of `either` is that afterwards you can ask why or why not:
  #
  # why_not_scary # => [{:not_ghost=>[:not_regrets]}, {:not_zombie=>[:not_hungry_for_brains]}]
  def either(*predicate_names)
    #
    # 1\. Setup up the why_ and why_not_ methods
    #

    # Two arrays to track the why and why not reasons.
    why_reasons = []
    why_not_reasons = []

    # This is a ruby 2.0 feature that replaces having to regexp parse the `caller` array.
    # Our goal here is to determine the name of the method that called us.
    # In this example it is likely to be the `scary?` method.
    context_method_name = caller_locations(1, 1)[0].label

    # Strip the trailing question mark
    context = context_method_name.sub(/\?$/, '').to_sym

    # Set instance variables for why and why not for the current context (calling method name).
    # In our example, this is going to be @why_scary and @why_not_scary.
    instance_variable_set("@why_#{context}", why_reasons)
    instance_variable_set("@why_not_#{context}", why_not_reasons)

    # Create reader methods for `why_scary` and `why_not_scary`.
    self.class.class_eval do
      attr_reader :"why_#{context}", :"why_not_#{context}"
    end

    #
    # 2\. Evaluate each predicate until one returns true
    #

    predicate_names.each do |predicate_name|
      # Transform the given predicate name into a predicate method name.
      # We check if the predicate needs to be negated, to support not_<predicate>.
      predicate_name_string = predicate_name.to_s
      if predicate_name_string.start_with?('not_')
        negate = true
        predicate_method_name = "#{predicate_name_string.sub(/^not_/, '')}?"
      else
        negate = false
        predicate_method_name = "#{predicate_name_string}?"
      end

      # Evaluate the predicate
      if negate
        # Negate the return value of a negated predicate.
        # This simplifies the logic for our success case.
        # `value` is always true if it is what we ask for.
        value = !public_send(predicate_method_name)
      else
        value = public_send(predicate_method_name)
      end

      #
      # 3\. Track which predicates were true/false to explain *why* we got the answer we did.
      #

      if value
        # We have a true value, so we found what we are looking for.

        # If possible, follow the chain of reasoning by asking why the predicate is true.
        if respond_to?("why_#{predicate_name}")
          why_reasons << { predicate_name => public_send("why_#{predicate_name}") }
        else
          why_reasons << predicate_name
        end

        # Because value is true, clear the reasons why we would not be.
        # They don't matter anymore.
        why_not_reasons.clear

        # To ensure lazy evaluation, we stop here.
        return true
      else
        # We have a false value, so we continue looking for a true predicate
        if negate
          # Our predicate negated, so we want to use the non-negated version.
          # In our example, if `alive?` were true, we are not a zombie because we are not "not alive".
          # Our check is for :not_alive, so the "why not" reason is :alive.
          negative_predicate_name = predicate_name_string.sub(/^not_/, '').to_sym
        else
          # Our predicate is not negated, so we need to use the negated predicate.
          # In our example, we are not scary because we are not a ghost (or a zombie).
          # Our check is for :scary, so the "why not" reason is :not_ghost.
          negative_predicate_name = "not_#{predicate_name_string}".to_sym
        end

        # If possible, follow the chain of reasoning by asking why the predicate is false.
        if respond_to?("why_#{negative_predicate_name}")
          why_not_reasons << { negative_predicate_name => public_send("why_#{negative_predicate_name}") }
        else
          why_not_reasons << negative_predicate_name
        end
      end
    end
    # We failed because we did not get a true value at all (which would have caused early termination).
    # Clear all positive reasons.
    why_reasons.clear

    # Explicitly return false to match style with the `return true` a few lines earlier.
    return false
  end

  # This method works very similar to `either`, which is defined above.
  # I'm only commenting on the differences here.
  #
  # This method mimics the behavior of "&&". These two lines are functionally equivalent:
  #
  # !alive? && hungry_for_brains?
  # all :not_alive, :hungry_for_brains
  def all(*predicate_names)
    context_method_name = caller_locations(1, 1)[0].label
    context = context_method_name.sub(/\?$/, '').to_sym
    why_reasons = []
    why_not_reasons = []
    instance_variable_set("@why_#{context}", why_reasons)
    instance_variable_set("@why_not_#{context}", why_not_reasons)
    self.class.class_eval do
      attr_reader :"why_#{context}", :"why_not_#{context}"
    end

    predicate_names.each do |predicate_name|
      predicate_name_string = predicate_name.to_s
      if predicate_name_string.start_with?('not_')
        negate = true
        predicate_method_name = "#{predicate_name_string.sub(/^not_/, '')}?"
      else
        negate = false
        predicate_method_name = "#{predicate_name_string}?"
      end

      if negate
        value = !public_send(predicate_method_name)
      else
        value = public_send(predicate_method_name)
      end

      # The logic is the same as `either` until here. The difference is:
      #
      # * Either looks for the first true to declare success
      # * And looks for the first false to declare failure
      #
      # This means we have to reverse our logic.
      if value
        if respond_to?("why_#{predicate_name}")
          why_reasons << { predicate_name => public_send("why_#{predicate_name}") }
        else
          why_reasons << predicate_name
        end
      else
        if negate
          negative_predicate_name = predicate_name_string.sub(/^not_/, '').to_sym
        else
          negative_predicate_name = "not_#{predicate_name_string}".to_sym
        end

        if respond_to?("why_#{negative_predicate_name}")
          why_not_reasons << { negative_predicate_name => public_send("why_#{negative_predicate_name}") }
        else
          why_not_reasons << negative_predicate_name
        end

        why_reasons.clear
        return false
      end
    end

    why_not_reasons.clear
    return true
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

有可能提供对*为什么*一个布尔值用不到 200 行的 Ruby 代码和对你自己的代码的微小改变返回它的值的可追溯性。

尽管有明显的边缘情况和限制，但很高兴知道对于不知道*为什么*一个方法返回`true`或`false`的问题有一个潜在的解决方案。