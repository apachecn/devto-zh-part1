# 在 Ruby 中排序哈希

> 原文：<https://dev.to/hanachin/ordering-hash-in-ruby-15la>

使用`Hash#slice`如果 RUBY_VERSION > = "2.5.0"

```
h = {a: 1, b: 2}
key_order = %i[b a]
h.slice(*key_order)
# => {:b=>2, :a=>1} 
```

Enter fullscreen mode Exit fullscreen mode

[https://github . com/ruby/ruby/blob/v2 _ 5 _ 0/spec/ruby/core/hash/slice _ spec . Rb # L19-L21](https://github.com/ruby/ruby/blob/v2_5_0/spec/ruby/core/hash/slice_spec.rb#L19-L21)

## 微基准

```
# sort_hash.rb

require 'benchmark/ips'

class Hash
  # active_support/core_ext/hash/slice
  # https://github.com/rails/rails/blob/v5.2.0.beta2/activesupport/lib/active_support/core_ext/hash/slice.rb#L23-L25
  def slice_as(*keys)
    keys.each_with_object(Hash.new) { |k, hash| hash[k] = self[k] if has_key?(k) }
  end

  # based on active_support/core_ext/hash/slice
  # https://github.com/rails/rails/blob/v5.2.0.beta2/activesupport/lib/active_support/core_ext/hash/slice.rb#L23-L25
  def slice_sort(*keys)
    keys.each_with_object(Hash.new) { |k, hash| hash[k] = self[k] }
  end
end

Benchmark.ips do |x|
  x.report('Hash#slice builtin') do
    { a: 1, b: 2, c: 3 }.slice(:c, :b, :a)
  end

  x.report('Hash#slice active_support') do
    { a: 1, b: 2, c: 3 }.slice_as(:c, :b, :a)
  end

  x.report('Hash#slice active_support tweaked') do
    { a: 1, b: 2, c: 3 }.slice_sort(:c, :b, :a)
  end

  x.compare!
end 
```

Enter fullscreen mode Exit fullscreen mode

```
% ruby -v sort_hash.rb
ruby 2.5.0p0 (2017-12-25 revision 61468) [x86_64-linux]
Warming up --------------------------------------
  Hash#slice builtin   111.211k i/100ms
Hash#slice active_support
                        67.275k i/100ms
Hash#slice active_support tweaked
                        73.364k i/100ms
Calculating -------------------------------------
  Hash#slice builtin      1.479M (± 1.2%) i/s -      7.451M in   5.040261s
Hash#slice active_support
                        812.401k (± 1.0%) i/s -      4.104M in   5.051928s
Hash#slice active_support tweaked
                        876.849k (± 1.0%) i/s -      4.402M in   5.020535s

Comparison:
  Hash#slice builtin:  1478540.2 i/s
Hash#slice active_support tweaked:   876849.3 i/s - 1.69x  slower
Hash#slice active_support:   812400.9 i/s - 1.82x  slower 
```

Enter fullscreen mode Exit fullscreen mode