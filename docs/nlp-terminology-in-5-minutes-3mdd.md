# 5 分钟内 NLP 术语

> 原文：<https://dev.to/tomerbendavid/nlp-terminology-in-5-minutes-3mdd>

| 学期 | 意义 |
| --- | --- |
| **权重和向量** |  |
| TF-IDF | 一个单词在文档中出现的次数越多，而在语料库(其他文档)中出现的次数越多，每个单词的权重就越高，因为这个单词对我们的文档来说是特殊的，所以它在我们的文档中的权重就越高！TF-IDF 是 **T** erm **F** 频率**I**n 反转 **D** 文档 **F** 频率的缩写 |
| 长度(TF-IDF，doc) | 对于向量中的每个单词数，文档中的不同单词数。 |
| 词向量 | 计算单词向量:
对于每个单词 w1 = >对于每 5 个窗口单词，使向量越来越
更近，v【w1】更近 v【w2】
国王-王后~男人-女人//哇它会为你找到的！
你甚至可以下载现成的单词矢量 |
| 谷歌单词向量 | 你可以下载现成的谷歌训练矢量词 |
| **文本结构** |  |
| 词性标注 | 单词角色:是动词、名词还是……并不总是显而易见 |
| 句首 | head(句子)最重要的单词，它不是 nessesaraly 的第一个
单词，它是句子最重要的单词
她撞到了墙上= >撞上了。
你为一个句子建立一个图形，它就成了根。 |
| 命名实体 | 人、公司、地点……快速了解文本内容的方法。 |
| **情感分析** |  |
| 情感词典 | 爱+2.9，恨:-3.2，“我爱过你但现在恨你”=> 2.9 - 3.2 |
| 情感实体 | 是关于电影还是关于电影院？ |
| 情感特征 | 相机/ **分辨率**，相机/ **便捷度** |
| **文本分类** | 决定，决定:主题是什么，他快乐吗，英语母语者？大多数监督训练:我们有标签，然后将新文本映射到标签 |
| 监督学习 | 我们有 3 套，培训套，开发套，测试套。 |
| 列车组 |  |
| 开发(=验证)集 | 调整参数(也是为了防止过度拟合)，调整模型 |
| 测试装置 | 检查您的模型 |
| 文本特征 | 把要分类的文档转换成特征，
包词的词向量，可以用 TF-IDF |
| 皱胃向左移 | 潜在 Dirichlecht 分配:LDA(Documents) = >话题
技术话题:Scala、编程、机器学习
体育话题:足球、篮球、滑板(3 个最重要的单词)
提前挑选话题数量#喜欢 5 个话题
Doc =分布(话题)每个话题的概率
话题=分布(单词)技术话题更高可能超过 cpu 单词
无监督，有哪些话题模式。有助于了解博士是干什么的。 |
| **机器读数** |  |
| 实体提取 | entity recognition(text)= >(entity name-> entity type)
(“保罗·纽曼是个很棒的演员”)= > [(保罗·纽曼- >人)] |
| 实体链接 | Entity link(Entity)= > fixed meaning
Entity link(" Paul Newman ")=>"[http://Wikipedia../保罗 _ 纽曼 _ the _ 演员](http://wikipedia../paul_newman_the_actor)
(而不是根据文字改编的另一个保罗·纽曼) |
| dbpedia | 对于维基百科，机器可以阅读它。用 SparQL 查询 DBPedia |
| 弗雷德(自由党)/派克斯 | 弗雷德(自然语言)= >形式结构 |
| 资源 | [https://www.youtube.com/watch?v=FcOH_2UxwRg](https://www.youtube.com/watch?v=FcOH_2UxwRg)
T3】https://tinyurl.com/word-vectors |