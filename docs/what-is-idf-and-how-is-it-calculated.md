# 什么是 IDF，如何计算？

> 原文：<https://dev.to/andrewlucker/what-is-idf-and-how-is-it-calculated>

逆文档频率

IDF 是现代搜索引擎相关性计算最基本的术语之一。它用于确定一个术语的稀有程度以及与原始查询的相关性。例如，以查询“金州勇士”为例。这个查询很难，因为没有一个单独的词来标识我们搜索篮球队的意图。相反，我们需要查看单词组，并权衡每组单词与整个查询的相关性。这是平面搜索查询相关性的基础，一切从 IDF 开始。

在计算 IDF 之前，我们需要将每个文档或查询与一组特性相关联。对于本教程，我们将只使用 *n 元语法*。n-gram 是一个或多个单词。我们可以使用 python 的字符串方法从文档或查询中快速提取特征。

```
def extract_features( document ):
    terms = tuple(document.lower().split())
    features = set()
    for i in range(len(terms)):
    for n in range(1,4):
    if i+n <= len(terms):
    features.add(terms[i:i+n])
    return features

print(extract_features(â€˜The Golden State Warriors’)) 
```

接下来，我们需要计算文档频率，然后进行反演。IDF 的公式从数据库中的文档总数 n 开始，然后除以包含我们的术语 tD 的文档数。这将永远不会产生小于 1 的数字，因为 1 表示该术语出现在所有文档中，没有比该限制更常见的文档频率了。接下来，我们通常取整项的对数，因为我们可能会索引数十亿个文档，IDF 可能会变得非常笨拙，除非我们以数量级来引用它。

在这里，我们可以在一个小型文档数据库中计算我们所有功能的 IDF。

```
def extract_features( document ):
   terms = tuple(document.lower().split())
   features = set()
   for i in range(len(terms)):
      for n in range(1,4):
          if i+n <= len(terms):
              features.add(terms[i:i+n])
   return features

documents = [
   "This article is about the Golden State Warriors",
   "This article is about the Golden Arches",
   "This article is about state machines",
   "This article is about viking warriors"]

def calculate_idf( documents ):
   N = len(documents)
   from collections import Counter
   tD = Counter()
   for d in documents:
      features = extract_features(d)
      for f in features:
          tD[" ".join(f)] += 1
   IDF = []
   import math
   for (term,term_frequency) in tD.items():
       term_IDF = math.log(float(N) / term_frequency)
       IDF.append(( term_IDF, term ))
   IDF.sort(reverse=True)
   return IDF

for (IDF, term) in calculate_idf(documents):
    print(IDF, term) 
```

正如您在输出中看到的，罕见术语被分配了更高的 IDF，因此在相关性计算中可以被赋予更高的权重。

[![](img/e984e64cdd78961866823df40dffee68.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VDJNMpbZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ApDGJo0U0-7WM-yLQC4cYcg.jpeg)