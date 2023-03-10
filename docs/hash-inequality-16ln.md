# 散列(不)相等

> 原文：<https://dev.to/burdettelamar/hash-inequality-16ln>

在我的自动化测试中，我经常想要测试一对散列的相等性。如果两者相等，那就很好。

但是如果它们不相等，记录的简单方法是记录失败，以及两个散列。如果哈希值非常小:我可以直观地比较它们来确定差异。

但是对于更大的哈希值，我不容易直观地确定它们的区别。这就是我的类`HashHelpler`出现的原因。

它有方法`HashHelper.compare(expected, actual)`,该方法接受预期的和实际的散列，并返回一个具有四个键及其相应值的散列:

*   `:ok` : value 是一个散列，包含了同时存在于`expected`和`actual`中的键/值对。
*   `:missing` : value 是一个散列，包含`expected`中的键/值对，但不在`actual`中。
*   `:unexpected` : value 是一个散列，包含`actual`中的键/值对，但不在`expected`中。
*   `:changed` : value 是一个散列，详细描述了在`expected`和`actual`中的键，但是它们的值不同。

所以:在我的方法`Log#verdict_assert_equal?`中，一个失败的散列比较得到并记录了详细的差异，这很容易看出什么是什么。

链接:

*   类[哈希帮助器](https://github.com/BurdetteLamar/RubyTest/blob/master/lib/helpers/hash_helper.rb)。
*   类 [HashHelperTest](https://github.com/BurdetteLamar/RubyTest/blob/master/test/helpers/hash_helper_test.rb) 。(我们这里都是测试员吧？).
*   模块[判断判断](https://github.com/BurdetteLamar/RubyTest/blob/master/lib/log/verdict_assertion.rb)。参见方法`_verdict_equal?`