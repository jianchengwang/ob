---
title: elastic-stack
slug: elastic-stack
author: [jianchengwang]
date: 2021-02-26
excerpt: "ELK = ElasticSearch + Logstash + Kibana，随着Beats的加入，原来的ELK体系变成了ElasticStack，即ElasticStack = ElasticSearch + Logstash + Kibana + Beats"
draft: false
tags: [middleware]
---

ELK = ElasticSearch + Logstash + Kibana，随着Beats的加入，原来的ELK体系变成了ElasticStack，即ElasticStack = ElasticSearch + Logstash + Kibana + Beats，感兴趣的可以建议直接看官方文档，[请戳](https://www.elastic.co/guide/index.html)

<!--more-->

## 概述

![](https://blog.res.jianchengwang.info/posts/elastic-stack/es-build.png)

**Elasticsearch**

Elasticsearch 基于java，是个开源分布式搜索引擎，它的特点有：分布式，零配置，自动发现，索引自动分片，索引副本机制，restful风格接口，多数据源，自动搜索负载等。

**Logstash**

Logstash 基于java，是一个开源的用于收集,分析和存储日志的工具。

**Kibana**

Kibana 基于nodejs，也是一个开源和免费的工具，Kibana可以为 Logstash 和 ElasticSearch 提供的日志分析友好的Web 界面，可以汇总、分析和搜索重要数据日志。

**Beats**

Beats是elastic公司开源的一款采集系统监控数据的代理agent，是在被监控服务器上以客户端形式运行的数据收集器的统称，可以直接把数据发送给Elasticsearch或者通过Logstash发送给Elasticsearch，然后进行后续的数据分析活动。Beats由如下组成:

- Packetbeat：是一个网络数据包分析器，用于监控、收集网络流量信息，Packetbeat嗅探服务器之间的流量，解析应用层协议，并关联到消息的处理，其支 持ICMP (v4 and v6)、DNS、HTTP、Mysql、PostgreSQL、Redis、MongoDB、Memcache等协议；
- Filebeat：用于监控、收集服务器日志文件，其已取代 logstash forwarder；
- Metricbeat：可定期获取外部系统的监控指标信息，其可以监控、收集 Apache、HAProxy、MongoDB MySQL、Nginx、PostgreSQL、Redis、System、Zookeeper等服务；

> Beats和Logstash其实都可以进行数据的采集，但是目前主流的是使用Beats进行数据采集，然后使用 Logstash进行数据的分割处理等，早期没有Beats的时候，使用的就是Logstash进行数据的采集。

## 安装

### ELK

我这边使用docker部署，便于后面迁移，

```yaml
version: '3'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.11.1
    container_name: elasticsearch
    environment:
      - "cluster.name=elasticsearch" #集群名称为elasticsearch
      - "discovery.type=single-node" #单节点启动
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m" #jvm内存分配为512MB
    volumes:
      - ./elk/elasticsearch/plugins:/usr/share/elasticsearch/plugins
      - ./elk/elasticsearch/data:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks: #网络命名空间  用于隔离服务
      - elk
  kibana:
    image: docker.elastic.co/kibana/kibana:7.11.1
    container_name: kibana
    links:
      - elasticsearch:es #配置elasticsearch域名为es
    depends_on:
      - elasticsearch
    environment:
      - "elasticsearch.hosts=http://es:9200" #因为上面配置了域名，所以这里可以简写为http://es:9200
    ports:
      - 5601:5601
    networks:
      - elk
  logstash:
    image: docker.elastic.co/logstash/logstash:7.11.1
    container_name: logstash
    volumes:
      - ./elk/logstash/config/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    depends_on:
      - elasticsearch
    links:
      - elasticsearch:es
    ports:
      - 4560:4560
    networks: #网络命名空间  用于隔离服务
      - elk

```

###  ElasticSearchHead

**docker安装**

```shell
#拉取镜像
docker pull mobz/elasticsearch-head:5
#创建容器
docker create --name elasticsearch-head -p 9100:9100 mobz/elasticsearch-head:5
#启动容器
docker start elasticsearch-head
```

**chrome插件安装**，打开chrome的应用商店，即可安装 https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm

##  ElasticSearch

### 基本概念

**索引**

- 索引（index）是Elasticsearch对逻辑数据的逻辑存储，所以它可以分为更小的部分。
- 可以把索引看成关系型数据库的表，索引的结构是为快速有效的全文索引准备的，特别是它不存储原始值。
- Elasticsearch可以把索引存放在一台机器或者分散在多台服务器上，每个索引有一或多个分片（shard），每个分片可以有多个副本（replica）。

**文档**

- 存储在Elasticsearch中的主要实体叫文档（document）。用关系型数据库来类比的话，一个文档相当于数据库表中的一行记录。
- Elasticsearch和MongoDB中的文档类似，都可以有不同的结构，但Elasticsearch的文档中，相同字段必须有相同类型。
- 文档由多个字段组成，每个字段可能多次出现在一个文档里，这样的字段叫多值字段（multivalued）。 每个字段的类型，可以是文本、数值、日期等。字段类型也可以是复杂类型，一个字段包含其他子文档或者数 组。

**映射**

所有文档写进索引之前都会先进行分析，如何将输入的文本分割为词条、哪些词条又会被过滤，这种行为叫做 映射（mapping）。一般由用户自己定义规则。

**文档类型**

- 在Elasticsearch中，一个索引对象可以存储很多不同用途的对象。例如，一个博客应用程序可以保存文章和评 论。
- 每个文档可以有不同的结构。
- 不同的文档类型不能为相同的属性设置不同的类型。例如，在同一索引中的所有文档类型中，一个叫title的字段必须具有相同的类型。

### 元数据

一个文档不只有数据。它还包含了元数据(metadata)——关于文档的信息。三个必须的元数据节点是：

| 节点   | 说明               |
| ------ | ------------------ |
| _index | 文档存储的地方     |
| _type  | 文档代表的对象的类 |
| _id    | 文档的唯一标识     |

#### index

索引(index)类似于关系型数据库里的“数据库”——它是我们存储和索引关联数据的地方。

> 提示：事实上，我们的数据被存储和索引在分片(shards)中，索引只是一个把一个或多个分片分组在一起的逻辑空间。然而，这只是一些内部细节——我们的程序完全不用关心分片。对于我们的程序而言，文档存储在索引(index)中。剩下的细节由Elasticsearch关心既可。

#### _type

在应用中，我们使用对象表示一些“事物”，例如一个用户、一篇博客、一个评论，或者一封邮件。每个对象都属于一个类(class)，这个类定义了属性或与对象关联的数据。user 类的对象可能包含姓名、性别、年龄和Email地址。 在关系型数据库中，我们经常将相同类的对象存储在一个表里，因为它们有着相同的结构。同理，在Elasticsearch 中，我们使用相同类型(type)的文档表示相同的“事物”，因为他们的数据结构也是相同的。

每个类型(type)都有自己的映射(mapping)或者结构定义，就像传统数据库表中的列一样。所有类型下的文档被存储在同一个索引下，但是类型的映射(mapping)会告诉Elasticsearch不同的文档如何被索引。

_type 的名字可以是大写或小写，不能包含下划线或逗号。我们将使用blog 做为类型名。

#### _id

id仅仅是一个字符串，它与_index 和_type 组合时，就可以在Elasticsearch中唯一标识一个文档。当创建一个文 档，你可以自定义_id ，也可以让Elasticsearch帮你自动生成（32位长度）

### RESTful API

在Elasticsearch中，提供了功能丰富的RESTful API的操作，包括基本的CRUD、创建索引、删除索引等操作。

**创建非结构化索引**

在Lucene中，创建索引是需要定义字段名称以及字段的类型的，在Elasticsearch中提供了非结构化的索引，就是不需要创建索引结构，即可写入数据到索引中，实际上在Elasticsearch底层会进行结构化操作，此操作对用户是透明的。

#### 创建空索引

```
PUT haoke
{
  "settings": {
    "index": {
    "number_of_shards": "2", #分片数
    "number_of_replicas": "0" #副本数
    }
  }
}
```

#### 删除索引

```
DELETE haoke
{
  "acknowledged": true
}
```

#### 插入数据

> URL规则： POST /{索引}/{类型}/{id}

```
POST haoke/user/1001
{
  "id":1001,
  "name":"张三",
  "age":20,
  "sex":"男"
}
```

不指定id的话，会自动生成id

#### 更新数据

在Elasticsearch中，文档数据是不为修改的，但是可以通过覆盖的方式进行更新

```
PUT haoke/user/1001
{
  "id":1001,
  "name":"张三",
  "age":21,
  "sex":"女"
}
```

可以看到数据已经被覆盖了。问题来了，可以**局部更新**吗？ -- 可以的。前面不是说，文档数据不能更新吗？ 其实是这样的：在内部，依然会查询到这个文档数据，然后进行覆盖操作，步骤如下：

1. 从旧文档中检索JSON
2. 修改它
3. 删除旧文档
4. 索引新文档

```
#注意：这里多了_update标识
POST haoke/user/1001/_update
{
  "doc": {
    "age": 23
  }
}	
```

#### 删除数据

在Elasticsearch中，删除文档数据，只需要发起DELETE请求即可，不用额外的参数

```js
DELETE haoke/user/1001
```

删除一个文档也不会立即从磁盘上移除，它只是被标记成已删除。Elasticsearch将会在你之后添加更多索引的时候才会在后台进行删除内容的清理。【相当于批量操作】

#### 判断数据存在

```
HEAD haoke/user/1005
```



#### 简单搜索

##### 根据id搜索

```
GET haoke/user/1001
# result
{
  "_index" : "haoke",
  "_type" : "user",
  "_id" : "1001",
  "_version" : 1,
  "_seq_no" : 4,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "id" : 1001,
    "name" : "张三",
    "age" : 20,
    "sex" : "男"
  }
}
```

可以看到数据放在`_source`中，我们也可以只查`_source`

```
GET haoke/user/1001/_source
# result
{
  "id" : 1001,
  "name" : "张三",
  "age" : 20,
  "sex" : "男"
}

GET haoke/user/1001/_source?_source=id,name
# result
{
  "name" : "张三",
  "id" : 1001
}
```

##### 查询全部

```
GET haoke/user/_search?pretty
# result
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "haoke",
        "_type" : "user",
        "_id" : "1001",
        "_score" : 1.0,
        "_source" : {
          "id" : 1001,
          "name" : "张三",
          "age" : 20,
          "sex" : "男"
        }
      }
    ]
  }
}
```

注意，使用查询全部数据的时候，默认只会返回10条，

##### 关键字搜索

```
# 查询年龄等于20
GET haoke/user/_search?q=age:20
```

#### DSL搜索

Elasticsearch提供丰富且灵活的查询语言叫做DSL查询(Query DSL),它允许你构建更加复杂、强大的查询。 DSL(Domain Specific Language特定领域语言)以JSON请求体的形式出现

```
POST haoke/user/_search
{
  "query" : {
    "match" : {
    	"age" : 20
    }
  }
}
```

##### 条件查询

实现：查询年龄大于20岁的女性用户，

```
POST haoke/user/_search
{
  "query": {
    "bool": {
      "filter": {
          "range": {
              "age": {
              "gt": 20
          },
          "term": {
          	  "age": 20
          }
        }
      },
      "must": {
        "match": {
        	"sex": "女"
        }
      }
    }
  }
}
```

查询名字是王五或者李四的用户，并且高亮显示

```
POST haoke/user/_search
{
  "query": {
    "match": {
    	"name": "王五 李四"
    }
  },
  # 高亮显示
  "highlight": {
  	"fields": {
    	"name": {}
    }
  }
}
```

##### 聚合

在Elasticsearch中，支持聚合操作，类似SQL中的group by操作

```
POST haoke/user/_search
{
  "aggs": {
    "all_interests": {
      "terms": {
          "field": "age"
      }
    }
  }
}
# result
{
  ....
  "aggregations" : {
    "all_interests" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : 20,
          "doc_count" : 1
        },
        {
          "key" : 30,
          "doc_count" : 1
        }
      ]
    }
  }
}

```

#### 批量操作

##### 批量查询

```
POST haoke/user/_mget
{
	"ids" : [ "1001", "1002", "1003" ]
}
```

数据是否存在需要通过`found`字段判定

#####  _bulk操作

在Elasticsearch中，支持批量的插入、修改、删除操作，都是通过_bulk的api完成的

请求格式如下：

```
{ action: { metadata }}
{ request body }
{ action: { metadata }}
{ request body }
...
```

**批量插入**

```
POST haoke/user/_bulk
{"create":{"_index":"haoke","_type":"user","_id":2001}}
{"id":2001,"name":"name1","age": 20,"sex": "男"}
{"create":{"_index":"haoke","_type":"user","_id":2002}}
{"id":2002,"name":"name2","age": 20,"sex": "男"}
{"create":{"_index":"haoke","_type":"user","_id":2003}}
{"id":2003,"name":"name3","age": 20,"sex": "男"}
```

**批量删除**

```
POST haoke/user/_bulk
{"delete":{"_index":"haoke","_type":"user","_id":2001}}
{"delete":{"_index":"haoke","_type":"user","_id":2002}}
{"delete":{"_index":"haoke","_type":"user","_id":2003}}
```

其他操作就类似了。一次请求多少性能最高？

- 整个批量请求需要被加载到接受我们请求节点的内存里，所以请求越大，给其它请求可用的内存就越小。有一 个最佳的bulk请求大小。超过这个大小，性能不再提升而且可能降低。
- 最佳大小，当然并不是一个固定的数字。它完全取决于你的硬件、你文档的大小和复杂度以及索引和搜索的负 载。
- 幸运的是，这个最佳点(sweetspot)还是容易找到的：试着批量索引标准的文档，随着大小的增长，当性能开始 降低，说明你每个批次的大小太大了。开始的数量可以在1000~5000个文档之间，如果你的文档非常大，可以使用较小的批次。
- 通常着眼于你请求批次的物理大小是非常有用的。一千个1kB的文档和一千个1MB的文档大不相同。一个好的 批次最好保持在5-15MB大小间。

#### 分页

和 SQL 使用 `LIMIT` 关键字返回单个 `page` 结果的方法相同，Elasticsearch 接受 `from` 和 `size` 参数：

- **`size`**

  显示应该返回的结果数量，默认是 `10`

- **`from`**

  显示应该跳过的初始结果数量，默认是 `0`

如果每页展示 5 条结果，可以用下面方式请求得到 1 到 3 页的结果：

```
GET /_search?size=5
GET /_search?size=5&from=5
GET /_search?size=5&from=10
```

考虑到分页过深以及一次请求太多结果的情况，结果集在返回之前先进行排序。 但请记住一个请求经常跨越多个分片，每个分片都产生自己的排序结果，这些结果需要进行集中排序以保证整体顺序是正确的。

理解为什么深度分页是有问题的，我们可以假设在一个有 5 个主分片的索引中搜索。 当我们请求结果的第一页（结果从 1 到 10 ），每一个分片产生前 10 的结果，并且返回给 *协调节点* ，协调节点对 50 个结果排序得到全部结果的前 10 个。

现在假设我们请求第 1000 页—结果从 10001 到 10010 。所有都以相同的方式工作除了每个分片不得不产生前10010个结果以外。 然后协调节点对全部 50050 个结果排序最后丢弃掉这些结果中的 50040 个结果。

可以看到，在分布式系统中，对结果排序的成本随分页的深度成指数上升。这就是 web 搜索引擎对任何查询都不要返回超过 1000 个结果的原因。

所以es为了性能，限制了我们分页的深度，es目前支持的最大的 max_result_window = 10000， 即from + size <= 10000

这时候一般有两种解决方案，

**scroll**，能够解决深度分页的问题，但是其无法实现实时查询，即当scroll_id生成后无法查询到之后数据的变更，因为其底层原理是生成数据的快照。

**search_after**，在es-5.X之后才提供的，search_after 是一种假分页方式，根据上一页的最后一条数据来确定下一页的位置，同时在分页请求的过程中，如果有索引数据的增删改查，这些变更也会实时的反映到游标上。为了找到每一页最后一条数据，每个文档必须有一个全局唯一值，官方推荐使用 _uid 作为全局唯一值，但是只要能表示其唯一性就可以。

这个感兴趣的可以自行了解。

#### 映射

前面我们创建的索引以及插入数据，都是由Elasticsearch进行自动判断类型，有些时候我们是需要进行明确字段类型的，否则，自动判断的类型和实际需求是不相符的。

自动判断的规则如下：

| **JSON data type**                                           | **`"dynamic":"true"`**                             | **`"dynamic":"runtime"`**                          |
| ------------------------------------------------------------ | -------------------------------------------------- | -------------------------------------------------- |
| `null`                                                       | No field added                                     | No field added                                     |
| `true` or `false`                                            | `boolean`                                          | `boolean`                                          |
| `double`                                                     | `float`                                            | `double`                                           |
| `integer`                                                    | `long`                                             | `long`                                             |
| `object`1                                                    | `object`                                           | `object`                                           |
| `array`                                                      | Depends on the first non-`null` value in the array | Depends on the first non-`null` value in the array |
| `string` that passes [date detection](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#date-detection) | `date`                                             | `date`                                             |
| `string` that passes [numeric detection](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#numeric-detection) | `float` or `long`                                  | `double` or `long`                                 |
| `string` that doesn’t pass `date` detection or `numeric` detection | `text` with a `.keyword` sub-field                 | `keyword`                                          |

Elasticsearch支持的类型可以访问[mapping-types](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html)自行查看，这里不多赘述。

**查看mapping**

```
GET haoke/_mapping
```

**创建包含mapping的索引**

```
PUT haoke
{
  "settings": {
    "index": {
    "number_of_shards": "2", #分片数
    "number_of_replicas": "0" #副本数
    }
  },
  "mappings": {
  	"properties" : {
        "age" : {
          "type" : "long"
        },
        ...
  }
}
```

### 中文分词

分词就是指将一个文本转化成一系列单词的过程，也叫文本分析，在Elasticsearch中称之为Analysis

比如，指定分词器进行分词，

```
POST /_analyze
{
    "analyzer":"standard",
    "text":"hello world"
}
# result
{
  "tokens" : [
    {
      "token" : "hello",
      "start_offset" : 0,
      "end_offset" : 5,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "world",
      "start_offset" : 6,
      "end_offset" : 11,
      "type" : "<ALPHANUM>",
      "position" : 1
    }
  ]
}
```

Elasticsearch默认不支持中文分词，所以要引入第三方插件，比如IK、jieba、THULAC等，一般IK使用居多，

IK Analyzer是一个开源的，基于java语言开发的轻量级的中文分词工具包。从2006年12月推出1.0版开始，IKAnalyzer已经推出了3个大版本。最初，它是以开源项目Luence为应用主体的，结合词典分词和文法分析算法的中文分词组件。新版本的IK Analyzer 3.0则发展为面向Java的公用分词组件，独立于Lucene项目，同时提供了对Lucene的默认优化实现。

采用了特有的“正向迭代最细粒度切分算法“，具有80万字/秒的高速处理能力 采用了多子处理器分析模式，支持：英文字母（IP地址、Email、URL）、数字（日期，常用中文数量词，罗马数字，科学计数法），中文词汇（姓名、地名处理）等分词处理。 优化的词典存储，更小的内存占用。

IK分词器 Elasticsearch插件地址：https://github.com/medcl/elasticsearch-analysis-ik

```shell
cd elasticsearch/plugins/ik
unzip elasticsearch-analysis-ik-7.11.1.zip
docker-compose up -d
```

测试分词效果

```
POST /_analyze
{
  "analyzer": "ik_smart",
  "text": "我是中国人"
}
#result
{
  "tokens" : [
    {
      "token" : "我",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "CN_CHAR",
      "position" : 0
    },
    {
      "token" : "是",
      "start_offset" : 1,
      "end_offset" : 2,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "中国人",
      "start_offset" : 2,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 2
    }
  ]
}
```

### 全文搜索

全文搜索两个最重要的方面是：

- 相关性（Relevance） 它是评价查询与其结果间的相关程度，并根据这种相关程度对结果排名的一种能力，这 种计算方式可以是 TF/IDF 方法、地理位置邻近、模糊相似，或其他的某些算法。
- 分词（Analysis） 它是将文本块转换为有区别的、规范化的 token 的一个过程，目的是为了创建倒排索引以及查询倒排索引。

创建测试索引

> ES 7.4 默认不在支持指定索引类型，默认索引类型是_doc

```
PUT todo?include_type_name=true
{
  "settings":{
    "index":{
      "number_of_shards":"1",
      "number_of_replicas":"0"
    }
  },
  "mappings":{
    "person":{
      "properties":{
        "name":{
          "type":"text"
        },
        "age":{
          "type":"integer"
        },
        "mail":{
          "type":"keyword"
        },
        "hobby":{
          "type":"text",
          "analyzer":"ik_smart"
        }
      }
    }
  }
}
```

插入测试数据

```
POST todo/_bulk
{"index":{"_index":"todo","_type":"person"}}
{"name":"张三","age": 20,"mail": "111@qq.com","hobby":"羽毛球、乒乓球、足球"}
{"index":{"_index":"todo","_type":"person"}}
{"name":"李四","age": 21,"mail": "222@qq.com","hobby":"羽毛球、乒乓球、足球、篮球"}
{"index":{"_index":"todo","_type":"person"}}
{"name":"王五","age": 22,"mail": "333@qq.com","hobby":"羽毛球、篮球、游泳、听音乐"}
{"index":{"_index":"todo","_type":"person"}}
{"name":"赵六","age": 23,"mail": "444@qq.com","hobby":"跑步、游泳、篮球"}
{"index":{"_index":"todo","_type":"person"}}
{"name":"孙七","age": 24,"mail": "555@qq.com","hobby":"听音乐、看电影、羽毛球"}
```

#### 单词搜索

```
POST todo/person/_search
{
  "query":{
    "match":{
      "hobby":"听音乐"
    }
  },
  "highlight":{
    "fields":{
      "hobby":{
      }
    }
  }
}
# result
{
  "took" : 56,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 0.919734,
    "hits" : [
      {
        "_index" : "todo",
        "_type" : "person",
        "_id" : "lmyl53cBxfH-S_1kdlIz",
        "_score" : 0.919734,
        "_source" : {
          "name" : "孙七",
          "age" : 24,
          "mail" : "555@qq.com",
          "hobby" : "听音乐、看电影、羽毛球"
        },
        "highlight" : {
          "hobby" : [
            "<em>听音乐</em>、看电影、羽毛球"
          ]
        }
      },
      {
        "_index" : "todo",
        "_type" : "person",
        "_id" : "lGyl53cBxfH-S_1kdlIz",
        "_score" : 0.816522,
        "_source" : {
          "name" : "王五",
          "age" : 22,
          "mail" : "333@qq.com",
          "hobby" : "羽毛球、篮球、游泳、听音乐"
        },
        "highlight" : {
          "hobby" : [
            "羽毛球、篮球、游泳、<em>听音乐</em>"
          ]
        }
      }
    ]
  }
}
```

过程说明：

- 检查字段类型
  - 爱好 hobby 字段是一个 text 类型（ 指定了IK分词器），这意味着查询字符串本身也应该被分词。
- 分析查询字符串 。
  - 将查询的字符串 “听音乐” 传入IK分词器中，输出的结果是单个项听 音乐。因为只有一个单词项，所以 match 查询执行的是单个底层 term 查询。
- 查找匹配文档 。
  - 用 term 查询在倒排索引中查找 “听音乐” 然后获取一组包含该项的文档，本例的结果是文档：3 、5 。
- 为每个文档评分 。
  - 用 term 查询计算每个文档相关度评分 _score ，这是种将 词频（term frequency，即词 “听音乐” 在相关文档的hobby 字段中出现的频率）和 反向文档频率（inverse document frequency，即词 “听音乐” 在所有文档的hobby 字段中出现的频率），以及字段的长度（即字段越短相关度越高）相结合的计算方式。

#### 多词搜索

```
POST todo/person/_search
{
  "query":{
    "match":{
      "hobby":"听音乐 篮球"
    }
  },
  "highlight":{
    "fields":{
      "hobby":{
      }
    }
  }
}
# result
{
  "took" : 6,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 4,
      "relation" : "eq"
    },
    "max_score" : 1.319227,
    "hits" : [
      {
        "_index" : "todo",
        "_type" : "person",
        "_id" : "lGyl53cBxfH-S_1kdlIz",
        "_score" : 1.319227,
        "_source" : {
          "name" : "王五",
          "age" : 22,
          "mail" : "333@qq.com",
          "hobby" : "羽毛球、篮球、游泳、听音乐"
        },
        "highlight" : {
          "hobby" : [
            "羽毛球、<em>篮球</em>、游泳、<em>听音乐</em>"
          ]
        }
      },
      {
        "_index" : "todo",
        "_type" : "person",
        "_id" : "lmyl53cBxfH-S_1kdlIz",
        "_score" : 0.919734,
        "_source" : {
          "name" : "孙七",
          "age" : 24,
          "mail" : "555@qq.com",
          "hobby" : "听音乐、看电影、羽毛球"
        },
        "highlight" : {
          "hobby" : [
            "<em>听音乐</em>、看电影、羽毛球"
          ]
        }
      },
      {
        "_index" : "todo",
        "_type" : "person",
        "_id" : "lWyl53cBxfH-S_1kdlIz",
        "_score" : 0.5662492,
        "_source" : {
          "name" : "赵六",
          "age" : 23,
          "mail" : "444@qq.com",
          "hobby" : "跑步、游泳、篮球"
        },
        "highlight" : {
          "hobby" : [
            "跑步、游泳、<em>篮球</em>"
          ]
        }
      },
      {
        "_index" : "todo",
        "_type" : "person",
        "_id" : "k2yl53cBxfH-S_1kdlIz",
        "_score" : 0.502705,
        "_source" : {
          "name" : "李四",
          "age" : 21,
          "mail" : "222@qq.com",
          "hobby" : "羽毛球、乒乓球、足球、篮球"
        },
        "highlight" : {
          "hobby" : [
            "羽毛球、乒乓球、足球、<em>篮球</em>"
          ]
        }
      }
    ]
  }
}

```

可以看到，包含了“听音乐”、“篮球”的数据都已经被搜索到了。可是，搜索的结果并不符合我们的预期，因为我们想搜索的是既包含“听音乐”又包含“篮球”的用户，显然结果返回的“或”的关系。在Elasticsearch中，可以指定词之间的逻辑关系，如下：

```
POST todo/person/_search
{
  "query":{
    "match":{
      "hobby":{
        "query": "听音乐 篮球",
        "operator":"and"
      }
    }
  },
  "highlight":{
    "fields":{
      "hobby":{
      }
    }
  }
}
# result
{
  "took" : 4,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.319227,
    "hits" : [
      {
        "_index" : "todo",
        "_type" : "person",
        "_id" : "lGyl53cBxfH-S_1kdlIz",
        "_score" : 1.319227,
        "_source" : {
          "name" : "王五",
          "age" : 22,
          "mail" : "333@qq.com",
          "hobby" : "羽毛球、篮球、游泳、听音乐"
        },
        "highlight" : {
          "hobby" : [
            "羽毛球、<em>篮球</em>、游泳、<em>听音乐</em>"
          ]
        }
      }
    ]
  }
}

```

可以看到结果符合预期。

前面我们测试了“OR” 和 “AND”搜索，这是两个极端，其实在实际场景中，并不会选取这2个极端，更有可能是选取这种，或者说，只需要符合一定的相似度就可以查询到数据，在Elasticsearch中也支持这样的查询，通过 `minimum_should_match`来指定匹配度，如：75%；

```
POST todo/person/_search
{
  "query":{
    "match":{
      "hobby":{
        "query": "听音乐 篮球",
        "operator":"and",
        "minimum_should_match": "75%"
      }
    }
  },
  "highlight":{
    "fields":{
      "hobby":{
      }
    }
  }
}
```

#### 组合查询

在搜索时，也可以使用过滤器中讲过的bool组合查询，示例：

```
POST todo/person/_search
{
  "query":{
    "bool":{
      "must":{
        "match":{
          "hobby":"篮球"
        }
      },
      "must_not":{
        "match":{
          "hobby":"听音乐"
        }
      },
      "should":[
        {
          "match":{
            "hobby":"游泳"
          }
        }
      ]
  }
  },
  "highlight":{
    "fields":{
      "hobby":{

      }
    }
  }
}

```

> 上面搜索的意思是： 搜索结果中必须包含篮球，不能包含听音乐，如果包含了游泳，那么它的相似度更高。

> 评分的计算规则
>
> bool 查询会为每个文档计算相关度评分 _score ， 再将所有匹配的 must 和 should 语句的分数 _score 求和，最后除以 must 和 should 语句的总数。
>
> must_not 语句不会影响评分； 它的作用只是将不相关的文档排除。

默认情况下，should中的内容不是必须匹配的，如果查询语句中没有must，那么就会至少匹配其中一个。当然了，也可以通过minimum_should_match参数进行控制，该值可以是数字也可以的百分比

示例：

```
POST todo/person/_search
{
  "query":{
    "bool":{
      "should":[
        {
          "match":{
            "hobby":"游泳"
          }
        },
        {
          "match":{
            "hobby":"篮球"
          }
        },
        {
          "match":{
            "hobby":"听音乐"
          }
        }
      ],
      "minimum_should_match":2
    }
  },
  "highlight":{
    "fields":{
      "hobby":{

      }
    }
  }
}
```

minimum_should_match为2，意思是should中的三个词，至少要满足2个

#### 权重

有些时候，我们可能需要对某些词增加权重来影响该条数据的得分。如下：

搜索关键字为“游泳篮球”，如果结果中包含了“听音乐”权重为10，包含了“跑步”权重为2，

```
POST todo/person/_search
{
  "query":{
    "bool":{
      "must":{
        "match":{
          "hobby":{
            "query":"游泳篮球",
            "operator":"and"
          }
        }
      },
      "should":[
        {
          "match":{
            "hobby":{
              "query":"音乐",
              "boost":10
            }
          }
        },
        {
          "match":{
            "hobby":{
              "query":"跑步",
              "boost":2
            }
          }
        }
      ]
    }
  },
  "highlight":{
    "fields":{
      "hobby":{

      }
    }
  }
}

```

### 集群环境

#### 集群节点

ELasticsearch的集群是由多个节点组成的，通过cluster.name设置集群名称，并且用于区分其它的集群，每个节点通过node.name指定节点的名称。

在Elasticsearch中，节点的类型主要有4种：

- master节点
  - 配置文件中node.master属性为true(默认为true)，就有资格被选为master节点。master节点用于控制整个集群的操作。比如创建或删除索引，管理其它非master节点等。
- data节点
  - 配置文件中node.data属性为true(默认为true)，就有资格被设置成data节点。data节点主要用于执行数据相关的操作。比如文档的CRUD。
- 客户端节点
  - 配置文件中node.master属性和node.data属性均为false。
  - 该节点不能作为master节点，也不能作为data节点。
  - 可以作为客户端节点，用于响应用户的请求，把请求转发到其他节点
- 部落节点
  - 当一个节点配置tribe.*的时候，它是一个特殊的客户端，它可以连接多个集群，在所有连接的集群上执行 搜索和其他操作。

#### 搭建集群

```shell
#启动3个虚拟机，分别在3台虚拟机上部署安装Elasticsearch
mkdir /todo/es-cluster

#分发到其它机器
scp -r es-cluster elsearch@192.168.40.134:/todo

#node01的配置：
cluster.name: es-cluster
node.name: node01
node.master: true
node.data: true
network.host: 0.0.0.0
http.port: 9200
discovery.zen.ping.unicast.hosts: ["192.168.40.133","192.168.40.134","192.168.40.135"]
# 最小节点数
discovery.zen.minimum_master_nodes: 2
# 跨域专用
http.cors.enabled: true
http.cors.allow-origin: "*"

#node02的配置：
cluster.name: es-cluster
node.name: node02
node.master: true
node.data: true
network.host: 0.0.0.0
http.port: 9200
discovery.zen.ping.unicast.hosts: ["192.168.40.133","192.168.40.134","192.168.40.135"]
discovery.zen.minimum_master_nodes: 2
http.cors.enabled: true
http.cors.allow-origin: "*"

#node03的配置：
cluster.name: es-cluster
node.name: node02
node.master: true
node.data: true
network.host: 0.0.0.0
http.port: 9200
discovery.zen.ping.unicast.hosts: ["192.168.40.133","192.168.40.134","192.168.40.135"]
discovery.zen.minimum_master_nodes: 2
http.cors.enabled: true
http.cors.allow-origin: "*"

#分别启动3个节点
./elasticsearch
```

启动后，可以在ElasticsearchHead查看集群的状态，

集群有三种颜色，

| 颜色   | 意义                                       |
| ------ | ------------------------------------------ |
| green  | 所有主要分片跟复制分片都可用               |
| yellow | 所有主要分片可用，但不是所有复制分片都可用 |
| red    | 不是所有的主要分片都可用                   |

#### 分片跟副本

为了将数据添加到Elasticsearch，我们需要索引(index)——一个存储关联数据的地方。实际上，索引只是一个用来指向一个或多个分片(shards)的“逻辑命名空间(logical namespace)”.

- 一个分片(shard)是一个最小级别“工作单元(worker unit)”,它只是保存了索引中所有数据的一部分。
- 我们需要知道是分片就是一个Lucene实例，并且它本身就是一个完整的搜索引擎。应用程序不会和它直接通 信。
- 分片可以是主分片(primary shard)或者是复制分片(replica shard)。
- 索引中的每个文档属于一个单独的主分片，所以主分片的数量决定了索引最多能存储多少数据。
- 复制分片只是主分片的一个副本，它可以防止硬件故障导致的数据丢失，同时可以提供读请求，比如搜索或者从别的shard取回文档。
- 当索引创建完成的时候，主分片的数量就固定了，但是复制分片的数量可以随时调整。

#### 故障转移

**将data节点停止**

这里选择将node02停止：

当前集群状态为黄色，表示主节点可用，副本节点不完全可用，过一段时间观察，发现节点列表中看不到node02，副本节点分配到了node01和node03，集群状态恢复到绿色。

将node02恢复： `./node02/1 bin/elasticsearch`

可以看到，node02恢复后，重新加入了集群，并且重新分配了节点信息。

**将master节点停止**

接下来，测试将node01停止，也就是将主节点停止。

从结果中可以看出，集群对master进行了重新选举，选择node03为master。并且集群状态变成黄色。 等待一段时间后，集群状态从黄色变为了绿色：

恢复node01节点：`./node01/1 bin/elasticsearch`

重启之后，发现node01可以正常加入到集群中，集群状态依然为绿色：

**特别说明：**

如果在配置文件中**discovery.zen.minimum_master_nodes**设置的不是N/2+1时，会出现脑裂问题，之前宕机 的主节点恢复后不会加入到集群。

#### 分布式文档

##### 路由

当我们想一个集群保存文档时，文档该存储到哪个节点呢？ 是随机吗？ 是轮询吗？实际上，在ELasticsearch中，会采用计算的方式来确定存储到哪个节点，计算公式如下：

```
shard = hash(routing) % number_1 of_primary_shards
```

其中：

- routing值是一个任意字符串，它默认是_id但也可以自定义。
- 这个routing字符串通过哈希函数生成一个数字，然后除以主切片的数量得到一个余数(remainder)，余数 的范围永远是0到number_of_primary_shards - 1，这个数字就是特定文档所在的分片

这就是为什么创建了主分片后，不能修改的原因

##### 文档的写操作

新建、索引和删除请求都是写（write）操作，它们必须在主分片上成功完成才能复制分片上

![es-cluster-write](https://blog.res.jianchengwang.info/posts/elastic-stack/es-cluster-write.png)

下面我们罗列在主分片和复制分片上成功新建、索引或删除一个文档必要的顺序步骤：

1. 客户端给Node 1 发送新建、索引或删除请求。
2. 节点使用文档的_id 确定文档属于分片0 。它转发请求到Node 3 ，分片0 位于这个节点上。
3. Node 3 在主分片上执行请求，如果成功，它转发请求到相应的位于Node 1 和Node 2 的复制节点上。当所有 的复制节点报告成功， Node 3 报告成功到请求的节点，请求的节点再报告给客户端。

客户端接收到成功响应的时候，文档的修改已经被应用于主分片和所有的复制分片。你的修改生效了。

##### 搜索文档

文档能够从主分片或任意一个复制分片被检索

![es-cluster-search](https://blog.res.jianchengwang.info/posts/elastic-stack/es-cluster-search.png)



下面我们罗列在主分片或复制分片上检索一个文档必要的顺序步骤：

1. 客户端给Node 1 发送get请求。
2. 节点使用文档的_id 确定文档属于分片0 。分片0 对应的复制分片在三个节点上都有。此时，它转发请求到 Node 2 。
3. Node 2 返回文档(document)给Node 1 然后返回给客户端。对于读请求，为了平衡负载，请求节点会为每个请求选择不同的分片——它会循环所有分片副本。可能的情况是，一个被索引的文档已经存在于主分片上却还没来得及同步到复制分片上。这时复制分片会报告文档未找到，主分片会成功返回文档。一旦索引请求成功返回给用户，文档则在主分片和复制分片都是可用的。

##### 全文搜索

对于全文搜索而言，文档可能分散在各个节点上，那么在分布式的情况下，如何搜索文档呢？

搜索，分为2个阶段，

- 搜索（query）
- 取回（fetch）

**query**

![es-cluster-search-query](https://blog.res.jianchengwang.info/posts/elastic-stack/es-cluster-search-query.png)

查询阶段包含以下三步：

1. 客户端发送一个search（搜索） 请求给Node 3 , Node 3 创建了一个长度为from+size 的空优先级队
2. Node 3 转发这个搜索请求到索引中每个分片的原本或副本。每个分片在本地执行这个查询并且结果将结果到 一个大小为from+size 的有序本地优先队列里去。
3. 每个分片返回document的ID和它优先队列里的所有document的排序值给协调节点Node 3 。Node 3 把这些 值合并到自己的优先队列里产生全局排序结果。

**fetch**

![es-cluster-search-fetch](https://blog.res.jianchengwang.info/posts/elastic-stack/es-cluster-search-fetch.png)

分发阶段由以下步骤构成：

1. 协调节点辨别出哪个document需要取回，并且向相关分片发出GET 请求。
2. 每个分片加载document并且根据需要丰富（enrich）它们，然后再将document返回协调节点。
3. 一旦所有的document都被取回，协调节点会将结果返回给客户端。

### java客户端

#### REST客户端

Elasticsearch提供了2种REST客户端，一种是低级客户端，一种是高级客户端。

- Java Low Level REST Client：官方提供的低级客户端。该客户端通过http来连接Elasticsearch集群。用户在使 用该客户端时需要将请求数据手动拼接成Elasticsearch所需JSON格式进行发送，收到响应时同样也需要将返回的JSON数据手动封装成对象。虽然麻烦，不过该客户端兼容所有的Elasticsearch版本。
- Java High Level REST Client：官方提供的高级客户端。该客户端基于低级客户端实现，它提供了很多便捷的 API来解决低级客户端需要手动转换数据格式的问题。

测试数据：

```
POST test/house/_bulk
{"index":{"_index":"test","_type":"house"}}
{"id":"1001","title":"整租 · 南丹大楼 1居室 7500","price":"7500"}
{"index":{"_index":"test","_type":"house"}}
{"id":"1002","title":"陆家嘴板块，精装设计一室一厅，可拎包入住诚意租。","price":"8500"}
{"index":{"_index":"test","_type":"house"}}
{"id":"1003","title":"整租 · 健安坊 1居室 4050","price":"7500"}
{"index":{"_index":"test","_type":"house"}}
{"id":"1004","title":"整租 · 中凯城市之光+视野开阔+景色秀丽+拎包入住","price":"6500"}
{"index":{"_index":"test","_type":"house"}}
{"id":"1005","title":"整租 · 南京西路品质小区 21213三轨交汇 配套齐* 拎包入住","price":"6000"}
{"index":{"_index":"test","_type":"house"}}
{"id":"1006","title":"祥康里 简约风格 *南户型 拎包入住 看房随时","price":"7000"}
```

##### REST低级客户端

创建项目，加入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>Study_ElasticSearch_Code</artifactId>
    <version>1.0-SNAPSHOT</version>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>7</source>
                    <target>7</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-client</artifactId>
            <version>7.11.1</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.11.1</version>
        </dependency>
    </dependencies>
</project>
```

编写测试类

```java

/**
 * 使用低级客户端 访问
 *
 */
public class ESApi {
    private RestClient restClient;
    private static final ObjectMapper MAPPER = new ObjectMapper();

    /**
     * 初始化
     */
    public void init() {
        RestClientBuilder restClientBuilder = RestClient.builder(new HttpHost("122.51.87.176", 9200, "http"));
        this.restClient = restClientBuilder.build();
    }

    /**
     * 查询集群状态
     */
    public void testGetInfo() throws IOException {
        Request request = new Request("GET", "/_cluster/state");
        request.addParameter("pretty", "true");
        Response response = this.restClient.performRequest(request);
        System.out.println(response.getStatusLine());
        System.out.println(EntityUtils.toString(response.getEntity()));
    }

    /**
     * 根据ID查询数据
     * @throws IOException
     */
    public void testGetHouseInfo() throws IOException {
        Request request = new Request("GET", "/test/house/o2zf53cBxfH-S_1kq1K1");
        request.addParameter("pretty", "true");
        Response response = this.restClient.performRequest(request);
        System.out.println(response.getStatusLine());
        System.out.println(EntityUtils.toString(response.getEntity()));
    }

    public void testCreateData() throws IOException {
        Request request = new Request("POST", "/todo/house");
        Map<String, Object> data = new HashMap<>();
        data.put("id", "2001");
        data.put("title", "张江高科");
        data.put("price", "3500");
        // 写成JSON
        request.setJsonEntity(MAPPER.writeValueAsString(data));
        Response response = this.restClient.performRequest(request);
        System.out.println(response.getStatusLine());
        System.out.println(EntityUtils.toString(response.getEntity()));

    }

    // 搜索数据
    public void testSearchData() throws IOException {
        Request request = new Request("POST", "/test/house/_search");
        String searchJson = "{\"query\": {\"match\": {\"title\": \"拎包入住\"}}}";
        request.setJsonEntity(searchJson);
        request.addParameter("pretty","true");
        Response response = this.restClient.performRequest(request);
        System.out.println(response.getStatusLine());
        System.out.println(EntityUtils.toString(response.getEntity()));
    }

    public static void main(String[] args) throws IOException {
        ESApi esApi = new ESApi();
        esApi.init();
//        esApi.testGetInfo();
//        esApi.testGetHouseInfo();
        esApi.testCreateData();
    }
}
```

##### REST高级客户端

引入依赖

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.11.1</version>
</dependency>
```

编写测试类

```java
/**
 * ES高级客户端
 *
 */
public class ESHightApi {
    private RestHighLevelClient client;

    public void init() {
        RestClientBuilder restClientBuilder = RestClient.builder(
                new HttpHost("122.51.87.176", 9200, "http"));
        this.client = new RestHighLevelClient(restClientBuilder);
    }

    public void after() throws Exception {
        this.client.close();
    }

    /**
     * 新增文档，同步操作
     *
     * @throws Exception
     */
    public void testCreate() throws Exception {
        Map<String, Object> data = new HashMap<>();
        data.put("id", "2002");
        data.put("title", "南京西路 拎包入住 一室一厅");
        data.put("price", "4500");
        IndexRequest indexRequest = new IndexRequest("test", "house")
                .source(data);
        IndexResponse indexResponse = this.client.index(indexRequest,
                RequestOptions.DEFAULT);
        System.out.println("id->" + indexResponse.getId());
        System.out.println("index->" + indexResponse.getIndex());
        System.out.println("type->" + indexResponse.getType());
        System.out.println("version->" + indexResponse.getVersion());
        System.out.println("result->" + indexResponse.getResult());
        System.out.println("shardInfo->" + indexResponse.getShardInfo());
    }

    /**
     * 异步创建文档
     * @throws Exception
     */
    public void testCreateAsync() throws Exception {
        Map<String, Object> data = new HashMap<>();
        data.put("id", "2003");
        data.put("title", "南京东路 最新房源 二室一厅");
        data.put("price", "5500");
        IndexRequest indexRequest = new IndexRequest("test", "house")
                .source(data);
        this.client.indexAsync(indexRequest, RequestOptions.DEFAULT, new
                ActionListener<IndexResponse>() {
                    @Override
                    public void onResponse(IndexResponse indexResponse) {
                        System.out.println("id->" + indexResponse.getId());
                        System.out.println("index->" + indexResponse.getIndex());
                        System.out.println("type->" + indexResponse.getType());
                        System.out.println("version->" + indexResponse.getVersion());
                        System.out.println("result->" + indexResponse.getResult());
                        System.out.println("shardInfo->" + indexResponse.getShardInfo());
                    }
                    @Override
                    public void onFailure(Exception e) {
                        System.out.println(e);
                    }
                });
        System.out.println("ok");
        Thread.sleep(20000);
    }

    /**
     * 查询
     * @throws Exception
     */
    public void testQuery() throws Exception {
        GetRequest getRequest = new GetRequest("test", "house",
                "o2zf53cBxfH-S_1kq1K1");
        // 指定返回的字段
        String[] includes = new String[]{"title", "id"};
        String[] excludes = Strings.EMPTY_ARRAY;
        FetchSourceContext fetchSourceContext =
                new FetchSourceContext(true, includes, excludes);
        getRequest.fetchSourceContext(fetchSourceContext);
        GetResponse response = this.client.get(getRequest, RequestOptions.DEFAULT);
        System.out.println("数据 -> " + response.getSource());
    }

    /**
     * 判断是否存在
     *
     * @throws Exception
     */
    public void testExists() throws Exception {
        GetRequest getRequest = new GetRequest("test", "house",
                "o2zf53cBxfH-S_1kq1K1");
// 不返回的字段
        getRequest.fetchSourceContext(new FetchSourceContext(false));
        boolean exists = this.client.exists(getRequest, RequestOptions.DEFAULT);
        System.out.println("exists -> " + exists);
    }
    /**
     * 删除数据
     *
     * @throws Exception
     */
    public void testDelete() throws Exception {
        DeleteRequest deleteRequest = new DeleteRequest("test", "house",
                "o2zf53cBxfH-S_1kq1K1");
        DeleteResponse response = this.client.delete(deleteRequest,
                RequestOptions.DEFAULT);
        System.out.println(response.status());// OK or NOT_FOUND
    }
    /**
     * 更新数据
     *
     * @throws Exception
     */
    public void testUpdate() throws Exception {
        UpdateRequest updateRequest = new UpdateRequest("test", "house",
                "qGzf53cBxfH-S_1kq1K2");
        Map<String, Object> data = new HashMap<>();
        data.put("title", "张江高科2");
        data.put("price", "5000");
        updateRequest.doc(data);
        UpdateResponse response = this.client.update(updateRequest,
                RequestOptions.DEFAULT);
        System.out.println("version -> " + response.getVersion());
    }
    /**
     * 测试搜索
     *
     * @throws Exception
     */
    public void testSearch() throws Exception {
        SearchRequest searchRequest = new SearchRequest("test");
        searchRequest.types("house");
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        sourceBuilder.query(QueryBuilders.matchQuery("title", "拎包入住"));
        sourceBuilder.from(0);
        sourceBuilder.size(5);
        sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));
        searchRequest.source(sourceBuilder);
        SearchResponse search = this.client.search(searchRequest,
                RequestOptions.DEFAULT);
        System.out.println("搜索到 " + search.getHits().totalHits + " 条数据.");
        SearchHits hits = search.getHits();
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
    }

    public static void main(String[] args) throws Exception {
        ESHightApi esHightApi = new ESHightApi();
        esHightApi.init();
        esHightApi.testCreate();
    }
}
```

#### Springboot整合

引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```

这个不多赘述，感兴趣的可以自行了解 [spring-data-elasticsearch](https://spring.io/projects/spring-data-elasticsearch)

## 相关链接

[Elastic Search Document](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)

[Elastic Stack（ELK）从入门到实践视频](https://www.bilibili.com/video/BV1iJ411c7Az?from=search&seid=1620396898343034932)

[Elastic Stack（ELK）从入门到实践笔记](https://gitee.com/moxi159753/LearningNotes/blob/master/ElasticStack/README.md)

