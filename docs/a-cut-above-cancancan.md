# 上图:康康康

> 原文：<https://dev.to/evanrblack/a-cut-above-cancancan>

开发具有多种用户类型的 web 应用程序会导致大量令人头疼的授权问题。试图提出我自己的解决方案导致了控制器中的大量逻辑，甚至是多种用户类型共享的视图中的更笨拙的逻辑，这些视图需要细微的差别。这种混乱还扩展到多个文件，使情况变得更糟。

受够了，我找到了一个叫做[康康康](https://github.com/CanCanCommunity/cancancan)的宝石，它让授权变得简单多了。CanCanCan 通过直观的 DSL 将所有检查集中在一起，而不是分散在代码库中的大量检查。

我在下面设置了一个典型的例子。我以前通常会解释一段代码，但我认为 CanCanCan 的 DSL 非常容易阅读，除了几个注释之外，它不需要任何东西。

```
# app/models/ability.rb

class Ability
  include CanCan::Ability

  def initialize(user)
    alias_action :create, :read, :update, :destroy, to: :crud

    if user.is_admin? or user.is_moderator?
      # Admins and mods can CRUD any post
      can :crud, Post
    else
      # Normal users can CRUD their own post
      can :crud, Post, user_id: user.id
      # But they can only read others
      can :read, Post
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
# app/controllers/posts_controller

class PostsController < ApplicationController
  load_and_authorize_resource
  # Your normal controller methods go here
end 
```

Enter fullscreen mode Exit fullscreen mode

这就是控制器所能做到的。但是风景呢？

假设您希望只有管理员和版主才能看到帖子的删除按钮(在帖子部分)。

```
<%# app/views/posts/_post.html.erb %>

<% if can? :destroy, post %>
  <%= link_to 'Delete', post, method: :delete, class: 'btn btn-danger' %>
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

这比在不了解检查的上下文的情况下检查用户的类要好得多。

不过，需要记住的是:CanCanCan 的自动化依赖于标准的 Rails 命名实践。然而，如果你发现自己有点违反标准，gem 提供了大量的选项来处理几乎任何情况。

我不能向任何设计多种用户类型的 web 应用程序的人推荐这种宝石。康康康是真正的宝石中的宝石，尤其是当你使用[设计](https://github.com/plataformatic/devise)的时候。

*这是来自[我的博客](https://www.erblack.com/)的交叉帖子。*