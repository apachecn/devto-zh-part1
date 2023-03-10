# 弹簧数据弹性搜索和地理点

> 原文：<https://dev.to/jmlw/spring-data-elasticsearch-and-geopoints-21dm>

[![Spring Data Elasticsearch and GeoPoints](img/f4ca4c670cf245533e8ccbd3764b5187.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2XhuHWXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1524146128017-b9dd0bfd2778%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

一些背景故事...我在开发一个 Java 应用程序，它有一个棱角分明的 1.x 前端。基础项目由 JHipster 生成，并使用 PostgreSQL 作为数据库和 Spring Boot。

应用程序很大程度上依赖于能够从客户端返回“靠近”所提供位置的项目列表。所以实际上只是一个地理空间搜索。为了做到这一点，我决定使用 Elasticsearch 的内置地理功能。我认为最简单的方法是使用我的 JPA 实体，它被注释为 Hibernate 实体，只要我使用一个字符串来表示我的 geopoint，它就可以兼作我的搜索引擎实体。设置起来非常简单，只需连接纬度、逗号和经度，或者相反，这取决于用例或符合 Elasticsearch 的要求。现在，我们可以轻松地将它存储在数据库中，而无需序列化程序和反序列化程序，并且它应该可以通过 Elasticsearch 开箱即用。

我用虚拟数据写了几个测试，以确保我可以进行基于地理的搜索，并准确返回我期望的结果。也就是说，当我搜索芝加哥环路 1 公里范围内的结果时，我不会返回位于纽约的纬度/经度。当我做测试时，我只看到红色。我对此感到非常沮丧。我完全遵循了 Spring Data Elasticsearch 开发人员在测试中的做法。我以为我已经设置好了。我确保我的 geopoint 字符串用`@GeoPointField`注释，并且我的实体被注释为用于 ElasticSearch 的`@Document`。但是，无论我怎么努力，Elasticsearch 都无法将我的字符串作为地理点处理。

然后我又看了一下 github 上 Spring Data Elasticsearch 的文档和测试。你可以看看 LocationMarkerEntity.java 的[实体对他们的测试作为例子。在测试中，他们能够使用 geopoint 字符串字段的 geo 空间查询返回项目。我的实体和他们的实体之间的唯一区别是在`@Document`注释上的一些附加选项。我以为既然我在字符串上使用了`@GeoPointField`注释，它就能顺利工作，然而事实并非如此。正如您在下面看到的，Spring 数据 Elasticsearch 的源也在`@Document`注释中包含了这个`type = "geo-annotation-point-type"`选项。](https://github.com/spring-projects/spring-data-elasticsearch/blob/b2f0300856cb3dd9bfeade3e1db26da96bc2d88a/src/test/java/org/springframework/data/elasticsearch/core/geo/LocationMarkerEntity.java) 

```
package org.springframework.data.elasticsearch.core.geo;

import org.springframework.data.elasticsearch.annotations.Document;
import org.springframework.data.elasticsearch.annotations.GeoPointField;

@Document(indexName = "test-geo-index", type = "geo-annotation-point-type", shards = 1, replicas = 0, refreshInterval = "-1")
public class LocationMarkerEntity {
    // some code omitted for berevity
    @GeoPointField
    private String locationAsString;

    @GeoPointField
    private double[] locationAsArray;
    // omitted
} 
```

我搜索了[Spring Data elastic search docs](http://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/)，但是我没有找到对这个类型参数的引用。我粗略地搜索了一下 Elasticsearch 文档，但没有找到任何东西。我还浏览了一下[Spring Data elastic search source](https://github.com/spring-projects/spring-data-elasticsearch)，看看这个参数是否在`@Document`注释中定义过，但是我没有找到。我可能在文档或源代码中漏掉了它，但这导致了令人沮丧的几天，因为我花时间试图找出为什么我的地理索引不能正常工作。

希望这能帮助其他有同样问题的人使用 Spring 数据弹性搜索框架。