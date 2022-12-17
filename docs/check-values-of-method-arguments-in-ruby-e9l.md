# 检查 Ruby 中方法参数的值

> 原文：<https://dev.to/siman/check-values-of-method-arguments-in-ruby-e9l>

我尝试它。

```
method(__method__).parameters.map { |t, v| [v, eval(v.to_s)] }.to_h 
```

Enter fullscreen mode Exit fullscreen mode

示例

```
def foo(a = 3, b = 'hello', *test)
  p method(__method__).parameters.map { |t, v| [v, eval(v.to_s)] }.to_h
end

foo #=> {:a=>3, :b=>"hello", :test=>[]} 
```

Enter fullscreen mode Exit fullscreen mode