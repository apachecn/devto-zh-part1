# Javascript 诗歌

> 原文：<https://dev.to/sethrah/javascript-poem>

最近，我通读了[安格斯·克罗尔](http://blog.anguscroll.com/)的[《如果海明威写了 JavaScript》](http://a.co/644J0Ed)。这本书有五个“编程作业”,然后是不同的著名作者如果写 java 脚本会想出的解决方案。

作者从厄内斯特·海明威和弗吉尼亚·伍尔夫到戴维·福斯特·华莱士和图派克·夏库尔。这本书也做得很好，在他们提交之前给出了作者的摘要，然后解释了为什么提交遵循他们的写作风格。

最酷的事情之一是解决方案如何利用形式将他们的特征融入到解决方案中。虽然在可维护性/可读性方面可能并不总是最好的，但我认为这些代码诗中肯定有一些东西是我日常编码中所缺少的，所以我从书中选取了一个作业，并做出了自己的回应。我对最终的回应(我认为我的评论比实际代码多)并不感到非常自豪，但这样做很有趣。

*分配*

> 编写一个可链接的函数，它接受每个函数调用一个单词，但是在没有参数的情况下调用时，将按顺序返回所有先前传递的单词

在下面的诗中，调用函数将实现赋值结果，可以像调用`recall('hello')('world')();`一样调用，它将打印`hello world`

```
words=/*Are but for recollection but what does that entail*/[];

/*To*/recall=/*just a */function/*of the*/(word){
  /*but how does one*/return word ?
  /*Simply by*/retain/*ing*/(word/*s*/)
  :/*then*/regurgitate(/*ing*/);
}

/*To*/retain=/*a*/function/*of*/(word){/*whence*/words.push(word);
  /*Towards words worthy to*/return/*and*/recall;}

/*To*/regurgitate=/*a*/function/*of*/(word){
  /*where*/recollection=/*of*/words.join(/*ed*/' ');/*are summoned forth*/
  words=[/*to be spent*/];
  return/*the*/recollection/*for substance went*/;
} 
```