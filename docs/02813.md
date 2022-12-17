# 懒惰的 FizzBuzz (Python 版本。)

> 原文：<https://dev.to/quanon/lazy-fizzbuzz-python-ver-71b>

最近我写了一篇关于 Ruby 的 FizzBuzz 的文章。现在我想用 Python 来做这件事。

在上一篇文章中，我在 Ruby 中使用[枚举器::Lazy](https://docs.ruby-lang.org/ja/latest/class/Enumerator=3a=3aLazy.html) 表示懒惰(也就是对 list 的懒惰求值)，在 Python 中使用[生成器](https://docs.python.org/3/tutorial/classes.html#generators)。

```
import itertools  # what a great tools 😊

class FizzBuzzRule(object):
  @classmethod
  def say(cls, word):
    return cls(word)

  def __init__(self, word):
    self.word = word

  def for_multiples_of(self, number):
    return itertools.cycle((*itertools.repeat('', number - 1), self.word))

numbers = itertools.count(1)
fizzes = FizzBuzzRule.say('Fizz').for_multiples_of(3)
buzzes = FizzBuzzRule.say('Buzz').for_multiples_of(5)

fizzbuzz_generator = (f'{fizz}{buzz}' or n
                      for n, fizz, buzz
                      in zip(numbers, fizzes, buzzes))

if __name__ == '__main__':
  for word in itertools.islice(fizzbuzz_generator, 30):
    print(word) 
```

Enter fullscreen mode Exit fullscreen mode

```
$  python fizzbuzz.py
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