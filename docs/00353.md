# 来吧，德伯格勒

> 原文：<https://dev.to/burdettelamar/comes-now-debughelper-3ien>

我不知道我输入了多少次这样的代码(进行 printf 风格的调试):

```
my_hash.each_pair do |key, value|
  p [key, value]
end 
```

Enter fullscreen mode Exit fullscreen mode

我终于明白了，并建立了一个助手方法来支持这一点:

```
DebugHelper.printf(data) 
```

Enter fullscreen mode Exit fullscreen mode

该方法允许任何数据，并且特别支持类似哈希和类似数组的结构。

它还允许一个可选的名称(默认为数据类名)和消息。

典型输出:

```
Hash (size=3)
  a => 0
  b => 1
  c => 2

Array (size=3)
  0: a
  1: b
  2: c 
```

Enter fullscreen mode Exit fullscreen mode

这是我的助手类:

```
# Class to help in 'printf' debugging.
class DebugHelper
  def self.printf(data, name = data.class.to_s, description = '')
    size = data.respond_to?(:size) ? data.size : 1
    puts format('%s (size=%d) %s', name, size, description)
    case
      when data.respond_to?(:each_pair)
        # Hash-like.
        data.each_pair do |k, v|
          puts format('  %s => %s', k, v)
        end
      when data.respond_to?(:each_with_index)
        # Array-like or Set-like.
        data.each_with_index do |v, i|
          puts format('  %6d: %s', i, v)
        end
      else
        puts format('  %s', data.inspect)
    end
    nil
  end
end 
```

Enter fullscreen mode Exit fullscreen mode