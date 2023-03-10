# 在 Rails 上投票[奖励:作为嘉宾投票！]

> 原文：<https://dev.to/jameschou93/vote-on-railsbonus-vote-as-a-guest-ckf>

作为一名 Rails 开发人员，你很可能已经接到了创建类似博客应用程序的任务(如果你还没有，请告诉我)。最近，我不得不创建一个具有评论、分享和投票功能的网站。通常，我会在运行`rails new`之前花时间规划出模式，但在这种情况下，我时间紧迫。幸好我有 [`act_as_voteable`宝石](https://github.com/ryanto/acts_as_votable)来拯救世界！

安装非常简单，因为您需要做的只是将 gem 包含在您的 gem 文件和捆绑包中:

```
gem 'acts_as_votable', '~> 0.11.1' 
```

并生成+迁移他们的迁移:

```
rails generate acts_as_votable:migration
rake db:migrate 
```

现在我们出发吧！

我最喜欢这个 gem 的一点是，创建投票和显示投票的方法很简单。我们需要做的第一件事是在你想要投票的对象和投票者之间建立联系。在这种情况下，我希望用户对我的帖子进行投票。下面是我的 User.rb 和 Post.rb 文件:

user.rb

```
 # Voter.
  acts_as_voter 
```

post.rb

```
 #Votee
  act_as_votable 
```

既然应用程序知道用户对帖子进行投票，我们需要在帖子控制器中创建 upvote 和 downvote 方法。令人惊叹的是，gem 已经限制了每个用户只能投一票(默认，但允许重复)，如果你愿意，你还可以根据用户角色进行投票加权！我没有在我的应用程序中使用加权投票，但如果你想了解更多关于如何玩投票的其他方法，一定要看看 [ryanto 为他的宝石提供的伟大文档！](https://github.com/ryanto/acts_as_votable)

后置控制器

```
def upvote
    @post = Post.find(params[:id])
    @post.liked_by current_user
    #can change current_user to any other voter
end

def downvote
    @post = Post.find(params[:id])
    @post.disliked_by current_user
    #can change current_user to any other voter
end 
```

在我的 html 视图中:

```
<%= link_to "Like", like_post_path(@post, method: :put) %> 
```

到目前为止，你所阅读的是一个准系统的快速安装教程，绝对有进一步操纵投票的潜力。[参见文档！](https://github.com/ryanto/acts_as_votable)。但我确实发现自己陷入了一个问题...如果我想让客人投票呢？！

通过对各种来源(google、stackoverflow、my sensai 等)的广泛研究，我找到了一种使用会话 id 实现这一点的方法！

这一次，我们必须允许用户和会话充当投票者；我们需要在 votersession.rb 和 user.rb 文件中添加`act_as_voter`。但是首先我们需要生成一个 votersession 模型。

```
rails g migration votersessions session_id:string
rails db:migrate 
```

现在，我们只需更改控制器，使用用户对象进行投票，如果不可用，则使用会话 id。这是我的湿润(不是[干](http://wiki.c2.com/?DontRepeatYourself)

```
 def upvote
    @idea = Idea.find(params[:id])
    # vote as user if logged in
    if current_user
      @idea.upvote_by current_user
    # else vote is associate with session
    else
      @session = VoterSession.find_by(session_id: request.session_options[:id])
    # Create new votersession if unique id does not exist
        if @session == nil
          @session = VoterSession.create(session_id: request.session_options[:id])
        end
      @idea.upvote_by @session
    end 
```

请记住，投票者仍然有机会投票不止一次，例如，如果用户没有登录，他们可以在每次清除 cookies 或使用不同的计算机时再次投票，所以我建议这种方法用于需要快速投票结构的较小应用程序。

我希望这篇文章能对开发者有所帮助，或者至少激发一个想法。如果你有任何问题，请留言！