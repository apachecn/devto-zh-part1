# Ruby 中“鸭子打字”的真实例子

> 原文：<https://dev.to/tra/a-real-world-example-of-duck-typing-in-ruby>

```
def get_first_element(object)
    object[0]
end 
```

在这里，我定义了一个 Ruby 方法，它可以接受任意对象并对其调用[]方法。

现在，通常您在数组上调用[]方法，因此该方法倾向于像预期的那样处理数组。

```
array = [1,2,3]
get_first_element(array)
#=>1 
```

但是它不仅仅处理数组。事实上，它处理任何响应[]方法的任意对象。

```
string = "Alphabet"
get_first_element(string)
#=>"A" 
```

只要对象响应[]方法，对我来说就足够了。而这就是**鸭分型**的基础。它基于这样的原则:如果一个元素“像鸭子一样嘎嘎叫”，那么它就是一只鸭子。如果它像数组一样嘎嘎叫，它就是数组。如果我传入一个不支持[]方法的对象，我们只会看到一个错误，就像这样...

```
awesome_object = Object.new
get_first_element(awesome_object)
#=>NoMethodError: undefined method `[]' for #<Object:0x007f9a93135b70> 
```

该错误消息可能对能够访问
源代码的程序员最有帮助

```
 , but at least the program halted at execution.

And that's all I have for now. I'll leave you with these cool code snippets of me getting the first element of integers. Happy coding.

```ruby
integer = 1
get_first_element(integer)
#=>1 
```

哦...

```
get_first_element(2)
#=>0

get_first_element(30)
#=>0 
```

取消快乐编码。

Ruby 中的整数*也*响应[]方法。[根据文档](http://www.rubydoc.info/stdlib/core/Fixnum:%5B%5D)，整数似乎“在幕后”有一个二进制表示，而[]方法让我得到了

```
 th digit of that binary representation. The docs give a pretty good example of what's going on...

```ruby
#Define a as the integer 13098, by using its binary representation
a = 0b11001100101010

#Retrieve that same binary representation by using []
30.downto(0) { |n| print a[n] }
#=> 0000000000000000011001100101010 
```

毕竟，所有的数字都必须有一个二进制表示，这个二进制表示必须存储在某个地方，你也可以把这个存储的二进制表示当作一个数组。这听起来完全是意料之中的行为，只要你知道要提前预料到。

鸭子呱呱叫，所以我们认为它是一只鸭子。它是一只鸭子...不要误解我。只是一只我不熟悉的鸭子。

巧的是，我居然用了

```
 in a personal side-project. I stayed up all night trying to track down weird bugs that occurred when I was passing integers into 

```get_first_element```

. Once I realized what was causing this, I added a quick hotfix to solve the issue...

```ruby
def get_first_element(object)
    string = object.to_s
    string[0]
end 
```

调用对象的

```
 method, thereby creating a string representation of the object. Then, simply access the first element of that string. This meant that 

```get_first_element```

 handled integers as I **wanted** them to...

```ruby
get_first_element(1)
#=>"1"

get_first_element(2)
#=>"2"

get_first_element(30)
#=>"3" 
```

...但是现在用户会对该方法处理数组的方式感到惊讶。

```
[1,2,3].to_s
#=>"[1,2,3]"

get_first_element([1,2,3])
#=>"[" 
```

以及在旧的
下先前会引发错误的对象

```
 would now produce a "valid" result under the new 

```get_first_element```

, if the objects are able to "quack" (respond to the 

```to_s```

 method)... 

```ruby
awesome_object = Object.new

awesome_object.to_s
#=> "#<Object:0x007f9a9314f3e0>"

get_first_element(awesome_object)
#=>"#" 
```

幸运的是，我个人的兼职项目只处理字符串和整数。如果我的副项目需要支持数组或其他任意对象(考虑到软件变化的频率，这总是有可能的)，我可能会考虑:

*   编写大量丑陋且过于复杂的代码来让`get_first_element`按照我的意图运行(通过自动化测试来形式化我对该方法“应该”如何工作的信念)。-写文件解释和**证明**所有的怪癖。说“事情就是这样，处理好它”是行不通的。-完全删除`get_first_element`并重写副项目。

我害怕不得不采取这些方法。如果被迫选择，我会选择“重写副业”。我的副业正在使用

```
 to do some rather "hacky" stuff, and there's probably a much better (and less painful) way to do that same stuff. All I have to do is find it...and then implement it.
<hr>
This post is not an attack against duck-typing, although my reliance on duck-typing did lead to this error. Duck-typing is part of idiomatic Ruby, and taking advantage of it tends to lead to "cleaner" code. But there are always trade-offs to consider, such as the ducks doing behaviors that you didn't intend them to simply because the ducks knew how to quack. But I never even experienced these trade-offs -- until now.

This blog post was really an illustration of the [Generalized Peter Principle](https://en.wikipedia.org/wiki/Peter_principle): "Anything that works will be used in progressively more challenging applications until it fails." Generally, this principle is usually applied to human beings -- "Workers rise to their level of incompetence." Duck-typing is useful, and it is precisely that it was so useful that it led me to spend the whole night debugging an issue caused by my use of duck-typing.

I'm still going to use duck-typing. It's just too useful and convenient, and the odds of me encountering this issue in another Ruby side-project seems fairly low.

But I'm going to be more cautious and careful when programming. More importantly, I plan to be more comfortable with my ignorance...never assuming that I know more than I actually do about the program I am writing, the patterns that I am using when writing the program, the language I am writing the program in, and the requirements that I am writing the program for.

Being comfortable with ignorance means that I might be able to anticipate and prepare for situations where the Generalized Peter Principle comes true and my tools break hard. 
```