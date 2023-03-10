# 自动将供应商描述作为 Shopify 产品的前缀

> 原文：<https://dev.to/adamkdean/automatically-prefix-vendor-description-to-shopify-products-4jf3>

我目前正在修改一个使用[shoppiy](http://www.shopify.com)的网站，这是一个很好的在线电子商务平台，我发现虽然起初这个系统看起来有点限制性，但一旦你掌握了窍门，它实际上是很有可塑性的。这有点像黑客攻击，但是有了正确的黑客攻击和一些黑客攻击，它实际上可以很好地工作。

下面的代码片段遍历产品所属的集合列表，如果集合等于产品的供应商，则显示供应商描述。

```
{% comment %} 
    This will show the vendors description above the items
{% endcomment %}

{% for c in product.collections %}
    {% if c.title == product.vendor %}
        {{ c.description }}
    {% endif %}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想让一个品牌的标志出现在该品牌的所有商品上，而不需要手动操作，这是非常好的。我使用的另一个技巧是制作一个*智能收藏库*,它将所有带有供应商名称的商品都分配到同名智能收藏库中。

供应商为`Blackrock`的产品就是一个例子。名为`Blackrock`的智能收藏将包含供应商为`Blackrock`的所有商品，并且该智能收藏的描述还可以包含描述中的品牌图像。将上面的代码添加到`product.liquid`页面可以在产品描述上方自动获得一个品牌图像，如下所示:

[![Example](img/3fddae6d1d2b4a5d7b728730aab26300.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fNrgKDid--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/96Iiz7c.png)

简单而有效。