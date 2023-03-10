# Rails 中如何建立关联

> 原文：<https://dev.to/farisj/how-associations-are-built-in-rails-2en1>

作为 web 开发人员，我们工作的核心组成部分之一是找出管理用户数据的有效方法。

在 Rails 中，作为 web 开发人员，Active Record 为我们提供了许多方法来维护和管理 Rails 中数据点之间的关系。在处理数据时，它有多种端点，将这种功能包装到服务于我们目的的对象中。

然而，我们需要真正理解这些工具，然后才能像 web 应用程序不时需要的那样熟练地使用它们。很多时候，让 Rails 抽象出 ORM 的魔力和创建数据之间的关系很容易，但是如果我们想真正理解 Rails 在幕后做什么呢？

[![Even Steve from Blue's Clues needs to decide the right tool for the job.](img/ba0797ae784ab884347a3ecc0d79153e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nd2c3RX_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/63AnIA6.jpg)

这篇文章将讨论`ActiveRecord::Association`并概述在这个框架中关系方法是如何产生的。

数据点之间可以有几种不同的关系。这些关系中最常见的是:

*   `belongs_to`
*   `has_one`
*   `has_many`
*   `has_and_belongs_to_many`

这篇文章假设你对这些关系有基本的了解。

我们大多数人都以某种方式熟悉 Rails 中的这些方法。但是，当我们将这些行添加到 Activerecord 类中时，实际上会发生什么呢？

当 Rails 启动时，ActiveRecord 模型被加载到内存中，一点元编程的魔力就开始了。当一个模型包含上述方法之一时，会触发一系列方法来构造处理这些关系的方法。考虑我们有两个模型，`User`和`Post`。

```
class User < ActiveRecord::Base
  has_many :posts
end

class Post < ActiveRecord::Base
  belongs_to :post
end 
```

我们操作这两个模型之间关系的主要方式是使用这些模型给我们的访问器方法。这些访问器方法是通过一系列事件构建在 Rails 中的——让我们看看调用堆栈中的一些方法。

我们从`ActiveRecord::Associations::Builder::Association`类开始，它是由`CollectionAssociation`(用于`has_many`和`has_and_belongs_to_many`)和`SingularAssociation`(用于`belongs_to`和`has_one`)继承的父类。它包含一个方法`self.build`。源代码如下:

```
def self.build(model, name, scope, options, &block)
  if model.dangerous_attribute_method?(name)
    raise ArgumentError, "You tried to define an association named #{name} on the model #{model.name}, but " \
                         "this will conflict with a method #{name} already defined by Active Record. " \
                         "Please choose a different association name."
  end

  extension = define_extensions model, name, &block
  reflection = create_reflection model, name, scope, options, extension
  define_accessors model, reflection
  define_callbacks model, reflection
  define_validations model, reflection
  reflection
end 
```

所以，这里发生了很多事情——确保方法名是正确的，定义一些东西，一般的 rails 神把戏。让我们来关注一下`define_accessors`。Rails 中的反射是一个对象，它允许系统确定该对象应该具有什么类型的关联(这不是本文的重点)。这将产生类似于`post.user`和`user.posts=`的东西。让我们看看这个方法，也是在这个关联类中:

```
def self.define_accessors(model, reflection)
  mixin = model.generated_association_methods
  name = reflection.name
  define_readers(mixin, name)
  define_writers(mixin, name)
end 
```

正如我们所看到的，我们在这里打破了读者和作者的定义。这些方法看起来是这样的(我们还是一个班的):

```
def self.define_readers(mixin, name)
  mixin.class_eval <<-CODE, __FILE__ , __LINE__ + 1 def #{name}(*args)
      association(:#{name}).reader(*args)
    end
 CODE
end

def self.define_writers(mixin, name)
  mixin.class_eval <<-CODE, __FILE__ , __LINE__ + 1 def #{name}=(value)
      association(:#{name}).writer(value)
    end
 CODE
end 
```

这里有一点元编程——这些方法定义了正在讨论的类的一个新方法。`reader`方法将返回一个包含一组对象数据的`CollectionProxy`(对于非单一关联)，或者返回对象本身。被调用的 writer 方法实际上委托给了一个 replace 方法，如下所示:

```
def writer(records)
  replace(records)
end 
```

现在，`replace`方法和调用栈的其余部分从这里开始变得相当复杂(这不是本文的目的)，但是要知道，`replace`方法最终会向您的数据库发送一个查询来更新 id 并反映您在数据库中所做的修改。

其核心是一些相对简单的元编程，用于创建所有这些方法。当然，内部细节可能会变得相当复杂(反射到底是做什么的？在单数和集合关联之间，这些事情是如何被不同地处理的？)，但是这个元编程的主要思想很容易理解。

希望这能从 Rails 的魔力中揭开一点神秘的面纱！