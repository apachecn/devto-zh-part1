# 关于萨夫挤压的一个小细节

> 原文：<https://dev.to/voins/one-tiny-detail-about-the-saff-squeeze>

肯特·贝克几年前介绍的萨夫挤压技术非常好。(不幸的是，原来的文章已经消失了，但有一个存档的副本: [Hit 'Em High，Hit 'Em Low](https://web.archive.org/web/20090301204625/http://www.threeriversinstitute.org/HitEmHighHitEmLow.html) )只有一个额外的细节，值得注意。如果你写了一个失败的测试，那么每次失败时，你都需要检查它是如何失败的。因为如果失败的原因变了，说明你做错了，是时候退一步了。但是有一个技巧。如果您重写测试，使它断言错误的行为，它将是绿色的(当然，直到您修复代码)。这意味着，你的代码以某种已知的方式失败*。没有惊喜。我发现这种变化更方便一些。*