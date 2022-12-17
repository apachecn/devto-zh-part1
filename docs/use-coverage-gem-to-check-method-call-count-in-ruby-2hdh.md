# 使用' coverage` gem 检查 Ruby 中的方法调用计数。

> 原文：<https://dev.to/siman/use-coverage-gem-to-check-method-call-count-in-ruby-2hdh>

`coverage`ruby 2.5 的 gem 可以获得方法覆盖。因此，我编写了一个代码来检查方法调用的计数。

示例代码。(fizzbuzz.rb)

```
def fizz
  "Fizz"
end

def buzz
  "Buzz"
end

def fizzbuzz
  "FizzBuzz"
end

1.upto(100) do |n|
  if n % 15 == 0
    puts fizzbuzz
  elsif n % 3 == 0
    puts fizz
  elsif n % 5 == 0
    puts buzz
  else
    puts n
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

check code(RUBY _ VERSION > = " 2 . 5 . 0 ")
它使用[terminal-table](https://github.com/tj/terminal-table)使结果易于查看。

```
require "coverage"
require "terminal-table"

Coverage.start(methods: true)
require_relative "fizzbuzz"
result = Coverage.result

rows = []

result.each do |filepath, coverage_data|
  filename = File.basename(filepath)
  methods = coverage_data[:methods]

  methods.each do |method_data, count|
    next if count.zero?

    defined_class, method_id, _from_row, _from_col, _to_row, _to_col = *method_data
    rows << [filename, defined_class, method_id, count]
  end
end

header = %w(filename defined_class method_id count)
rows.sort_by! { |e| -e.last }
table = Terminal::Table.new headings: header, rows: rows
puts table 
```

Enter fullscreen mode Exit fullscreen mode

结果

```
.
.
# display fizzbuzz.rb result
.
.
+-------------+---------------+-----------+-------+
| filename    | defined_class | method_id | count |
+-------------+---------------+-----------+-------+
| fizzbuzz.rb | Object        | fizz      | 27    |
| fizzbuzz.rb | Object        | buzz      | 14    |
| fizzbuzz.rb | Object        | fizzbuzz  | 6     |
+-------------+---------------+-----------+-------+ 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错。