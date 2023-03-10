# Rails 中带有 Arel 的可组合查询生成器

> 原文：<https://dev.to/sophiedebenedetto/composable-query-builders-in-rails-with-arel-31aj>

我们将使用 Arel 构建一个灵活的查询构建器类，它将允许我们在给定任何搜索表单输入的情况下动态搜索我们的数据库。

这篇文章的重点不是构建复杂的 Arel 查询，而是制定一个设计模式，允许我们以一种可组合和灵活的方式利用 Arel。这种模式在很大程度上受到了我的同事 T2 的启发。

## Arel 是什么？

Arel 是一个用于 Ruby 的 SQL AST(抽象语法树型)管理器。它允许我们以语义的、可重用的方式编写复杂的 SQL 查询。Arel 是“框架框架”；它旨在优化数据库兼容性上的对象和集合建模。例如，活动记录建立在 Arel 之上。

Arel 将我们的数据域映射成树状结构。例如，我们可以通过调用:
来获取与用户相关的数据模型部分的树状表示

```
User.arel_tabel

=> #<Arel::Table:0x007fd42da94350
 @aliases=[],
 @columns=nil,
 @engine=
  User(id: integer, name: string),
 @name="user",
 @primary_key=nil,
 @table_alias=nil> 
```

在我们的`Arel::Table`实例中，我们可以使用[谓词](http://www.rubydoc.info/github/rails/arel/master/Arel/Predications)来执行查询，比如:

```
users = User.arel_table
User.select(users[Arel.star]).where(users[:id].eq(1)) 
```

可以像散列一样操作`Arel::Table`的实例，散列的键代表表上的每一列。每一列都表示为一个`Arel::Attributes::Attribute`。Arel 在这些属性对象上公开了一组谓词方法，我们可以调用这些方法来构造查询。

```
users = User.arel_table

users[:name]
 => #<struct Arel::Attributes::Attribute
 relation=
  #<Arel::Table:0x007fd42da94350
   @aliases=[],
   @columns=nil,
   @engine=
    User(id: integer, name: string, age: integer),
   @name="users",
   @primary_key=nil,
   @table_alias=nil>,
 name=:name> 
```

为了使用谓词方法之一执行 select 语句，`gteq`(大于或等于)看起来像这样:

```
User.select(users[:name]).where(users[:age].gteq(21))

=> SELECT users.name FROM users where users.age >= 21; 
```

## 为什么我们需要可组合性？

您可能会想——“一个允许我编写语义和可重用查询的工具？听起来像是活动记录”。毫无疑问，活动记录已经提供了一个强大的查询工具包。但是，当简单的查询扩展了活动记录的能力范围时，会发生什么呢？

假设您想在数据库中查询年龄超过 21 岁的用户。

您可以在 where 子句中编写一个 SQL 字符串，如下所示:

```
User.where("age >= 21") 
```

当查询变得更加复杂时会发生什么？假设您想要查询年龄超过 21 岁的用户，并且您想要获得他们所有的带有特定区号的电话号码？你可能会得到这样的结果:

```
User
  .select("users.*, phone_numbers.number")
  .joins(:phone_numbers)
  .where("users.age >= 21 AND phone_numbers.area_code = '212'") 
```

但是如果我们想返回没有电话号码的用户呢？由`.joins`方法生成的内部连接不会这样做。我们需要做出改变:

```
User
  .select("users.*, phone_numbers.number")
  .joins("LEFT OUTER JOIN users on phone_numbers where user.id = phone_numbers.user_id")
  .where("users.age >= 21") 
```

随着我们的查询变得越来越复杂，我们不得不使用越来越多的原始 SQL。这种方法有一些缺点。

*   可重用性:我们可能会发现自己需要在代码的其他部分查询 21 岁以上的用户，而不需要查找他们的电话号码。组成上述查询的子查询在当前形式下是*不*可重用的。这种方法不是很枯燥。

*   可读性:我不了解你，但是我不喜欢阅读 SQL 的长字符串，尤其是在我的 Ruby 类的上下文中。Ruby 是一种雄辩的语义语言，让我们使用它。更不用说我们*不会*在这个上下文中得到我们的 SQL 字符串的语法验证，因为这些字符串不会被文本编辑器解释为 SQL，而是可以包含任何内容的 Ruby 字符串。

Arel 为我们回答了这两个问题。它允许我们甚至将最复杂的 SQL 查询*而不是*写成 SQL 字符串，而是使用 Arel DSL 在 Ruby 中编写。除此之外，它允许我们将大而复杂的查询分解成子查询，我们可以一次又一次地重复使用。

Arel 中的相同查询更加语义化和灵活:

```
users           = User.arel_table
phone_numbers   = PhoneNumber.arel_table

left_outer_join = users
                    .join(phone_numbers, Arel::Nodes::OuterJoin)
                    .on(users[:id].eq(phone_numbers[:user_id]))
                    .join_sources

User
  .select([users[:name], phone_numbers[:number]])
  .joins(left_outer_join)
  .where(users[:age].gteq(21).and(phone_numbers[:area_code].eq("212"))) 
```

虽然这乍一看似乎有些冗长(我承认 Arel 语法可能有点多)，但我们已经可以感觉到如何将它分解成可重用的组件。例如，我们可以将`left_outer_join`组合抽象成一个助手方法，该方法可以从任何关联的表中构造一个左外部连接。我们可以将`select`语句拆分成它自己的帮助器方法，并构建另一个帮助器方法来包含查询。这是我们将使用自己的查询构建器类创建的一种组合性。

现在我们对 Arel 及其提供的优势有了基本的了解，让我们用它在 Rails 中构建一个可组合的查询构建器类。给定一组表单输入，我们的查询生成器将能够动态搜索我们的数据库。

## App

我们的应用程序非常简单——一个瑞克和莫蒂对话生成器。“Meeseeks Box”应用程序有一个数据库，里面全是里克和莫蒂的场景。

[![](img/da1fc9cd0886b57f4f4032e5da861c7d.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--RwAqjkCO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.thegreatcodeadventure.com/conteimg/2017/12/Screen-Shot-2017-12-13-at-12.14.52-PM.png) *你想用 Arel 构建一个复杂的查询生成器？？Caaaan dooo！
[来源](https://fsmedia.imgix.net/5d/4f/bd/56/9b71/425e/aae8/a38a9d8d5bef/mr-meeseeks-can-get-a-bit-stressed-if-he-lives-for-too-long.jpeg)*
每一个场景都有季号、插曲号和对话属性。我们的用户可以填写任何或所有指定季节，剧集或对话关键字的表格。

我们需要构建一个查询构建器类，它能够通过任意或所有这些属性搜索场景表。

### 查询生成器

我们将调用查询构建器`SceneQueryBuilder`，并在`app/services/`中定义它。

#### 基数查询

我们的查询构建器将接受表单输入生成的`Scene`实例的参数，并使用它来查询满足条件集的 _all_scenes。我们将定义我们的`base_query`来使用 Arel 到`SELECT * FROM scenes`。

```
class SceneQueryBuilder
  attr_reader :query, :scene

  def initialize(scene)
    @scene = scene
    @query = base_query
  end

  def base_query
    Scene.select(scenes[Arel.star])
  end

  def scenes
    Scene.arel_table
  end
end 
```

### 子查询

接下来，我们将构建一些方法，使用 Arel 查询表单中可能出现的每个属性:季节、剧集或对话关键字。

```
class SceneQueryBuilder
  attr_reader :query, :scene

  def initialize(scene)
    @scene = scene
    @query = base_query
  end

  def base_query
    Scene.select(scenes[Arel.start])
  end

  def scenes
    Scene.arel_table
  end

  def by_season
    query.where(scenes[:season].eq(scene.season))
  end

  def by_episode
    query.where(scenes[:episode].eq(scene.episode))
  end

  def by_dialogue
   query.where(scenes[:dialogue].matches(scene.dialogue))
  end
end 
```

现在我们可以像这样使用我们的查询构建器:

```
scene = Scene.new(scene_params)
SceneQueryBuilder.new(scene).by_season
SceneQueryBuilder.new(scene).by_episode
SceneQueryBuilder.new(scene).by_dialogue 
```

#### 用`#reflect`链接查询方法

尽管如此，我们仍然不能轻松地组合我们的查询。我们表单的全部目的是允许用户通过这些属性的任意组合进行搜索。

让我们的查询构建器能够将这些查询方法链接在一起。

我们希望能够通过子查询方法调用的任意组合或顺序来完成以下任务:

```
SceneQueryBuilder.new(scene).by_season.by_episode.by_dialogue 
```

为了实现这一点，每个子查询方法必须:

*   返回一个`SceneQueryBuilder`的实例
*   保持对上一个查询的了解

#### 动态构建查询生成器

我们将定义一个方法`#reflect`,它将产生查询构建器类的一个新实例。我们还将修改我们的类，用查询的第二个参数进行初始化。

```
class SceneQueryBuilder
  attr_reader :query, :scene

  def initialize(scene, query=nil)
    @scene = scene
    @query = query || base_query
  end

  ... 
```

我们修改过的`#initialize`方法允许我们传入一个可选的查询，并使用这个查询作为我们链接后续子查询的基础。

我们来分解一下:

```
initial_query = SceneQueryBuilder.new(scene)
second_query  = SceneQueryBuilder.new(scene, initial_query).by_season

SceneQueryBuilder.new(scene, second_query) 
```

这具有将我们的子查询链接在一起的效果。如果你问我的话，我觉得这个 API 不是很优雅。

为了实现真正的方法链接，我们需要教会每个子查询方法返回查询构建器的一个实例。

我们将定义一个方法`#reflect`，它接受我们添加到链中的查询的一个参数，并使用它来初始化一个新的`SceneQueryBuilder`实例。

```
class SceneQueryBuilder
  ...
  def reflect(query)
    self.class.new(scene, query)
  end 
```

最后，我们将在每个子查询方法中使用这个方法:

```
class SceneQueryBuilder
  attr_reader :query, :scene

  def initialize(scene)
    @scene = scene
    @query = base_query
  end

  def base_query
    Scene.select(scenes[Arel.start])
  end

  def scenes
    Scene.arel_table
  end

  def by_season
    reflect(query.where(scenes[:season].eq(scene.season)))
  end

  def by_episode
    reflect(query.where(scenes[:episode].eq(scene.episode)))
  end

  def by_dialogue
    reflect(query.where(scenes[:dialogue].matches(scene.dialogue))) 
  end

  def reflect(query)
    self.class.new(scene, query)
  end
end 
```

每个子查询方法调用当前查询上的新查询——存储在`#query` attr_reader 中——并将结果查询传递给`#reflect`。Reflect 获取这个结果查询，并使用它来初始化和返回一个新的`SceneQueryBuilder`实例，其中`#query`现在被设置为由所讨论的子查询方法组成的查询。

现在，我们可以像这样链接我们的方法调用:

```
SceneQueryBuilder.new(scene).by_episode.by_dialogue.by_season 
```

每个子查询方法都返回一个`SceneQueryBuilder`的实例，它反过来将响应链中的下一个子查询方法。因此，每次链接的方法调用都会添加基本查询。

#### 使用`#inject`链接查询方法

我们几乎完成了我们的查询构建器。但是它并没有想象中的那么灵活。我们现在可以将子查询方法的任意组合链接在一起。但是我们如何知道调用哪些方法呢？请记住，我们传递到查询构建器中的场景对象是用表单参数中的属性创建的:

```
scene = Scene.new(scene_params)
SceneQueryBuilder.new(scene)
... 
```

场景对象可以具有剧集、季节或对话属性的任意组合。如果场景有一个`episode`属性，我们需要调用`by_episode`方法；如果场景有一个`season`属性，我们需要调用`by_season`方法；如果场景有一个`dialogue`属性，我们需要调用`by_dialogue`方法。我们*可以*写一些条件逻辑来适应这个:

```
results = SceneQueryBuilder.new(scene)
if scene.episode
  results = results.by_episode
end

if scene.season
  results = results.by_season
end

if scene.dialogue
  results = results.by_dialogue
end 
```

在我看来，这种方法有一些缺陷。

*   这是严格的——我们对每个属性都有一个硬编码的`if`条件。如果我们将来添加属性和查询方法，我们将需要编写一个新的`if`条件。
*   它很冗长——它不允许我们在一行中利用我们优雅的方法链接。

我们需要找到一种方法，根据场景对象的属性动态链接这些方法。

我们将做两件事:

*   识别并收集我们希望基于场景属性调用的子查询方法
*   使用`#inject`迭代这些方法

我们将把这个逻辑封装在一个面向公众的`.build_query`方法中:

```
attr_reader :query, :scene

  def self.build_query(scene)
    query_conditions = collect_query_methods(scene)
    query_builder    = new(scene)
    query_conditions.inject(query_builder) { |query_builder, method| query_builder.send(method) }.query
  end

  def self.collect_query_methods(scene)
    scene.attributes.keys.map do |key|
      "by_#{key.to_s}" if scene.attributes[key] && !scene.attributes[key].empty?
    end.compact
  end
end 
```

方法将迭代场景的属性散列。如果给定属性的值不是`nil`或空字符串，它将收集相应的子查询方法名。换句话说，如果场景有一个`episode`属性，它会将`"by_episode"`添加到收集的数组中。

结果，我们得到了一个子查询方法名的数组，我们需要在查询构建器中调用它。例如:

```
["by_season", "by_dialogue"] 
```

然后，我们使用`#inject`迭代这个方法名集合。`#inject`方法是一个枚举器，接受初始值或对象的参数。它对集合进行迭代，并为块生成两个参数:

*   当前正在迭代的集合元素
*   “累加器”，它从初始值开始，并成为在迭代的前一步由块返回的任何值

我们将全新的`SceneQueryBuilder`实例传递给`#inject`；告诉`#inject`通过调用`#send`来动态调用给定的子查询方法；并将该方法调用的结果(即，具有构建的查询的新的 SceneQueryBuilder 实例)传递给迭代的下一步。

`#inject`迭代的结果是一个具有`query`属性的`SceneQueryBuilder`实例，它是链接`by_episode`和`by_season`查询的结果:

```
SceneQueryBuilder.new(scene).by_episode.by_season 
```

我们可以在那个对象上调用`#query`来显示我们的结果。

如果我们把它们放在一起，我们可以像这样使用我们的`SceneQueryBuilder`超级动态:

```
scene = Scene.new(scene_params)
SceneQueryBuilder.build_query(scene) 
```

就是这样！

## 结论

这篇文章不太关注构建复杂的 Arel 查询——尽管我鼓励你利用这个主题的资源。主要的收获是我们在这里建立的模式。我们构建了一个完全动态的查询构建器类——给定一些搜索输入，它可以使用我们构建的构件构建自己的查询。搜索功能越复杂，这种模式就越有用。

编码快乐！