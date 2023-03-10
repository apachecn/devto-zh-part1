# MongoDB 3.6 中的模式验证

> 原文：<https://dev.to/kenwalger/schema-validation-in-mongodb-36-99d>

MongoDB 3.6 在新版本中带来了许多很棒的新特性。我已经在以前的帖子中介绍过[更改流](https://dev.to/kenwalger/change-streams-coming-soon-in-mongodb-36-967)和[可重试写操作](https://dev.to/kenwalger/retryable-writes-in-mongodb-27l)。这篇文章将介绍一个特性，它扩展了 MongoDB 3.2 的[文档验证](https://docs.mongodb.com/manual/core/document-validation/)特性，即模式验证。模式验证允许团队为每个集合定义一个规定的文档结构。

有时候需要实施严格的数据结构和内容，即使是使用 [MongoDB](https://www.mongodb.com) 提供的灵活模式和[文档数据模型](https://www.kenwalger.com/blog/nosql/document-model/)。模式验证允许为每个集合定义规定的文档结构。如果一个人试图插入或更新一个*不*符合所应用的结构的文档，该操作可以被拒绝。文档结构的规则基于[J](http://json-schema.org/)T8】子模式草案规范。

### 模式验证

在深入讨论特性优势之前，让我们快速了解一下这是如何工作的。

想象一个食物食谱的集合。每份食谱都有食谱名称、份数、烹饪方法、配料和说明清单。对于配料，我们希望强制要求对配料的任何准备工作都有一个数字量、数量度量、配料名称和可选值，例如“去皮”或“[布朗索瓦兹](https://www.amazon.com/gp/product/0470421355/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0470421355&linkCode=as2&tag=kenwalgersite-20&linkId=d679d531adba3349c14bcf9f8a731651) [![](img/5ae19dd14e379d78f8a6959508ef88db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WJiv8n-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kenwalger.com/blog/ir-na.amazon-adsystem.com/e/ir%3Ft%3Dkenwalgersite-20%26l%3Dam2%26o%3D1%26a%3D0470421355) ”。对于我们这里的例子，我的选项并不包括所有的选项，只是作为例子。希望以前的厨艺同事见谅。

我们将首先创建一个`recipes`集合，并使用`validator`选项和新的 [$jsonSchema](https://docs.mongodb.com/master/reference/operator/query/jsonSchema/#op._S_jsonSchema) 操作符来分配模式验证规则。

```
db.createCollection( "recipes",
{
  validator: 
    {
      $jsonSchema:
        {
          bsonType: "object",
          required: ["name", "servings", "ingredients"],
          additionalProperties: false,
          properties:
            {
              _id: {},
              name: {
                bsonType: "string",
                description: "'name' is required and is a string"
                    },
              servings: {
                bsonType: ["int", "double"],
                minimum: 0,
                description: "'servings' is required and must be an integer greater than zero."
                    },
              cooking_method: {
                 enum: ["broil", "grill", "roast", "bake", "saute", "pan-fry", "deep-fry", "poach", "simmer", "boil", "steam", "braise", "stew"],
              description: "'cooking_method' is optional but, if used, must be one of the listed options."
                    },
              ingredients: 
              {
                bsonType: ["array"],
                minItems: 1,
                maxItems: 50,
                items: {
                  bsonType: ["object"],     
                  required: ["quantity", "measure", "ingredient"],
                  additionalProperties: false,
                  description: "'ingredients' must contain the stated fields.",
                  properties: 
                  {
                    quantity: {
                      bsonType: ["double", "decimal"],
                      description: "'quantity' is required and is of double or decimal type"
                            },
                    measure: {
                      enum: ["tsp", "Tbsp", "cup", "ounce", "pound", "each"],
                      description: "'measure' is required and can only be one of the given enum values"
                            },
                    ingredient: {
                      bsonType: "string",
                      description: "'ingredient' is required and is a string"
                            },
                    format: {
                      bsonType: "string",
                      description: "'format' is an optional field of type string"
                            }
                  }
              }
           }
        }
     }
   }
}) 
```

我们的`validator`可以包含更多的[规则](https://docs.mongodb.com/master/reference/operator/query/jsonSchema/#doc-insert-schema-validation)，这超出了模式验证介绍的范围。但是，我们可以在这个小例子中看到这个特性有多么强大。让我们看看一些要插入的示例文档，看看如果我们尝试将的[文档](https://docs.mongodb.com/manual/core/document/index.html)插入到[集合](https://docs.mongodb.com/manual/core/databases-and-collections/index.html)中会发生什么。

###### 样本插入

```
db.recipes.insertOne({name: "Chocolate Sponge Cake Filling", 
servings: 4, 
ingredients: [{quantity: 7, measure: "ounce", ingredient: "bittersweet chocolate", format: "chopped"}, 
{quantity: 2, measure: "cup", ingredient: "heavy cream"}
]}) 
```

这个插入是可行的，因为它以正确的格式覆盖了所有必需的字段。它也不包括任何无效的额外字段，这些字段将被禁止，因为我们已经设置了`additionalProperties: false`。然而，如果我们试图插入下面的文档，我们会得到一个错误。

```
db.recipes.insertOne({name: "Chocolate Sponge Cake Filling", 
servings: 4, 
ingredients: [{quantity: 7, measure: "ounce", ingredient: "bittersweet chocolate", format: "chopped"}, 
{quantity: 2, measure: "cup", ingredient: "heavy cream"}],
directions: "Boil cream and pour over chocolate. Stir until chocolate is melted."
}) 
```

因为我们在配方文档中添加了一个`directions`字段，所以设置了`additionalProperties: false`的插入将会失败。我应该在模式验证中添加一个可选的`directions`字段，考虑到方向的确很重要。

### 模式验证的好处

即使有了这些基本的例子，我认为模式验证显然是一个很好的新增强。MongoDB 中动态模式的灵活性现在可以很容易地与对整个集合的数据治理控制结合起来。虽然这样做有很多实际的好处，但这里有一些具体的好处。

1.  应用程序逻辑简化。有了集合外观的严格定义，应用程序就不需要处理保证和相关的错误。
2.  数据控制。客户端应用程序必须遵守集合规定的规则。不需要担心数据被更新或插入不正确的字段名称或数据类型。
3.  政府合规。各种行业和地区的应用程序和数据模型都需要以特定的格式存储数据。例如，欧盟的[欧盟通用数据保护法规](https://www.amazon.com/gp/product/184928945X/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=184928945X&linkCode=as2&tag=kenwalgersite-20&linkId=da741c9e3ba84c6d499cac9f9a96a5bd) ![](img/7b9b16b1f4d5a8fe711d627c3ca7dce6.png)。

### 总结起来

使用 MongoDB 3.6 模式验证，管理员可以对数据库进行可调控制。不符合一组规定条件的文档可以被拒绝或仍然被书写，并记录一条关于该操作的消息。它还带来了基于模式定义进行查询的能力，例如，允许对所有不符合模式的文档进行搜索。

模式验证是 MongoDB 3.6 版本中另一个令人兴奋的特性。我强烈建议您[下载](https://www.monogdb.com/download-center)并试用它。我打赌你会和我一样对此感到兴奋。

* * *

在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息。或者更好的是，注册电子邮件列表，以便在您的邮箱中获得更新！

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问 MongoDB 要文档的定义？”并得到有益的回应。如果你也想更新你的烹饪知识，我还为回声报创造了一个[烹饪技巧](https://www.echoskillstore.com/Kens-Culinary-Glossary/B072JXS3L5)。

[![Facebook](img/79cfd3c9812f2a2b24840e107dfe0e1a.png "Share on Facebook")](http://www.facebook.com/sharer.php?u=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-validation-mongodb-3-6%2F&t=Schema%20Validation%20in%20MongoDB%203.6&s=100&p%5Burl%5D=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-validation-mongodb-3-6%2F&p%5Bimages%5D%5B0%5D=https%3A%2F%2Fi2.wp.com%2Fwww.kenwalger.com%2Fblog%2Fwp-content%2Fuploads%2F2017%2F11%2Fdocument_validation_feature-e1511822206981.png%3Ffit%3D125%252C125%26ssl%3D1&p%5Btitle%5D=Schema%20Validation%20in%20MongoDB%203.6)[![twitter](img/e3a323cb1ef89a3b7bfbb40f5a5800cd.png "Share on Twitter")](https://twitter.com/intent/tweet?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-validation-mongodb-3-6%2F&text=Hey%20check%20this%20out)[![google_plus](img/9a7da8faca1a1fde7772bc97d1ffaea0.png "Share on Google+")](https://plus.google.com/share?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-validation-mongodb-3-6%2F)[![reddit](img/67ebb1b320193a9bf3c562f5d77f04df.png "Share on Reddit")](http://www.reddit.com/submit?url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-validation-mongodb-3-6%2F&title=Schema%20Validation%20in%20MongoDB%203.6)[![linkedin](img/f9684edc4e8f6cf298d3fa21f5869127.png "Share on Linkedin")](http://www.linkedin.com/shareArticle?mini=true&url=https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-validation-mongodb-3-6%2F&title=Schema%20Validation%20in%20MongoDB%203.6)[![mail](img/265476ced77e071f0a8d25be8996a26b.png "Share by email")](mailto:?subject=Schema%20Validation%20in%20MongoDB%203.6&body=Hey%20check%20this%20out:%20https%3A%2F%2Fwww.kenwalger.com%2Fblog%2Fnosql%2Fmongodb%2Fschema-validation-mongodb-3-6%2F)

MongoDB 3.6 中的[模式验证一文最早出现在肯·w·阿尔杰](https://www.kenwalger.com/blog/nosql/mongodb/schema-validation-mongodb-3-6/)的[博客上。](https://www.kenwalger.com/blog)