# 获取 Ruby 中最长的方法名

> 原文：<https://dev.to/siman/get-the-longest-method-name-in-ruby-5jl>

它可以得到最长的方法名。

```
p ObjectSpace.each_object(Module).flat_map { |m| m.instance_methods + m.private_instance_methods }.uniq.max_by(&:size) 
```

Enter fullscreen mode Exit fullscreen mode

我在本地机器上试过。(红宝石 2.4.3p205)

```
:fix_syck_default_key_in_requirements 
```

Enter fullscreen mode Exit fullscreen mode