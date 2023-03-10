# 用 Python 和 NLTK 构建一个快速摘要器

> 原文：<https://dev.to/davidisrawi/build-a-quick-summarizer-with-python-and-nltk>

如果你对数据分析感兴趣，你会发现学习自然语言处理非常有用。开始学习 NLP 的一个好项目是编写一个摘要器——一种减少文本正文但保持其原始含义的算法，或者对原始文本给出很好的见解。

NLP 有很多库。对于这个项目，我们将使用 NLTK -自然语言工具包。

让我们从写下构建项目所需的步骤开始。

## 构建汇总器的 4 个步骤

1.  删除分析的停用词(定义如下)
2.  创建单词频率表——每个单词在文本中出现多少次
3.  根据句子包含的单词和频率表给每个句子打分
4.  通过添加超过某个分数阈值的每个句子来建立摘要

就是这样！Python 实现也简单明了。

### 什么是停用词？

任何不增加句子意义价值的词。例如，假设我们有这样一句话

> 一群人每天从阿拉法亚的一家银行跑到最近的墨西哥小吃店

通过删除句子中的停用词，我们可以减少单词的数量并保留其含义:

> 一群人每天从阿拉法亚银行跑到最近的墨西哥小吃店

我们通常从被分析的文本中移除停用词，因为知道它们的频率并不能给文本主体任何洞察。在这个例子中，我们删除了单词 *a* 、中的*和*中的*的实例。*

## 现在，我们开始吧！

有两个 NLTK 库是构建一个高效的摘要器所必需的。

```
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize, sent_tokenize 
```

注意:有更多的库可以让我们的摘要器更好，本文最后讨论了一个例子。

### 文集

语料库是指文本的集合。它可以是某个诗人的诗歌数据集、某个作者的作品集等等。在这种情况下，我们将使用预先确定的停用词数据集。

### [令牌设备](#tokenizers)

基本上，它把一个文本分成一系列的记号。有三个主要的记号赋予器——单词、句子和正则表达式记号赋予器。对于这个具体的项目，我们将只使用单词和句子分词器。

## 去除停用词，制作频率表

首先，我们创建两个数组——一个用于停用词，一个用于正文中的每个词。

让我们用`text`作为原文正文。

```
stopWords = set(stopwords.words("english"))
words = word_tokenize(text) 
```

其次，我们为词频表创建一个字典。为此，我们应该只使用不属于停用词数组的词。

```
freqTable = dict()
for word in words:
    word = word.lower()
    if word in stopWords:
        continue
    if word in freqTable:
        freqTable[word] += 1
    else:
        freqTable[word] = 1 
```

现在，我们可以在每个句子上使用`freqTable`字典，以了解哪些句子与文本的整体目的最相关。

## 给每一句话打分

我们已经有了一个句子分词器，所以我们只需要运行`sent_tokenize()`方法来创建句子数组。其次，我们需要一个字典来保存每个句子的分数，这样我们以后可以通过字典来生成摘要。

```
sentences = sent_tokenize(text)
sentenceValue = dict() 
```

现在是时候检查每一个句子，并根据单词给它打分了。有许多算法可以做到这一点——基本上，任何一致的通过单词给句子评分的方法都可以。我用了一个基本的算法:把一个句子中每个不停的词的频率加起来。

```
for sentence in sentences:
    for wordValue in freqTable:
        if wordValue[0] in sentence.lower():
            if sentence[:12] in sentenceValue:
                sentenceValue[sentence[:12]] += wordValue[1]
            else:
                sentenceValue[sentence[:12]] = wordValue[1] 
```

*注意:*word value 的索引 0 会返回单词本身。索引 1 实例的数量。

如果`sentence[:12]`引起了你的注意，接得好。这只是将每个句子散列到字典中的一种简单方法。

请注意，我们的评分算法的一个潜在问题是长句比短句更有优势。要解决这个问题，把每个句子的分数除以句子的字数。

### 那么，我们可以用什么值来比较我们的分数呢？

解决这个问题的一个简单方法是求一个句子的平均分。从那里，找到一个门槛将是轻而易举的事。

```
sumValues = 0
for sentence in sentenceValue:
    sumValues += sentenceValue[sentence]

# Average value of a sentence from original text average = int(sumValues/ len(sentenceValue)) 
```

那么，什么是好的门槛呢？错误的值可能会给出太小/太大的摘要。

平均值本身就是一个很好的阈值。对于我的项目，我决定做一个更短的总结，所以我使用的阈值是平均值的 1 . 5 倍。

现在，让我们应用我们的阈值，将我们的句子按顺序存储到摘要中。

```
summary = ''
for sentence in sentences:
        if sentence[:12] in sentenceValue and sentenceValue[sentence[:12]] > (1.5 * average):
            summary +=  " " + sentence 
```

你成功了！！你现在可以`print(summary)`看看我们的总结有多好。

## 可选增强:制作更智能的词频表

有时，我们希望两个非常相似的词来增加同一个词的重要性，例如，母亲、妈妈和妈咪。为此，我们使用一种斯特梅尔算法将单词与它的词根联系起来。

要实现斯特梅尔，我们可以使用 NLTK 词干分析器的库。您会注意到有许多词干分析器，每一个都是查找词根的不同算法，对于特定的场景，一个算法可能比另一个算法更好。

```
from nltk.stem import PorterStemmer
ps = PorterStemmer() 
```

然后，在将每个单词添加到我们的`freqTable`之前，通过词干分析器传递每个单词。在把每个句子中的单词分数相加之前，仔细阅读每个句子时，找出每个单词的词干是很重要的。

## 大功告成！

恭喜你！让我知道，如果你有任何其他问题或增强这个摘要。

* * *

感谢您阅读我的第一篇文章！心情激荡