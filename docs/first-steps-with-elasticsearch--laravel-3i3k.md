# 使用 Elasticsearch & Laravel 的第一步

> 原文：<https://dev.to/treggats/first-steps-with-elasticsearch--laravel-3i3k>

[![](img/4a98fd8480e39930ecd19faff0be97a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_r8pKKcN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVXdzDpO_KNegDIdMdO1OkA.png)

在过去的工作中与 Solr 一起工作后，我一直对索引大量数据和处理这些数据感兴趣。

所以最近我被告知要调查 Elasticsearch，我一直在关注[如何将你的 Laravel 应用程序与 Elasticsearch](https://blog.madewithlove.be/post/how-to-integrate-your-laravel-app-with-elasticsearch) 整合的帖子，作者是[托尼·梅西亚](https://twitter.com/tony0x01)，来自[的《用爱制造](https://madewithlove.be)。

事实证明这其实很简单，需要索引的雄辩模型有一个包含创建和删除事件的特征。这些实际上是模型观察者事件，它们决定了一段数据是否应该被 Elasticsearch 索引。

```
public function saved ($model)
{
    $this->elasticsearch->index([
        'index' => $model->getSearchIndex(),
        'type' => $model->getSearchType(),
        'id' => $model->id,
        'body' => $model->toSearchArray(),
    ]);
}

public function deleted ($model)
{
    $this->elasticsearch->delete([
        'index' => $model->getSearchIndex(),
        'type' => $model->getSearchType(),
        'id' => $model->id,
    ]);
} 
```

Enter fullscreen mode Exit fullscreen mode

第一部分是将数据导入 Elasticsearch，第二部分是从 Elasticsearch 获取数据，并在 Laravel 端呈现出来。Tony 的文章使用了一个存储库来与 Elasticsearch 进行交互。在储存库中，提供了以下方法来完成搜索部分。

```
private function searchOnElasticsearch (string $query): array
{
    $instance = new Article;

    $items = $this->search->search([
        'index' => $instance->getSearchIndex(),
        'type' => $instance->getSearchType(),
        'body' => [
           'query' => [
              'multi\_match' => [
              'fields' => ['title', 'body', 'tags'],
                    'query' => $query,
                ],
            ],
        ],
    ]);

return $items;
} 
```

Enter fullscreen mode Exit fullscreen mode

它返回一个数组，其中包含从 Elasticsearch 中检索到的数据，这些数据又可以被 Laravel 用作数据源。

这是我使用 Elasticsearch 的第一步，我喜欢它设置起来如此简单。下一步是在个人项目中实现这一点，同时尝试学习更多关于 Elasticsearch 的知识。