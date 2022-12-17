# 在 Java 中调试 Elasticsearch 查询:如何将查询转换为 JSON

> 原文：<https://dev.to/committedsw/debugging-elasticsearch-queries-in-java-how-to-convert-queries-to-json-1f1f>

[![Code Data Programming Code Computer Programming cc zero](img/b1575f892674fe6207fe819a6567cd9f.png)T2】](///static/Code-Data-Programming-Code-Computer-Programming-cc-zero-83e60fb3a72b66edc7930573fd279778-a09d4.jpg)

Elasticsearch 有很好的文档，有详细的描述和大量的例子。 [Elasticsearch Java API](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-query-dsl.html) 有方便的构建器类，从`org.elasticsearch.index.query.QueryBuilders`就可以访问，以一种直观流畅的方式进行查询和聚合，这是 Java 惯用的方式。

然而，不直观地说，`QueryBuilder`和`AggregationBuilder`子类没有实现`toString`，这使得调试和分析这些 Java 调用比您预期的更困难，因为并不总是清楚它们与 Elasticsearch 文档中广泛使用的 Elasticsearch-native JSON 格式相比如何。当查询的构造超出您的控制范围时，这个问题会变得更加复杂！

我们可以用 JSON 格式查看查询的一种方法是启用 Elasticsearch 的[慢速日志](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-slowlog.html)，但是还有一种更简单的方法，使用 Java Elasticsearch API 来实现我们想要的:

```
import java.io.IOException;
import org.elasticsearch.common.xcontent.ToXContent;
import org.elasticsearch.common.xcontent.XContentBuilder;
import org.elasticsearch.common.xcontent.json.JsonXContent;

...

public static void toJSON(ToXContent queryBuilder) {
  try {
    XContentBuilder builder =
        queryBuilder.toXContent(JsonXContent.contentBuilder(), ToXContent.EMPTY_PARAMS);
    log.info(builder.string());
  } catch (IOException e) {
    log.error("Failed to log elasticsearch query", e);
  }
}

... 
```

这可以用于实现`QueryBuilder`或`AggregationBuilder`的类，因为它们都扩展了`ToXContent`接口。

当使用 Spring Data Elasticsearch 时，我们可以使用相同的方法，通过调用`NativeSearchQueryBuilder.getQuery()`、`NativeSearchQueryBuilder.getFilter()`或`NativeSearchQueryBuilder.getAggregations()`，并使用相同的方法传递结果对象。

现在我们可以看到将发送给 Elasticsearch 的确切查询:

`{ "term" : { "user" : "Kimchy" } }`

如果您正在使用 [Kibana](https://www.elastic.co/products/kibana) 查看您的索引，请尝试复制&将您的查询粘贴到 Discover 页面上以供进一步分析。