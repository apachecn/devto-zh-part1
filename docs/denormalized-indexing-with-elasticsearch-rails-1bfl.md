# 具有弹性搜索轨道的非规范化索引

> 原文：<https://dev.to/felipeelias/denormalized-indexing-with-elasticsearch-rails-1bfl>

使用 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 的最大优势之一是它速度快，即使你有非常复杂的文档，其中许多属性来自不同的模型。

实现这一点需要你[将那些模型](https://en.wikipedia.org/wiki/Denormalization)反规范化成一个单一的`index`，作为开发者，你有责任保持它的一致性。

这揭示了一些有趣的挑战，让我们来看看。

## 5 分钟后博客

假设我们在`Posts`和`Authors`之间有一个简单的 has_many/belongs_to 关系。我们的最终目标是能够通过名字搜索特定作者的*帖子。*

假设您有一个安装了 [elasticsearch-rails](https://github.com/elastic/elasticsearch-rails) 并运行 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 的基本 Rails 应用程序，我们的模型将如下所示:

```
# db/migrate/...
class CreateModels < ActiveRecord::Migration[5.1]
  def change
    create_table :authors do |t|
      t.string :name
      t.string :email
      t.timestamps
    end

    create_table :posts do |t|
      t.string :title
      t.text :body
      t.datetime :published_at
      t.references :author, foreign_key: true
      t.timestamps
    end
  end
end

# models/author.rb
class Author < ApplicationRecord
  has_many :posts
end

# models/post.rb
class Post < ApplicationRecord
  include Elasticsearch::Model
  include Elasticsearch::Model::Callbacks

  belongs_to :author
end 
```

Enter fullscreen mode Exit fullscreen mode

`Post`模型将是我们管理指数变化的切入点。我不打算详细说明 [elasticsearch-rails](https://github.com/elastic/elasticsearch-rails) gem 是如何工作的，你可以在 [github 库](https://github.com/elastic/elasticsearch-rails)上查看它的文档。

假设您导入了所有帖子和用户，您可以使用:
执行全文搜索

```
Post.search('example').records.all 
```

Enter fullscreen mode Exit fullscreen mode

这将允许您搜索`Post`模型中的每个属性，但不是通过`author`名称。

## 延伸

现在是有趣的部分。让我们在与`posts`相同的索引中添加`author`信息。这将帮助我们实现通过`author`名字搜索的目标。

您需要定义一个自定义映射，以及如何通过覆盖`#as_indexed_json`方法:
来索引它

```
class Post < ApplicationRecord
  # ... snipped

  mapping dynamic: :strict do
    indexes :id,           type: :long
    indexes :title,        type: :text
    indexes :body,         type: :text
    indexes :published_at, type: :date
    indexes :created_at,   type: :date
    indexes :updated_at,   type: :date
    indexes :author do
      indexes :id,   type: :long
      indexes :name, type: :text
    end
  end

  def as_indexed_json(options = {})
    self.as_json(
      options.merge(
        only: [:id, :title, :body, :published_at, :created_at, :updated_at],
        include: { author: { only: [:id, :name] } }
      )
    )
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

更改之后，您必须**重新创建**索引并且**重新导入**数据:

```
Post.__elasticsearch__.create_index!(force: true)
Post.import 
```

Enter fullscreen mode Exit fullscreen mode

## 然后，有一个 bug🐞

如果你使用你的应用程序一段时间，你会注意到如果你改变一篇文章的`author`，这种改变不会反映在 Elasticsearch 中。

经过调试， [elasticsearch-rails](https://github.com/elastic/elasticsearch-rails) gem 只对通过 [ActiveModel::Dirty](http://api.rubyonrails.org/classes/ActiveModel/Dirty.html) 模块改变的属性进行索引。这在我们的例子中不起作用，因为`author`不是`post`的属性。

简单来说，当你修改一个`post`的`author`时，改变的属性就是`author_id`。在你保存了`post`之后，gem 会根据`#as_indexed_json`返回的散列来比较哪些属性发生了变化。因为我们的更改现在被表示为一个`author`散列，所以 gem 在那里找不到`author_id`。

有几种方法可以解决这个问题:

1.  放下`Elasticsearch::Model::Callbacks`模块，然后自己处理索引逻辑
2.  每当`author_id`改变时，通过添加`author`键作为改变来强制改变
3.  完全忽略所有更改

我选择解决方案#2，它看起来像这样:

```
class Post < ApplicationRecord
  # ... snipped

  before_save :force_index

  def force_index
    if changes['author_id']
      attr = :@__changed_model_attributes
      old_changes = __elasticsearch__.instance_variable_get(attr)
      __elasticsearch__.instance_variable_set(attr, old_changes.merge!('author' => true))
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这是一个黑客，它改变了 [elasticsearch-rails](https://github.com/elastic/elasticsearch-rails) gem 的内部结构，我对这个解决方案不是很满意。我这样做是为了保持仅索引已更改属性的功能，但是，维护起来会很麻烦。

如果你不关心这个优化，你可以用#3 和**总是**通过清除`@__changed_model_attributes`实例变量:
来强制索引

```
def force_index
  __elasticsearch__.instance_variable_set(:@__changed_model_attributes, nil)
end 
```

Enter fullscreen mode Exit fullscreen mode

无论采用哪种方法，如果您更改`author`，这些更改都会反映在 Elasticsearch 中。

## 然后，有两个 bug🐞🐞

根据上一个错误的提示，你会注意到改变作者的名字也不会影响到 [Elasticsearch](https://www.elastic.co/products/elasticsearch) ！这是因为`Author`模型不知道如何将自己索引为`Post`索引。

这就是保持索引一致性变得棘手的地方。有许多方法可以解决这个问题，每种方法都有自己的缺点。为了简单起见，我将推荐一个工作良好的解决方案，除了 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 本身之外，不使用任何其他依赖项🎉🎉🎉。

我们将使用`#update_by_query`特性，顾名思义，它允许您更新与查询匹配的各种文档。它有一些很酷的特性，比如能够异步工作(T2)，按照自己的速度更新文档，而不会让集群过载(T4)，处理冲突(T6)。点击查看[文档。](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/docs-update-by-query.htm)

让我们利用这一点在后台更新属于特定`author`的所有帖子:

```
# models/author.rb
class Author < ApplicationRecord
  after_commit :update_relations

  private

  def update_relations
    Post.update_authors(self)
  end
end

# models/post.rb
class Post < ApplicationRecord
  # ... snipped

  def self.update_authors(author, options = {})
    options[:index] ||= index_name
    options[:type]  ||= document_type
    options[:wait_for_completion] ||= false

    options[:body] = {
      conflicts: :proceed,
      query: {
        match: {
          'author.id': author.id
        }
      },
      script: {
        lang:   :painless,
        source: "ctx._source.author.name = params.author.name",
        params: { author: { name: author.name } }
      },
    }

    __elasticsearch__.client.update_by_query(options)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

代码非常简单明了。`Author`模型中的任何变化都将触发一个`#update_by_query`,它对所有匹配查询的帖子执行更新:

```
query: {
  match: {
    'author.id': author.id
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于每一场比赛，它将执行定义的*脚本更新*，简单地将`author`名称设置为在`params` :
中指定的名称

```
script: {
  lang:   :painless,
  source: "ctx._source.author.name = params.author.name",
  params: { author: { name: author.name } }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能希望优化`#update_relations`方法，只在必要时调用`#update_authors`。使用`params`可以让您在将来轻松地包含更多的属性，还可以避免在`source`中串联字符串带来的潜在安全问题。

将`wait_for_completion`设置为`false`将告诉 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 异步执行更新。如果有一个`author`有大量帖子的潜在情况，这是很好的。

## 思考冲突

你可能已经注意到了，我在更新的主体中设置了`conflicts: :proceed`。这是为了处理几种情况:

### 帖子更新

想象一下这样一种情况，我们更新了一个拥有亿万帖子的作者的名字。那需要一段时间...在此期间，该作者的任何帖子都有可能被其他人更新。

在运行`#update_by_query`、 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 之前，会对索引进行快照，并使用内部版本控制方案来识别此类冲突。如果一个`post`在更新“排队”之后和“运行”之前被更新，`post`将会有一个新的版本，所以对于那个`post``#update_by_query`将会失败。在这种情况下，我们希望**跳过**这样的冲突并继续。

这意味着*最后一次更新赢得了*,我们保证帖子将具有作者姓名的最新值。

### 对同一作者的多次更新

如果有人更新了`author`一次，然后立即后悔这个决定，并再次更新为其他内容，那么第一次更新有可能仍然在运行(考虑到大量的帖子)。如果是这样，第一次更新就会遇到冲突，忽略它们，继续前进。

理论上，第二次更新总是会赢，因为它会在第一次更新之后。

## 结论

[反规范化](https://en.wikipedia.org/wiki/Denormalization)数据可以帮助你利用 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 快速查询特性，但是它的代价是必须处理多个模型的并发更新，这揭示了一些非常难以调试的问题和不一致性。

请注意，这是一个非常简单的场景，如果你除此之外没有其他东西，你可能不需要 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 。然而，当您必须在同一个文档中索引许多不同的模型，并且在数据库中进行连接变得非常困难时，最大的优势就来了。

* * *

原载于 2017 年 12 月 27 日[媒](https://medium.com/@felipeelias/denormalized-indexing-with-elasticsearch-rails-1c4549d7d393)。