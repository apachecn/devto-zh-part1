# 懒惰的嘶嘶声

> 原文：<https://dev.to/quanon/lazy-fizzbuzz-7gd>

当我正要去洗手间的时候，我突然想到了这个气泡。所以我写了这张纸条😇

```
def genarate_lazy_list(number, yell)
  [*Array.new(number - 1, nil), yell].cycle
end

numbers = 1.step.lazy
fizzes = genarate_lazy_list(3,'Fizz')
buzzes = genarate_lazy_list(5,'Buzz')
fizzbuzzes = genarate_lazy_list(15,'FizzBuzz')

# Add backslash for irb. I prefer leading dots to trailing ones.
numbers \
  .zip(fizzes, buzzes, fizzbuzzes) \
  .map { |n, fizz, buzz, fizzbuzz| fizzbuzz || buzz || fizz || n } \
  .take(30) \
  .each(&method(:puts)) 
```

Enter fullscreen mode Exit fullscreen mode

```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
16
17
Fizz
19
Buzz
Fizz
22
23
Fizz
Buzz
26
Fizz
28
29
FizzBuzz 
```

Enter fullscreen mode Exit fullscreen mode