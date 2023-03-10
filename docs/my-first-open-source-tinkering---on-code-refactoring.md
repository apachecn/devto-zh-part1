# 我的第一次开源代码重构

> 原文：<https://dev.to/mohaazeem/my-first-open-source-tinkering---on-code-refactoring>

我已经写了几年的代码了，但是我很少接触任何开源代码，也很少使用它。看到许多有经验的开发人员将他们的成功(当然，部分是)归功于开源贡献，并相信投身于那个世界最终会提升我的技能，让我学到否则我不会学到的很酷的技巧，我决定尝试一下。

所以事不宜迟，让我们直接进入正题。

# Faker 宝石

在测试你的代码时，Faker 是一块方便的宝石。它可以快速轻松地生成虚假数据，并且支持各种各样的数据，如姓名、电子邮件、电话号码，甚至是《权力的游戏》中的角色。请务必访问回购，以了解更多信息。

#### Faker 模块

如上所述，faker 针对您希望使用的数据类型有不同的*模块*。您可以使用类似于`Faker::Address.street_name`或`Faker::Internet.public_ip_v4_address`的东西来分别获得一个随机的街道名称或 IP 地址。你可以在回购上找到其他非常酷的模块。

# 问题

我一直在使用`Faker::Time`模块，该模块(您可能已经猜到了)会生成随机时间戳供您在测试中使用。这个模块有一个`between`方法，它从和*到*日期获取*，并生成一个范围内的日期。*

用例是生成昨天和今天之间的时间，这可以通过以下方式实现:

```
Faker::Time.between(DateTime.now - 1, DateTime.now) 
```

输出示例如下:

```
=> 2017-06-24 01:00:32 +0200 
```

问题是我们将`DateTime.now`作为上限，所以它可能会生成一个对应于今天的日期。然而，它没有考虑到一天中还没有到来的小时、分钟或秒。也就是说，如果今天是 24/6，现在是上午 10:00:00，它仍然可以生成 24/6，但是是在上午 10:30:00(或者通常是上午 10:00:00 之后的某个时间)的日期。这是一个问题，因为它导致随机失败的测试用例。我们尤其不希望生成未来的日期。

# 解决方案(希望是有趣的部分)

这个随机测试用例失败的问题困扰了我很多，所以我决定阅读`Faker::Time`模块的实现代码，看看是什么导致了这个问题。于是我就这么做了，我有了一种直觉，知道如何解决这个问题。因此，我**将回购分发给**，**将**克隆到我的机器上，然后开始玩。

正如文章的标题所示，我写这篇文章并不是为了演示解决方案。我正在演示为什么修补开源项目是有用的。我致力于解决这个问题，并最终在 **Ruby** 中学习了更多关于代码重构的知识。我决定利用这一点来展示这种美丽语言的灵活性。

首先，让我们通过检查代码来看看问题的根源:

```
module Faker
   class Time < Date
     TIME_RANGES = {
       :all => (0..23),
       :day => (9..17),
       :night => (18..23),
       :morning => (6..11),
       :afternoon => (12..17),
       :evening => (17..21),
       :midnight => (0..4)
     }

     class << self
       def between(from, to, period = :all, format = nil)
         time = period == :between ? rand(from..to) : date_with_random_time(super(from, to), period)
         time_with_format(time, format)
       end

       # more code ...

       private

       def date_with_random_time(date, period)
         ::Time.local(date.year, date.month, date.day, hours(period), minutes, seconds)
       end

       # more code ...

       def hours(period)
         raise ArgumentError, 'invalid period' unless TIME_RANGES.has_key? period
         sample(TIME_RANGES[period].to_a)
       end

       def minutes
         seconds
       end

       def seconds
         sample((0..59).to_a)
       end
     end
  end
end 
```

#### 击穿

*   这个`Faker::Time.between(DateTime.now - 1, DateTime.now)`调用将导致`between`方法调用`date_with_random_time`方法。
*   该方法生成一个日期，其小时、分钟和秒分别由方法`hours`、`minutes`和`seconds`生成。*这正是问题所在。*
*   很明显，`seconds`方法(也是`minutes`所依赖的)生成一个一天中所有秒的数组，并从中抽取一个样本(随机选取一个)，而不管它实际上是否已经过去(它的效果在分钟内比在秒内更显著)。
*   `hours`方法做了本质上相同的事情，它只利用了一个哈希函数，该哈希函数将一个*周期*作为键，默认情况下是`:all`，并且返回一天中的所有时间。

找到问题所在后，我将展示我的代码是如何进化的，一次展示一个不同的版本。这有望让你了解我是如何增量重构代码的。

* * *

### 版本 1

这个想法很简单。在数组生成步骤和采样之前，我只在数组中输入已经过去的小时/分钟/秒。滥用 **Ruby** 中的一切都是对象这一事实，我可以在数组上调用`select`，并传递给它一个过滤不是未来日期的块。

所以代码看起来像这样:

```
def hours(period)
  raise ArgumentError, 'invalid period' unless TIME_RANGES.has_key? period
  sample(TIME_RANGES[period].to_a.select { |t| t <= DateTime.now.hour })
end

def minutes
  sample((0..59).to_a.select { |t| t <= DateTime.now.min })
end

def seconds
  sample((0..59).to_a.select { |t| t <= DateTime.now.sec })
end 
```

很清楚为什么`minutes`现在有了自己的实现。还得被`DateTime.now.min`绑定。

* * *

### 第二版

关于第一个版本有两个问题:

1.  我的修改覆盖了 gem 的默认行为。如果我们代码中的一部分可以容忍未来的日期，那会怎么样？此外，这个宝石在我们的代码库中使用，我不想到处修改人们的工作来适应我所做的改变。
2.  `minutes`和`seconds`方法看起来非常相似，最好不要有重复的代码。

所以现在我必须为那些使用它的人保留默认行为，并且还要确保修改(现在只有我会使用)不需要改变人们的工作。

#### 解决第一个问题

我给`between`方法引入了一个新的布尔参数，即`bound_by_now`，默认为`false`。用默认的*值*添加这个额外的参数解决了让其他人改变他们的实现的问题。

此外，我会使用一个奇特的 **Ruby** 三元运算符将默认行为与我的新实现分开。

```
def between(from, to, bound_by_now = false, period = :all, format = nil)
  time = period == :between ? rand(from..to) : date_with_random_time(super(from, to), bound_by_now, period)
  time_with_format(time, format)
end

# more code ...

private

def date_with_random_time(date, bound_by_now, period)
  ::Time.local(date.year, date.month, date.day, hours(bound_by_now, period), minutes(bound_by_now), seconds(bound_by_now))
end

# more code ...

def hours(bound_by_now, period)
  raise ArgumentError, 'invalid period' unless TIME_RANGES.has_key? period
  bound_by_now == true ? sample(TIME_RANGES[period].to_a.select { |t| t <= DateTime.now.hour }) : sample(TIME_RANGES[period].to_a)
end

def minutes(bound_by_now)
  bound_by_now == true ? sample((0..59).to_a.select { |t| t <= DateTime.now.min }) : sample((0..59).to_a)
end

def seconds(bound_by_now)
  bound_by_now == true ? sample((0..59).to_a.select { |t| t <= DateTime.now.sec }) : sample((0..59).to_a)
end 
```

#### 解决第二个问题

一种方法是将逻辑移到一个单独的方法中，即`get_min_sec`，并用一个标识符(目前是一个字符串)调用它，以区分分和秒。

```
def minutes(bound_by_now)
  get_min_sec('min', bound_by_now)
end

def seconds(bound_by_now)
  get_min_sec('sec', bound_by_now)
end

def get_min_sec(type, bound_by_now)
  case type
    when 'min' do limit = DateTime.now.min
    when 'sec' do limit = DateTime.now.sec
  end
  bound_by_now == true ? sample((0..59).to_a.select { |t| t <= limit }) : sample((0..59).to_a)
end 
```

所以最终修改后的版本看起来像这样:

```
def between(from, to, bound_by_now = false, period = :all, format = nil)
  time = period == :between ? rand(from..to) : date_with_random_time(super(from, to), bound_by_now, period)
  time_with_format(time, format)
end

# more code ...

private

def date_with_random_time(date, bound_by_now, period)
  ::Time.local(date.year, date.month, date.day, hours(bound_by_now, period), minutes(bound_by_now), seconds(bound_by_now))
end

# more code ...

def hours(bound_by_now, period)
  raise ArgumentError, 'invalid period' unless TIME_RANGES.has_key? period
  bound_by_now == true ? sample(TIME_RANGES[period].to_a.select { |t| t <= DateTime.now.hour }) : sample(TIME_RANGES[period].to_a)
end

def minutes(bound_by_now)
  get_min_sec('min', bound_by_now)
end

def seconds(bound_by_now)
  get_min_sec('sec', bound_by_now)
end

def get_min_sec(type, bound_by_now)
  case type
    when 'min' do limit = DateTime.now.min
    when 'sec' do limit = DateTime.now.sec
  end
  bound_by_now == true ? sample((0..59).to_a.select { |t| t <= limit }) : sample((0..59).to_a)
end 
```

* * *

### 第三版

显然，这种分离是以更长的代码为代价的。我不得不引入控制语句，比如三元运算符和 case 语句。但是同样的， **Ruby** 非常灵活，这也是可以修复的。

#### 删除三元运算符

传递给`select`方法的块最终计算为布尔值，那么为什么不将`bound_by_now`也注入它们呢？检查以下代码:

```
select { some_boolean_expression || !bound_by_now }) 
```

*   如果`bound_by_now`是`true`，那么块依赖于第一个表达式(因为任何与`false`或的东西就是它自己)，它将只选择已经过去的日期。
*   如果“bound_by_now”为`false`，那么将选择数组的所有元素，因为现在整个块的计算结果为`true`。

因此，将其应用于`hours`和`get_min_sec`，我们得到:

```
def hours(bound_by_now, period)
  raise ArgumentError, 'invalid period' unless TIME_RANGES.has_key? period
  sample(TIME_RANGES[period].to_a.select { |t| t <= DateTime.now.hour || !bound_by_now })
end

def minutes(bound_by_now)
  get_min_sec('min', bound_by_now)
end

def seconds(bound_by_now)
  get_min_sec('sec', bound_by_now)
end

def get_min_sec(type, bound_by_now)
  case type
    when 'min' do limit = DateTime.now.min
    when 'sec' do limit = DateTime.now.sec
  end
  sample((0..59).to_a.select { |t| t <= limit || !bound_by_now }) 
```

#### 删除案例语句

如版本 2 中所解释的，`minutes`和`seconds`将标识符传递给`get_min_sec`，以便它可以决定边界限制。但是我们如何删除这个 case 语句呢？我们可以利用 2 **红宝石**的把戏:

1.  **Ruby** 支持*符号*，某种意义上类似于字符串，只是一个符号一次只能有一个副本。这是标识符的一个很好的用例。一个符号由一个冒号后跟一个名字来声明，比如`:id`。
2.  **Ruby** 中的对象响应一个名为`send`的方法，该方法将一个方法的标识符作为参数，它只是在对象上调用那个方法(当然，如果对象响应那个方法)。我们可以很容易地在这里创建`:min`和`:sec`符号，并将它们传递给在`DateTime.now`上调用的`send`。

现在我们可以修改代码，看起来像这样:

```
def minutes(bound_by_now)
  get_min_sec(:min, bound_by_now)
end

def seconds(bound_by_now)
  get_min_sec(:sec, bound_by_now)
end

def get_min_sec(type, bound_by_now)
  sample((0..59).to_a.select { |t| t <= DateTime.now.send(type) || !bound_by_now })
end 
```

最后，在重构了两个控制语句之后，版本 3 如下所示:

```
def hours(bound_by_now, period)
  raise ArgumentError, 'invalid period' unless TIME_RANGES.has_key? period
  sample(TIME_RANGES[period].to_a.select { |t| t <= DateTime.now.hour || !bound_by_now })
end

def minutes(bound_by_now)
  get_min_sec(:min, bound_by_now)
end

def seconds(bound_by_now)
  get_min_sec(:sec, bound_by_now)
end

def get_min_sec(type, bound_by_now)
  sample((0..59).to_a.select { |t| t <= DateTime.now.send(type) || !bound_by_now })
end 
```

* * *

# 完整代码&测试

现在代码已经完成，让我们最后一次检查它，并检查我们如何测试它。

#### 全码

下面是版本 3 之后修改的完整代码:

```
def between(from, to, bound_by_now = false, period = :all, format = nil)
  time = period == :between ? rand(from..to) : date_with_random_time(super(from, to), bound_by_now, period)
  time_with_format(time, format)
end

  # more code ...

  private

def date_with_random_time(date, bound_by_now, period)
  ::Time.local(date.year, date.month, date.day, hours(bound_by_now, period), minutes(bound_by_now), seconds(bound_by_now))
end

# more code ...

def hours(bound_by_now, period)
  raise ArgumentError, 'invalid period' unless TIME_RANGES.has_key? period
  sample(TIME_RANGES[period].to_a.select { |t| t <= DateTime.now.hour || !bound_by_now })
end

def minutes(bound_by_now)
  get_min_sec(:min, bound_by_now)
end

def seconds(bound_by_now)
  get_min_sec(:sec, bound_by_now)
end

def get_min_sec(type, bound_by_now)
  sample((0..59).to_a.select { |t| t <= DateTime.now.send(type) || !bound_by_now })
end 
```

#### 测试

现在，为了测试新的行为，我可以做以下事情:

1.  创建一个空数组。
2.  使用`Fake::Time.between`推送一些随机生成的日期
3.  选择那些在未来生成的(`> DateTime.now`)并确保它打印出一个空数组。

这可以很容易地翻译成代码:

```
a = []
for i in 1..500 do
  a << Faker::Time.between(DateTime.now - 1, DateTime.now, true)
end
res = a.select { |t| t > Time.now }

puts res 
```

它会输出:

```
=> nil 
```

然而，这也是可以重构的！这可以使用一行代码来实现:

```
Array.new(500){ Faker::Time.between(DateTime.now - 1, DateTime.now, true) }.select { |t| t > Time.now  } 
```

这将输出:

```
=> [] 
```

使用相同的代码，我可以省略第三个`true`参数来激活 gem 的默认行为。这可能会从数组中打印出一些未来的日期，因为我生成了 500 个日期，所以很可能会找到一些日期。

# 最后的想法

作为一名开发人员，为开源项目做出贡献无疑会对你的进步做出贡献。它推动你学习，因为你实际上解决了真正的问题。我真的希望我能通过一个例子来证明这一点。

最后，我还没有成为重构大师。所以如果谁有什么建议，请告诉我！