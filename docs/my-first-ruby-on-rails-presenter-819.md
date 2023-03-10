# 我的第一个 Ruby on Rails 演示者

> 原文：<https://dev.to/maxwell_dev/my-first-ruby-on-rails-presenter-819>

作为我成为全栈玩家的持续职业追求的一部分，我必须完成几项任务，例如:

*   突袭城堡
*   杀死恶龙
*   拯救公主
*   学习 Ruby on Rails

谢天谢地，我可以按任何顺序进行，所以我从 Rails 开始！

在完成了总是很有帮助的 [Ruby on Rails 教程](https://www.railstutorial.org/)之后，我开始学习它没有涵盖的主题。实际上，我在工作中曾经接触过一个——演示者模式！创建和设计我的第一个演示者是一个令人惊讶的挑战，所以我想在这里为其他人分享我的过程。遗憾的是，拯救公主的教程要推迟了。

我假设任何阅读这篇文章的人至少对 Rails 工具和功能有基本的了解，所以我将跳过对控制器、路线等的概述。如果您对这些还不熟悉，我建议您先浏览一下 Rails 教程！

### 什么是演示者？

呈现器是控制器和视图之间的一个额外的层，可以更好地组织正在使用的数据。你的应用数据库中的原始数据可能不会被原样使用:

*   一些数字可能需要相应的字符串或哈希值
*   大型数据组可能需要重组或重新计算
*   以上所有可能依赖于其他信息或需要额外的参数

如果没有演示者，还有另外两种不太好的方法来解决这个问题。

1.  全部在控制器中完成，但这不是控制器的用途。它们控制数据、布局和呈现页面的其他操作。使数据适合于瀑布是另一回事，而且使控制器体积庞大，难以维护。
2.  请助手帮忙，但会出现类似的问题。帮助者将变得过长和复杂，并且变得难以在具有更多通用任务的其他帮助者中组织起来(否则称为“帮助者地狱”)。

这两个答案的共同的，也是最重要的问题是，它把太多的责任混在了一起。演示者的角色是将数据重组为页面需要的内容。这是一个重要的、特定的任务目的，与其他两个不同。

这个特定的演示者是我为管理预算而做的一个 Rails 项目。它可以让用户跟踪支出(他们花了多少钱)和收入(他们赚了多少钱)。他们的预算有几个不同的类别，一个类别包含特定类型的支出或收入。当用户希望看到某个类别的重要信息时，我制作了这个演示器。

背景设置好了，让我们来制作真正的演示者吧！

### 设置基础知识

我的第一个障碍是演示者不像控制器、路线或视图——不包括 Rails magic。这给了设置一些额外的步骤。

首先是**设置一个基本演示者，**为任何功能，我想分享给他们所有人。我的只有一个功能，所以所有的演示者都可以使用这个应用程序的助手(带一个`h`前缀)。

```
class BasePresenter

  private

  def h
    ApplicationController.helpers
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在来设置我的类别展示者继承这个。

```
class CategoriesPresenter < BasePresenter

end 
```

Enter fullscreen mode Exit fullscreen mode

缺少 rails 的魔力也意味着我必须为演示者明确定义类别属性。我还必须用它需要的其他变量来初始化它们——这里是开始和结束日期，因为用户会按月查看这些信息。

```
class CategoriesPresenter < BasePresenter

delegate :category,
         :name, 
         :description, 
         :expense, 
         :expenses, 
         :length,
         :budget, to: :@category

  def initialize(category = nil, start_date = nil, end_date = nil)
    @category = category
    @start_date = start_date
    @end_date = end_date
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

设置完成后，我可以访问数据库中的所有类别信息。现在我可以开始为视图设计数据了！

### 定义普通演示者值

...不幸的是，有趣的部分将不得不等待。仅仅因为所有的类别值都被传入并不意味着我可以使用它们——它们需要被显式地定义为函数。

这并不难，只是多了一个有点繁琐的步骤:

```
 def id
    @category.id.to_i
  end

  def created_at
    @category.created_at
  end

  def budget
    @category.budget
  end 
```

Enter fullscreen mode Exit fullscreen mode

我想使用的值-它们-只需要像这样在演示者中定义。

但是即使简单的功能也可以在演示器中变得更加有用。例如，虽然我的数据包含所有类别的费用，但不包括它们的总价值。我有一个助手来计算这个(因为这是一个常见的计算)，所以我可以使用那个。

```
 def total
    h.category_total(expenses)
  end 
```

Enter fullscreen mode Exit fullscreen mode

另外，数据包含所有费用，但我只想找到开始和结束日期内的费用。所以我重新定义了费用，使其在这个范围内，并按价值重新组织。

```
 def expenses
    expenses = @category.expenses.select { |expense| expense.created_at.between?(@start_date, @end_date) }.sort_by &:value
    expenses.reverse
  end 
```

Enter fullscreen mode Exit fullscreen mode

另一个需要定义的是:尽管每个类别都有“费用”，但有些实际上可能被设定为收入来源。这就是为什么数据库中的“expense”值是一个布尔值，将它标记为费用或收入。展示者将该布尔值翻译成所需的标签。

此外，该视图经常以复数形式提及“支出”或“收入”,因此为了方便起见，这里也可以对其进行定义。

```
 def type
    if @category.expense
      "Expense"
    else
      "Income"
    end
  end

  def type_p
    type.pluralize(type)
  end 
```

Enter fullscreen mode Exit fullscreen mode

### 定义更有趣的演示者值

完成了简单的工作后，让我们来看看更复杂的演示者功能。

前两个定义了类别的重要链接:关于当月类别的信息，以及当月的所有类别。这使得在时间框架内链接页面变得更加容易。它们需要插入到 url 中的日期对象的某些信息。

```
 def date_link
    year = @start_date.strftime("%Y")
    month = @start_date.strftime("%-m")

    "#{year}/#{month}/#{@category.id}"
  end

  def month_link
    year = @start_date.strftime("%Y")
    month = @start_date.strftime("%-m")

    "/categories/#{year}/#{month}/"
  end 
```

Enter fullscreen mode Exit fullscreen mode

回过头来看，由于它们需要相同的设置变量，我将它们放在一起并输出一个散列。

```
 def link
    year = @start_date.strftime("%Y")
    month = @start_date.strftime("%-m")

    {
      :date => "#{year}/#{month}/#{@category.id}",
      :month => "/categories/#{year}/#{month}/"
    }
  end 
```

Enter fullscreen mode Exit fullscreen mode

如果不是这样，那么在其他地方定义年和月，并在单独的函数中引用。我还在学习，所以我不确定，但为了简单起见，将来我可能会输出更少的散列。

我写的最后一个函数还输出该类别余额的散列值，或者总支出或总收入与预期相差多少。支出和收入的计算方式不同——支出低于预算更好，收入高于预算更好(收入高于预期)。

这个函数返回数字余额，并使用一个助手返回一个相关的字符串，如果它是正数或负数。我将它们组合在一个函数的散列中，因为我认为它们足够接近，可以组合在一起。

```
 def balance
    if @category.expense
      total = @category.budget - h.category_total(expenses)
    else
      total = h.category_total(expenses) - @category.budget
    end

    {
      :total => total,
      :state => h.check_state(total)
    }
  end 
```

Enter fullscreen mode Exit fullscreen mode

至此，我的演示者完成了！

### 总之

随着我学习更多的 Rails，我计划继续使用演示者，因为他们满足了重要的需求。它们添加了所需的功能，而不会泄漏到不需要的区域(比如我的控制器或助手)，并使我的视图更接近“在正确的位置插入变量”模板。如果不使用它们来编写这个应用程序，就会陷入“助手地狱”,需要跟踪太多复杂的助手。如果我没有添加演示者，我可能会因为简单的沮丧或困惑而停止。

我们希望 Rails 能让 presenter 模式成为框架的正式部分，这样就不会那么麻烦了。就我个人而言，我几乎无法想象在没有它们的情况下使用 Rails。