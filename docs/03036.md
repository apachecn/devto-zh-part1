# 快速获取正则表达式

> 原文：<https://dev.to/wataken44/getting-regex-in-a-quick-way-52o>

创建片段

```
#!/usr/bin/env ruby
# -*- coding: utf-8 -*-

# escape.rb

def main()
    ARGV.each do |arg|
        puts(Regexp.escape(arg))
    end
end

if __FILE__ == $0 then
    main()
end 
```

Enter fullscreen mode Exit fullscreen mode

奔跑

```
$ ./escape.rb "http://www.google.com/"
http://www\.google\.com/ 
```

Enter fullscreen mode Exit fullscreen mode

:D