# 使用 awk 批量重命名图像，包括图像分辨率

> 原文：<https://dev.to/victoria/batch-renaming-images-including-image-resolution-with-awk-2d7>

在我的“我做过的让我感觉非常棒的古怪事情”清单上，最近的一项是一个小时的冒险，最终以这段代码结束:

```
$ file IMG* | awk 'BEGIN{a=0} {print substr($1, 1, length($1)-5),a++"_"substr($8,1, length($8)-1)}' | while read fn fr; do echo $(rename -v "s/$fn/img_$fr/g" *); done IMG_20170808_172653_425.jpg renamed as img_0_4032x3024.jpg
IMG_20170808_173020_267.jpg renamed as img_1_3024x3506.jpg
IMG_20170808_173130_616.jpg renamed as img_2_3024x3779.jpg
IMG_20170808_173221_425.jpg renamed as img_3_3024x3780.jpg
IMG_20170808_173417_059.jpg renamed as img_4_2956x2980.jpg
IMG_20170808_173450_971.jpg renamed as img_5_3024x3024.jpg
IMG_20170808_173536_034.jpg renamed as img_6_4032x3024.jpg
IMG_20170808_173602_732.jpg renamed as img_7_1617x1617.jpg
IMG_20170808_173645_339.jpg renamed as img_8_3024x3780.jpg
IMG_20170909_170146_585.jpg renamed as img_9_3036x3036.jpg
IMG_20170911_211522_543.jpg renamed as img_10_3036x3036.jpg
IMG_20170913_071608_288.jpg renamed as img_11_2760x2760.jpg
IMG_20170913_073205_522.jpg renamed as img_12_2738x2738.jpg
// ... etc etc 
```

Enter fullscreen mode Exit fullscreen mode

上述列表的最后一项是“待办事项:为这个列表想一个更短的标题。”

我之前写过命令行工具的威力，比如 [sed](https://victoria.dev/verbose/when-stackoverflow-sed-saves-30mins/) 。这篇文章详细阐述了如何将所有这些神奇的功能串成一条巨大的、长长的、彩虹色的、粘稠的牛逼流。

## 重命名文件

实际处理文件重命名的工具是`rename`。语法是:`rename -n "s/original_filename/new_filename/g" *`其中`-n`做一个预演，替换`-v`将重命名文件。`s`表示我们的替换字符串，代表“全局”的`g`查找该字符串的所有出现。`*`匹配我们的搜索和替换参数的零次或多次出现。

我们稍后将回到这一点。

## 获取文件信息

当我在图像目录中运行`$ file IMG_20170808_172653_425.jpg`时，我得到这个输出:

```
IMG_20170808_172653_425.jpg: JPEG image data, baseline, precision 8, 4032x3024, frames 3 
```

Enter fullscreen mode Exit fullscreen mode

因为我们可以获得图像分辨率(上面的“4032x3024”)，我们知道我们可以在新的文件名中使用它。

## 分离出我们想要的信息

我喜欢`awk`的简单。它获取文本行，并通过内置变量将单个信息提供给我们，我们可以将这些变量称为列号，用`$1`、`$2`等表示。默认情况下，`awk`在空格处拆分列。以上面的例子为例:

| one | Two | three | four | five | six | seven | eight | nine | Ten |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| IMG_20170808_172653_425.jpg: | 联合图像专家组 | 图像 | 数据， | 基线， | 精确 | 8, | 4032x3024， | 框架 | three |

我们可以使用不同的值作为分割器，例如，如果我们想使用逗号作为列的分割线，可以使用`-F','`。对于我们当前的项目，空间是好的。

在将信息插入新文件名之前，我们需要解决几个问题。列`$1`有我们想要的原始文件名，但是在末尾有一个额外的“:”字符。我们不需要这个”。jpg”要么。列`$8`有一个多余的“，”我们也不想要。为了获得我们需要的信息，我们将使用`substr()`获取列的子串:

`substr($1, 1, length($1)-5)` -这给出了从字符串开始到字符串结束的文件名，减去 5 个字符(“长度减 5”)。
`substr($8,1, length($8)-1)`——这给出了图像的大小，没有多余的逗号(“长度减 1”)。

## 避免重复文件名

为了确保具有相同分辨率的两个图像不会创建相同的、竞争的文件名，我们将在文件名后附加一个唯一的递增数字。

`BEGIN{a=0}` -使用`BEGIN`告诉`awk`在(鼓声)开始时只运行一次下面的代码。这里，我们将变量`a`声明为`0`。
`a++`——稍后在我们的代码中，在文件名的适当位置，我们调用`a`并递增它。

当`awk`打印一个字符串时，它会将没有用逗号分隔的所有内容连接起来。例如，`{print a b c}`会创造“ABC”,`{print a,b,c}`会创造“abc”。

我们可以在文件名中添加额外的字符，比如下划线，方法是在引号中插入:`"_"`。

## 把一切串起来

为了将一个命令的输出提供给另一个命令，我们使用“管道”，写作`|`。

如果我们在这种情况下只使用管道，那么来自`file`和`awk`的所有数据都会立刻被输入到`rename`中，形成一个非常非常长并且可能是非编译文件名。要逐行运行`rename`命令，我们可以使用`while`和`read`。类似于`awk` , `read`接受输入，并将其分割成我们可以分配和使用的变量。在我们的代码中，它从`awk`(原始文件名)获取输出的第一位，并给它分配变量名`$fn`。它获取第二个输出(我们的递增数字和图像分辨率)并将其分配给`$fr`。变量名是任意的；你想怎么称呼他们都行。

要运行我们的`rename`命令，就像我们在终端中一个接一个地手工输入它们一样，我们可以使用`echo $(some command)`。最后，`done`结束了我们的`while`循环。

## 奖励回合:彩虹输出！

我没有拿“彩虹色”开玩笑...

```
$ pip install lolcat 
```

Enter fullscreen mode Exit fullscreen mode

以下是我们的完整代码:

```
$ file IMG* | awk 'BEGIN{a=0} {print substr($1, 1, length($1)-5),a++"_"substr($8,1, length($8)-1)}' | while read fn fs; do echo $(rename -v "s/$fn/img_$fs/g" *); done | lolcat 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！