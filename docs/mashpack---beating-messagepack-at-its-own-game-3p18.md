# Mashpack -在自己的游戏中击败 Messagepack

> 原文：<https://dev.to/sethmlarson/mashpack---beating-messagepack-at-its-own-game-3p18>

Mashpack 是一种 JSON 对象序列化格式，是流行格式 [Messagepack](https://msgpack.org/) 的“方言”。我阅读了[Messagepack 规范](https://github.com/msgpack/msgpack/blob/master/spec.md)，注意到一些我认为可以改进的细节，所以我挑战自己“击败”Messagepack。以下是我的结果:

# 关键设计差异

*   单字节前缀重新排序
*   类型化数组
*   8 位数组类型
*   扩展ˌ扩张

## 单字节头数据类型

Mashpack 和 Messagepack 都使用位前缀技巧将某些小数据类型打包到一个字节中，同时仍然允许足够的前缀空间来支持我们需要的所有其他数据类型，以确保有效地使用空间。

这两个规范都使用单字节数据类型，这很重要，因为大多数序列化对象都希望最终成为单字节头数据类型，因为它们的头占用空间很小，所以拥有一个大范围的公共值是非常宝贵的。

| 投机 | 单字节头数据类型 |
| --- | --- |
| 混搭包 | `MAPP`、`STRP`、`MARRAYP`、`INTP`、`NINTP` |
| 消息包 | `positive fixint`、`fixstr`、`negative fixint`、`fixmap`、`fixarray` |

在 Mashpack 中，字符串和地图数据类型的优先级是用最大的空间量
来确定的。与 Messagepack 相比，Messagepack 非常重视正整数，因此所有其他数据类型的范围都较小。

参见下表，比较两种规格:

| 数据类型 | Mashpack 中的范围 | Messagepack 中的范围 |
| --- | --- | --- |
| `MAPP` / `fixmap` | 0 到 63 个键值对 | 0 到 15 个键值对 |
| `STRP` / `fixstr` | 0 到 63 个编码字节 | 0 到 31 个编码字节 |
| `MARRAYP` / `fixarray` | 0 到 31 个元素 | 0 到 15 个元素 |
| `NINTP` / `negative fixint` | -1 到-32 | -1 到-32 |
| `INTP` / `positive fixint` | 0 到 31 | 0 到 127 |

更多信息见[一个表](https://github.com/SethMichaelLarson/mashpack#specification)中所有 Mashpack 的数据类型。

## 类型化和混合数组

Mashpack 添加了 Messagepack 中没有的新数据类型，称为“类型化数组”或简称为`ARRAY*`。这与“混合数组”(`MARRAY*`)不同，它要求数组中的所有对象都是相同的数据类型。具有不同头但属于同一家族的对象可以被“升级”到更大的数据类型，只要得到的类型化数组小于混合数组。

一个例子是一个整数列表，其中一个将被编码成一个`INTP`类型，所有其他被转换成`INT8`的将被转换成一个`ARRAY8[INT8]`。见下图:

```
object = [1, 255, 255]
could be naively encoded into:
MARRAYP([INTP(1), INT8(255), INT8(255)]) = 7 bytes

but converting the INTP(1) into INT8(1) we can take
advantage of typed arrays to end up with this:

ARRAY8[INT8]([1, 255, 255]) = 6 bytes 
```

## 添加一个 8 位数组类型

尽管有一个未使用的数据类型 id ( `0xC1`)，Messagepack 并没有定义
一个 8 位数组类型。此外，fixarray
的范围更小，只有 15 个元素，这意味着 Messagepack
中有 16 个元素的数组使用 3 个字节的头，而不是 Mashpack 中的 1 个字节。

## 较大的字符串表示 Unicode 友好

Unicode 为普遍将字节转换成字符串带来了很多和谐，这曾经是一个非常困难的问题。unicode 的一个不可避免的缺点是，并非所有的字符都是 8 位的，这意味着每个非 ASCII 的字符串都需要额外的空间来存储。Mashpack 能够在一个单字节字符串中存储两倍多的编码字节数，这有助于解决这个问题。

由于 Python 2，Messagepack 的 Python 实现似乎也与其字符串数据类型有着奇怪的关系。当前的 Mashpack Python 实现只支持 Python 3+,并且只允许对其字符串类型进行 UTF-8 编码。

## 更大的字符串意味着网址也大多很短！

现在很多 API 都有有用的“探索性”URL 来提示 API 用户关于额外的内容。这将几乎总是采取一个完整的网址的形式，通常很长！几乎总是长于 31 字节。拥有一个更大的单字节字符串有助于解决这个问题。参见压缩性能比较部分。

## 分机

Mashpack 定义了三种扩展数据类型大小(`EXT8`、`EXT16`和`EXT32`)，而 Messagepack 定义了 8 种(`fixint 1`、`fixint 2`、`fixint 4`、`fixint 8`、`fixint 16`、`ext8`、`ext16`和`ext32`)

扩展在 Messagepack 和 Mashpack 中的工作非常相似，因为它们都有一个扩展代码和数据部分。扩展代码是一个介于 0 和 255 之间的无符号整数，表示正在使用什么扩展。Messagepack 和 Mashpack 的扩展之间的区别在于，Messagepack 的固定扩展类型定义了长度正好为 1、2、4、8 或 16 字节的“固定”扩展。这为具有较小数据段的扩展提供了 2 字节的报头。

Mashpack 只允许最少 3 个字节的头，所以在 Mashpack 下，小型数据扩展在存储方面稍受影响。当使用 17 字节或更长的数据段时，在 Mashpack 和 Messagepack 下进行相同的打包。

## 前缀层数较少

与 Messagepack 的前缀分层(1x1-> 2 x3-> 31x 8)相比，Mashpack 的前缀分层(2 x2-> 3 x3-> 2 x4-> 31x 8)只有三层，这意味着 Mashpack 最多只会使用 3 次比较来确定打包对象的类型，而 Messagepack 需要 4 次比较。

## 正固定件快速拆包

Messagepack 的正固定类型以前缀`0xxxxxxx`为前缀，允许直接从整数所在的字节中读取整数，而无需屏蔽。

Mashpack 没有这个属性，但只需要一个位屏蔽操作来弥补。在确保其他对象有更好的位前缀和范围时，这种差异是不可避免的。

Mashpack 和 Messagepack 的`NINTP`和 negative fixint 对于前缀为`111xxxxx`的负数都有这个属性。

# 表现

## 压缩差异

下面是一些小型、中型和大型对象，用于比较 Mashpack 和 Messagepack 之间的压缩。越低越好。

| 目标 | Mashpack 大小 | 消息包大小 | 差异 |
| --- | --- | --- | --- |
| One hundred and twenty-seven | Two | one | +1 (+50%) |
| [1] * 16 | Seventeen | Nineteen | -2 (-10.5%) |
| [网址](https://github.com/SethMichaelLarson/mashpack) | Forty-six | Forty-eight | -2 (-4.1%) |
| [127] * 100 | One hundred and three | One hundred and one | +2 (+1.9%) |
| [255] * 100 | One hundred and three | Two hundred and three | -100 (-49.2%) |
| [GitHub 推送事件](https://developer.github.com/v3/activity/events/types/#webhook-payload-example-26) | Six thousand and ninety-one | Six thousand one hundred and ninety-two | -101 (-1.6%) |

## 速度差异

这里有几个被处理了 10，000 次的大小对象，用来测试 Mashpack 和 Messagepack 之间打包和解包速度的差异。越低越好。

**注意:**这些速度比较是在英特尔 i5-6400 4x2.7GHz 处理器上进行的，内存为 16 GB，使用的是 Python“回退”版本的 Messagepack，因为目前还没有实现 Mashpack 的 Cython。稍后将会有更多的基准来比较 Cython 的实现。

### 包装物件

| 目标 | 混搭时间 | 消息包时间 | 差异 |
| --- | --- | --- | --- |
| [1, 1, 1, 1, 1] | 0.22 秒 | 0.31 秒 | -0.09 秒 |
| [GitHub 推送事件](https://developer.github.com/v3/activity/events/types/#webhook-payload-example-26) | 42.9 秒 | 59.4 秒 | -16.5 秒 |

### 拆包对象

| 目标 | 混搭时间 | 消息包时间 | 差异 |
| --- | --- | --- | --- |
| [1, 1, 1, 1, 1] | 0.15 秒 | 0.15 秒 | 0 秒 |
| [GitHub 推送事件](https://developer.github.com/v3/activity/events/types/#webhook-payload-example-26) | 8.39 秒 | 9.72 秒 | -1.33 秒 |

# 我该如何提供帮助或贡献？

Mashpack 是一个全新的规范！在它成为标准之前还有很多工作要做。这总是项目中最有趣的部分，所以我想和所有感兴趣的人分享。:)

为 Mashpack 库做出贡献的每个人都将获得该库的 Collaborator，因为我希望这是社区的一个项目。所有的变更都需要由另一个合作者和任何代码所有者进行代码审查。

## 新来开源还是 Python？

[有些问题我已经用`good first issue`](https://github.com/SethMichaelLarson/mashpack/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) 做了标记，我愿意投入额外的时间、精力和资源来帮助你解决。如果你想解决这些问题中的一个，但觉得需要额外的支持，你可以[在 Twitter 上给我发消息](https://twitter.com/sethmlarson)问问题。:-)

## 准备好解决更棘手的问题了吗？

在这一类别中有许多问题都标有 [`help wanted`标签](https://github.com/SethMichaelLarson/mashpack/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22)。我最终会谈到这些，但是如果你想投稿并让自己出名，这是一个很好的起点。

## 你熟悉或有兴趣学习 Cython 吗？

如上所述，没有 Mashpack Packer 或 Unpacker 对象的 Cython 实现。拥有一个 Cython 实现可以将操作速度提高十倍，并且是 Messagepack 的有力竞争者。我已经用标签标记了所有与 Cython 相关的[问题。](https://github.com/SethMichaelLarson/mashpack/issues?q=is%3Aissue+is%3Aopen+label%3Acython)