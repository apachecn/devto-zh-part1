# 让你的伪代码成为你真正的代码

> 原文：<https://dev.to/cannikin/make-your-pseudocode-your-real-code-96n>

如果你已经写了一段时间的代码，你可能会遇到伪代码的概念:一个类、对象或算法的快速“模型”,它使用人类友好的快捷方式来理解你正在做的事情的概念。

下面是一些伪代码，用于根据用户的登录状态和权限决定是否向用户显示一个按钮(以及哪个按钮):

```
if logged_in?
  if user_can_create_post?
    show_create_post_button
else
  show_login_button 
```

Enter fullscreen mode Exit fullscreen mode

每一步发生了什么是非常清楚的:你想当然地认为“登录？”如果用户已经登录，将返回`true`，如果用户没有登录，将返回`false`。在这一点上，我们不关心“登录”意味着什么的细节，因为它在这段代码试图做什么的大画面中并不重要。

当我们试图让一个概念被理解时，我们使用伪代码是有原因的:这是一种向某人，甚至是非技术人员演示一些代码如何工作的非常低开销的方式。

在真实代码(在本例中是 Ruby)中的一个可能实现如下:

```
if session[:user].present? && current_user = User.find(session[:user])
  if current_user.permissions.find_by(:name => 'can_create_post').present?
    render :partial => 'button', :label => 'Create Post'
  end
else
  render :partial => 'button', :label => 'Login'
end 
```

Enter fullscreen mode Exit fullscreen mode

这个管用。当你在写的时候，你可能已经很清楚了。但是 6 个月后再遇到它，或者把它展示给一个新的开发人员，你必须停下来看看每个方法调用，花费一些脑力把每个步骤翻译成它在你的应用环境中实际做的事情

```
# does user have a user ID in session?
session[:user].present? 

# is this a real user ID in the database? If so, save it as a local variable `current_user`
current_user = User.find(session[:user]) 

# does user have a permission record in the database?
current_user.permissions.find_by(:name => 'can_create_post').present? 

# show a little view snippet for an HTML <button> with the given text label
render :partial => 'button', :label => 'Create Post' 
```

Enter fullscreen mode Exit fullscreen mode

我认为许多开发人员没有意识到(或者假装他们不在乎)他们花了多少时间在脑子里一遍又一遍地转换这样的语句。如果你能简化这个过程，甚至完全消除它，为什么不呢？

那段伪代码非常清楚...如果我们的*真正的*代码解析和理解如此简单会怎么样？我们能走多近？(合理的警告:有些语言会使这个练习比其他语言更容易。鲁比尤其如此。如果你正在用一种强类型的语言编码，这将很难避免类型声明的仪式，这将使代码更加“繁忙”。)

(另外:如果你不知道，Ruby 会自动返回方法调用中的最后一条语句，所以我们在这些新方法中不需要任何显式的`return`语句。)

```
def show_action_button
  if logged_in?
    if can_create_post?
      show_create_post_button
    end
  else
    show_login_button
  end
end

def logged_in?
  current_user.present?
end

def current_user
  if session[:user].present?
    User.find(session[:user])
  else
    nil
  end
end

def can_create_post?
  current_user.present? and current_user.permissions.find_by(:name => 'can_create_post').present?
end

def show_create_post_button
  show_button('Create Post')
end

def show_login_button
  show_button('Login')
end

def show_button(label)
  render :partial => 'button', :label => label
end 
```

Enter fullscreen mode Exit fullscreen mode

那是非常接近的。不过，这确实需要更多的代码行。但这是我乐意做的一个折衷——我希望这段代码在将来能被快速理解和编辑。我真的不在乎为了执行它，计算机必须工作得更努力一点，让堆栈更深一点。

我们将所有东西都提取到方法中，这些方法 a)可重用，b)根据它们的用途明确命名，c)只做一件事。我们的主算法`show_action_button`读起来就像我们的原始伪代码。

一些额外的好处:

*   使呈现按钮的代码干涸
*   测试更简单，因为每种方法都可以独立测试
*   如果您在将来的某个时候发现这段代码有问题，代码的各个部分基本上都标有它们做了什么，以便更容易找到

你会遇到各种各样的建议，关于如何将你的代码分解成可重用的方法，干燥一个部分，或者确保你的方法只做一件事。如果你试着写你的算法，使它看起来像伪代码，这些公理自然会产生令人愉快的副作用。

下一次，当你看着一段代码并试图弄清楚它是否可以变得更简单时，想想如何将真正的代码转换成伪代码！

为了进一步阅读，看看 Martin Fowler 的这篇文章,他谈到给你的函数起“意图揭示”的名字，这样你就可以跳过实现细节，更快地理解顶级函数/类的核心含义。