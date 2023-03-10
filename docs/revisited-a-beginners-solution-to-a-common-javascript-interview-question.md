# 重访:一个常见 Javascript 面试挑战的初学者解决方案

> 原文：<https://dev.to/vincecampanale/revisited-a-beginners-solution-to-a-common-javascript-interview-question>

“确认结局”是自由代码营的算法脚本挑战的另一个大问题。目标是检查一个字符串(第一个参数，`str`)是否以给定的目标字符串(第二个参数，`target`)结束。

对于编码练习来说，这个问题有无数种解决方法。今天，我将从我第一次开始编码的时候开始走一遍我的解决方案，改进它，并以描述`.substr()`和`.substring()`之间的区别作为结束。

#### 初学大法

当我第一次冒险学习编码时，我尝试了这个问题并成功地解决了它，然而我的解决方案有点..."详细"...

我使用了带两个参数的`.substr()`方法。第一个参数是字符串中从*开始到*的索引，第二个参数是从该索引中提取的字符数。所以，假设你想从`"hello!""`那里得到`"lo"`。为了用`.substr()`做到这一点，你会说`"hello!".substr(3, 2)`。这将转到`"hello"!`中的第三个字符，即第一个`"l"`，然后再转一个字符，在`"hello!"`中的第四个字符`"o"`处停止。如果你认为我的计数技巧需要改进，你可能是对的，但是在 Javascript 中，字符串索引是从 0 开始的(像数组一样)。所以字符`"h"`在字符串中的索引为 0。

```
function confirmEnding(str, target) {
  var substr = str.substr(str.length - target.length, target.length);

  if (substr === target) return true;
  else return false;
} 
```

简单重述一下我在这里所做的:创建一个变量，它包含一个从`str`参数末尾截取的字符串，该字符串的长度与目标字符串的长度相同。如果该变量中包含的字符串与目标匹配，则返回 true。否则，返回 false。一个非常简单的问题的非常简单的解决方案，但这可以更好。

#### 更好的解决方案

调用`.substr`时，你可以使用一个巧妙的技巧。如果你给它一个负数作为它的第一个也是唯一的参数，例如`"string".substr(-3)`，你将得到一个包含从给定数字*开始到字符串结尾*的字符的字符串。于是`"string".substr(-3)`返回`"ing"`。让我们在解决方案中使用这个技巧:

```
function confirmEnding(str, target) {
  return target === str.substr(-target.length);
} 
```

用英语来说，这个函数是这样说的:从`str`的末尾返回一个与`target`长度相同的字符串，将新字符串与`target`进行比较。如果他们*严格与*相等，还真。否则，返回 false。这也适用于两个等号，但是使用三个等号是典型的好习惯。

我们现在可以使用 ES6 语法来创建一个非常漂亮、简洁、纯粹的函数:

```
const confirmEnding = (str, target) => target === str.substr(-target.length); 
```

#### [T3`.susbtr()`和`.substring()`的区别](#the-difference-between-raw-susbtr-endraw-and-raw-substring-endraw-)

这个壮举也可以用`.substring()`来完成，但是我不认为这个解决方案很优雅。不过这是一个很好的练习，我会让你来尝试一下。这些方法可能看起来是彼此的别名，但是它们实际上做不同的事情。

来自 MDN 文档(又名 Javascript 圣经):

`.substr()`:“返回从指定位置开始到指定字符数的字符串中的字符”。
`.substring()`:“返回一个索引和另一个索引之间的字符串的子集，或者通过字符串的结尾”。

或许更简单的思考方式是这样的:

`.substr()`:从索引*自变量一*开始，得到*自变量二*的字符数。
`.substring()`:从索引*参数一*开始，获取字符，直到*参数二*。

从另一个角度来看:

`.substr(from this index, for this many characters)`
T1】

希望这是有帮助的！随时寻求反馈、问题、建议，尤其是赞美。如果你想跟随我探索这种奇妙的网络语言，可以在推特上或者我的 T2 博客上找到我。

文尼完毕。âœŒï