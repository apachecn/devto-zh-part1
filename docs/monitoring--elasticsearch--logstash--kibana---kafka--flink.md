# 监控=(elastic search+Logstash+Kibana)+Kafka * Flink

> 原文：<https://dev.to/lukaszkuczynski/monitoring--elasticsearch--logstash--kibana---kafka--flink>

我曾经是一名普通的业务开发人员。Delphi，然后是 Java。最近在某个项目不太有趣的地方工作，我得出结论:我想在这里带来真正的商业价值。

然后 **Elasticsearch** 出现在我的脑海里…

我在先前的地方用它工作，但是在这里，看！没人用 ES。 [**码头工人**](https://docs.docker.com/) 是这段旅程的里程碑之一。现在，有几十个应用程序在运行，我无法想象手动运行每个应用程序所需的工作量。现在吗？这是小菜一碟。

使用准备好的 Docker 图像，我能够构建监控系统，提供**历史**和**实时分析**。我来介绍一下，有哪些积木。我还将提供一些示例 docker-compose 脚本和配置文件。不可否认的配置简易性证明。

## 文件和资源被监控…

**节拍**在听。这是他们的任务。在他们的帮助下，很容易监控资源，如服务器的状态，文件或网络应用程序。说出某个东西的名字，[他们就会监控那个](https://www.elastic.co/products/beats)。我需要的一切都可以用 beats 轻松完成，比如 Filebeat 和 Metricbeat。这个小型的 Go-powered 应用程序正在仔细观察某个地方正在发生的事情——*输入*，并忠实地向 _ 输出报告。_Beats 可以直接向 ELK 系统的 heart elastic search 发送数据，也可以通过 Logstash 间接发送数据。正在配置？在 Yaml 中可以这样做

```
filebeat.prospectors:
- input_type: log
  paths:
  - /var/log/*.log
output.logstash:
  hosts: ["127.0.0.1:5044"] 
```

## …然后解析…

来自 Filebeat 的消息可以使用 [**grok** 表达式](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html)进行解析。这或多或少类似于众所周知的正则表达式。JSONs 说，数据被当作原始文本并生成文档。然后可以丰富文档，例如基于 GPS 坐标的城市名称。现在我们已经准备好了，我们可以存储它或进一步发送。接下来，您将看到示例性的 logstash 配置。

```
input {
  beats {
    port =&gt; 5044
  }
}

output {
  elasticsearch {
    hosts =&gt; "localhost:9200"
    index =&gt; "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
    document_type =&gt; "%{[@metadata][type]}"
  }
} 
```

这个配置假设我们想要监听直接来自 Logstash 的消息，并进一步将它们提供给 Elasticsearch。

## …达到持久…

持久性是[弹性搜索](https://www.elastic.co/products/elasticsearch)。这并不是数据库的完整含义。Elasticsearch 是下面有 Lucene 引擎的搜索引擎。您可以很容易地以接近实时的方式浏览它，使用所有丰富的全文搜索进行查询。在 docker-ready 环境中运行分布式 Elasticsearch 可能很简单，如下所示:

```
version: '2'
services:
  elasticsearch1:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.5.0
    container_name: elasticsearch1
    volumes:
      - esdata1:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
  elasticsearch2:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.5.0
    volumes:
      - esdata2:/usr/share/elasticsearch/data
    networks:
      - esnet 
```

..然后开火一次。

## …在基巴纳浏览！

Kibana 正是这一切的 GUI 工具。如果您对通过 Postmans 或其他工具进行 REST 查询不太感兴趣，可以查看一下。我们是人类。在进行一次 Udacity 培训时，[上了一堂关于数据可视化的很棒的课](http://lukcreates.pl/data-science/data-visualization-is-data-communication/)。以男人容易理解的方式呈现有意义的信息是多么重要。

## 真实时间？没问题

并行地，整个系统将它放入数据流中。数据流是在完全相同的 Filebeat 中产生的，这将它推向 [**卡夫卡**平台](https://kafka.apache.org/)的流。然后消费者 [**Flink**](https://flink.apache.org/) 应用程序收集消息并将其放入窗口或基于时间的桶中，然后将其转发给警报机器。然后可以设置一些逻辑，即*当 M 秒内出现 N 条消息时，提醒我。*

## 总结

如果没有 Docker 准备好的图像，构建所有这些块就不会那么容易。在我看来，监控系统将会越来越受欢迎。保持警惕。还有关心！