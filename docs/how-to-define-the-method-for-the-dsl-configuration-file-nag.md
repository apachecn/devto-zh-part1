# 如何定义 DSL 配置文件的方法

> 原文：<https://dev.to/hanachin/how-to-define-the-method-for-the-dsl-configuration-file-nag>

您可以通过细化为 DSL 配置文件 <sup id="fnref1">[1](#fn1)</sup> 定义方法。

例如，下面代码中的`hi`方法只能在这个 Rakefile 中使用，它不应该影响 Rakefile 之外的任何代码。

```
# Rakefile
main = self

using Module.new {
  refine(main.singleton_class) do
    def hi
      puts :hi
    end
  end
}

desc 'hi'
task :hi do
  hi
end 
```

Enter fullscreen mode Exit fullscreen mode

```
% rake hi
hi 
```

Enter fullscreen mode Exit fullscreen mode

## 常见错误 1

下面的代码定义了顶层的方法。

```
# Rakefile
def hi
  puts :hi
end

desc 'hi'
task :hi do
  hi
end 
```

Enter fullscreen mode Exit fullscreen mode

## 常见错误 2

下面的代码定义了顶层的方法。

```
# Rakefile
desc 'hi'
task :hi do
  def hi
    puts :hi
  end

  hi
end 
```

Enter fullscreen mode Exit fullscreen mode

## 常见错误 3

下面的代码定义了顶层的方法。

```
 # Rakefile
m = Module.new do
  def hi
    puts :hi
  end
end

desc 'hi'
task :hi do
  include m
  hi
end 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么要避免在顶层定义方法？

它可能会破坏其他库的代码。

例如:

```
# duck.rb
class Duck < Struct.new(:name)
  def sound
    puts 'quack'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
# Rakefile
require_relative 'duck.rb'

def puts(*)
  print 'Hi! '
  super
end

desc 'hi'
task :hi do
  duck = Duck.new('Donald')
  puts duck.name
  duck.sound
end 
```

Enter fullscreen mode Exit fullscreen mode

```
$ rake hi
Hi! Donald
Hi! quack 
```

Enter fullscreen mode Exit fullscreen mode

## 精益求精的好处

ruby 的细化是词法范围 <sup id="fnref2">[2](#fn2)</sup> 。
因此，精化定义的顶级方法不会破坏其他库代码，完全没问题！

```
# Rakefile
require_relative 'duck.rb'

main = self

using Module.new {
  refine(main.singleton_class) do
    def puts(*)
      print 'Hi! '
      super
    end
  end
}

desc 'hi'
task :hi do
  duck = Duck.new('Donald')
  puts duck.name
  duck.sound
end 
```

Enter fullscreen mode Exit fullscreen mode

```
$ rake hi
Hi! Donald
quack 
```

Enter fullscreen mode Exit fullscreen mode

## 其他方法

将方法和 DSL 包装在一个模块中。

```
# Rakefile
require_relative 'duck.rb'

Module.new {
  def puts(*)
    print 'Hi!, '
    super
  end
  extend(self)

  extend Rake::DSL

  desc 'hi'
  task :hi do
    duck = Duck.new('Donald')
    puts duck.name
    duck.sound
  end
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$ rake hi
Hi!, Donald
quack 
```

Enter fullscreen mode Exit fullscreen mode

* * *

1.  e.g. `Rakefile`、`config/routes.rb`、`unicorn.rb`等-我...。

2.  [https://docs . ruby-lang . org/en/trunk/syntax/refinements _ rdoc . html # label-scope](https://docs.ruby-lang.org/en/trunk/syntax/refinements_rdoc.html#label-Scope)T2】↩