# 将任意 JSON 索引到 Elasticsearch

> 原文：<https://dev.to/chrismwendt/indexing-arbitrary-json-into-elasticsearch-28m7>

使用[嵌套数据类型](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/nested.html)、[多字段](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/multi-fields.html)、 [`ignore_malformed`](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/ignore-malformed.html) ，并添加一个预处理步骤将嵌套的 JSON 展平为键值对，可以将任意 JSON(即使有类型冲突)索引到 Elasticsearch 中。

指标定义:

```
{  "settings":  {  "mapper.dynamic":  false  },  "mappings":  {  "sometype":  {  "dynamic":  "strict",  "_all"  :  {  "enabled"  :  true  },  "properties":  {  "kv_pairs":  {  "type":  "nested",  "properties":  {  "key":  {  "type":  "string",  "index":  "not_analyzed"  },  "value":  {  "type":  "string",  "fields":  {  "raw_string":  {  "type":  "string",  "index":  "not_analyzed"  },  "analyzed_string":  {  "type":  "string",  "index":  "analyzed"  },  "date":  {  "type":  "date",  "ignore_malformed":  true  },  "long":  {  "type":  "long",  "ignore_malformed":  true  },  "double":  {  "type":  "double",  "ignore_malformed":  true  }  }  }  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

预处理步骤示例:

```
$ echo '{"integer":3,"nested":{"inner":"object"}}' | \
    jq '[leaf_paths as $path | {"key": $path | map(tostring) | join("."), "value": getpath($path)}] | {kv_pairs: .}'
{
  "kv_pairs": [
    {
      "key": "integer",
      "value": 3
    },
    {
      "key": "nested.inner",
      "value": "object"
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

示例查询:

```
{  "query":  {  "nested":  {  "path":  "kv_pairs",  "query":  {  "bool":  {  "must":  [  {  "match":  {  "kv_pairs.key.raw_string":  "nested.inner"  }  },  {  "match":  {  "kv_pairs.value.raw_string":  "object"  }  }  ]  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

*   将 JSON 展平成键-值对是必要的，以避免[映射爆炸](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion)
*   嵌套类型对于关联键-值对是必要的，这样`"bool":{"must":[...]}`子句就可以对单独的键-值对进行操作
*   `ignore_malformed`抑制弹性搜索映射冲突