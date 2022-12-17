# 你好，世界

> 原文：<https://dev.to/asuenami/hello-world-c20>

```
class Person
  attr_reader :first_name, :last_name

  def initialize(first_name, last_name)
    @first_name = first_name
    @last_name = last_name
  end

  def full_name
    "#{first_name}  #{last_name}"
  end

  def say_hello
    puts "hello, world! hello, dev.to! My name is #{full_name}"
  end
end

me = Person.new('Akira', 'Suenami')
me.say_hello 
```

Enter fullscreen mode Exit fullscreen mode