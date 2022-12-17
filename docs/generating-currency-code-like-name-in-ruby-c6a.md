# 在 Ruby 中生成类似货币代码的名称

> 原文：<https://dev.to/kentaro/generating-currency-code-like-name-in-ruby-c6a>

各种货币。此外，加密货币的兴起增加了类似货币的价值储存手段的数量。我们才敢往里面加点。当时，给货币命名是个问题。

## 货币代码是什么？

ISO 4217 描绘了如何缩短货币名称；货币代码。

> 代码的前两个字母是 ISO 3166-1 alpha-2 国家代码的两个字母(省略)，第三个通常是货币本身的首字母。所以日本的货币代码是 JPY——JP 代表日本，Y 代表日元。

[ISO 4217 -维基百科](https://en.wikipedia.org/wiki/ISO_4217)

## 智能令牌的货币代码

因为我想要一个类似的由智能令牌支持的原始货币，所以我为 Ruby 编写了一个名为`currency_coder`的小库。

[货币 _ 编码器宝石](https://github.com/pepabo/currency_coder)

这个库将任意字符串(如你的名字)转换成类似货币代码的简称，如下:

```
cc = CurrencyCoder.new

currency_map = {
  "osamu"    => "OSM",
  "mayoto"   => "MYT",
  "kurotaki" => "KRT",
  "putchom"  => "PTM",
  "antipop"  => "ATP",
  "jitsuzon" => "JZN"
}

currency_map.each do |name, code|
  assert_equal code, cc.as_currency_code(name)
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们使用这个库创建自己的货币吧！