# Google CloudÂ Storage 初探

> 原文：<https://dev.to/kyle/a-first-look-at-google-cloudstorage>

注:这是我的帖子关于探索谷歌云平台的[的延续。在](https://dev.to/kyle/a-first-look-at-google-cloudplatform)[我的个人博客](https://kyle.io/2017/07/first-look-google-cloud-storage/)上也有它的镜像。

* * *

*存储*价格方面， [S3](https://aws.amazon.com/s3/pricing/) 和 [GCS](https://cloud.google.com/storage/pricing) 基本相当，注意 GCS 带宽更贵，而且你并不能真正选择你想要支付的速率。

GCS 单区域相当于标准 S3，近线相当于 S3 不频繁访问，包括每 GB 检索费。两种级别的存储都比各自的 S3 级别便宜。手术费用是完全一样的。

不过，还有另外两种存储类别:

#### 多区域桶

一个主要功能是多区域存储桶，每 GB 多 0.006 美元。大概是为了避免像[us-~~tire fire~~-east-1](http://tirefi.re/aws/)翻倒和你的水桶消失这样的事件，如果你需要高可用性(并且愿意相信谷歌有适当的 HA)，这是一个明确的点。

假设单区域存储 3 份，我推测对于价格，多区域每个区域存储 2 份共 4 份。这(疑似)是多区域存储桶仅贵三分之一的原因。

#### 高岭土

Coldline 更有趣一点。作为冰川的等价物，它有一个很大的优势——检索速度快(秒量级)，比冰川好得多。缺点是固定的检索成本—0.05 美元/GB。仅仅比较 Coldline 和 Nearline 意味着你不应该每两年*检索一次以上。)如果你想比 Nearline 省钱。(Nearline 是 0.01 美元/GB，Coldline 是 0.007 美元/GB，所以选择 Coldline 每月可以节省 0.003 美元/GB。)*

相比较而言，Glacier 有许多检索速度和相应的检索定价。甚至 Glacier 的加急请求(最贵的选项)的费用也比 Coldline 低 40%。(有趣的是，批量检索选项大约占 S3 IA 检索的 25%,但是需要 8-12 个小时。)

考虑到 Glacier 比 Coldline 便宜将近 50%，我真的怀疑 Coldline 的实用性。考虑到恢复成本，只有在您存储大约两年内不需要访问的归档数据时，恢复才有意义。我认为 Glacier 在预期用例上有更好的处理方式。

我可以看到 Nearline 正在被使用(人们期望立即访问)，但 Coldline 的检索价格无法改变，这使我对使用它非常怀疑。