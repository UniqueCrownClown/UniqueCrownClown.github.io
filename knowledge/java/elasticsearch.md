---
title: elasticsearch
date: 2022-08-21 11:23:51
categories: java #文章分类
tags: [elasticsearch] #文章标签，可以一次添加多个标签
---

elasticsearch...

<!-- more -->

## elasticsearch

开源的数据库，是由 Elastic 公司开发的，基于 Lucene 基础架构，使用 Java 语言开发的一个支持多集群，高亮，快速搜索的数据库，由于其天生支持多集群分布式，逐渐超越 sloar 从而在企业中得到广泛的使用

概念 index 索引(database 数据库),type(table 表),一个 type 包含多个 document，每个 document 代表一个具体的数据实例,document(row 行),每个 document 具有多个 field，每个 field 代表一个属性或字段，类似于关系型数据库中的列。field(column 列),每个 field 都有一个类型，例如文本、数字、日期等。QueryDSL 在 es 中是使用的 json 格式的查询语句(mysql 的 sql 语句)

```
@Field(type = FieldType.Keyword)
@Field(analyzer = "ik_max_word",type = FieldType.Text)
@Field(type =FieldType.Nested)
```

### 倒排索引

- 将每一个文档的数据利用分词算法根据语义拆分，得到一个个词条
- 创建表，每行数据包括词条、词条所在文档 id、位置等信息
- 因为词条唯一性，可以给词条创建正向索引

- 正向索引是最传统的，根据 id 索引的方式。但根据词条查询时，必须先逐条获取每个文档，然后判断文档中是否包含所需要的词条，是根据文档找词条的过程。
- 而倒排索引则相反，是先找到用户要搜索的词条，根据词条得到保护词条的文档的 id，然后根据 id 获取文档。是根据词条找文档的过程

### index templates(模板)

是一种定义索引创建方式的机制。模板可以定义索引的名称、分片和副本数量、映射、设置等信息。当新的索引被创建时，Elasticsearch 将会自动应用匹配的模板。

通过使用索引模板，您可以确保新创建的索引具有一致的设置和属性。这样可以简化索引管理，并确保所有的索引都遵循同样的规则和标准。

### pipeline

Pipeline 是一种用于处理文档的机制，可以通过一系列的处理步骤对文档进行转换、过滤、聚合等操作。Pipeline 可以在索引文档时进行处理，也可以在搜索时进行处理。

索引文档时，通过指定 pipeline 参数来应用特定的 Pipeline

```bash
# 定义pipeline
PUT _ingest/pipeline/my_pipeline
{
  "description": "My custom pipeline",
  "processors": [
    {
      "set": {
        "field": "my_field",
        "value": "my_value"
      }
    }
  ]
}
# 应用pipeline
PUT my_index/_doc/1?pipeline=my_pipeline
{
  "some_data": "example data"
}
```

### 索引映射

定义了索引中的字段及其属性的过程。它定义了每个字段的数据类型、分析器和其他属性，以及如何将文档中的值存储和索引。索引映射对于搜索和聚合操作非常重要，因为它们确定了如何解释和处理文档中的数据。

Elasticsearch 的索引状态

- 打开状态（open）：表示索引处于正常的可读写状态，可以进行搜索、索引和删除等操作。

- 关闭状态（closed）：表示索引已经被关闭，不能进行任何操作。关闭索引可以用于临时停止对索引的访问或者进行维护操作。

- 读取只状态（read-only）：表示索引只能进行读取操作，不能进行写入操作。这种状态可以用于保护数据或者限制对数据的修改。

- 写入只状态（write-only）：表示索引只能进行写入操作，不能进行读取操作。这种状态比较少见，一般用于特殊的场景，比如日志采集。

- 异常状态（unavailable）：表示索引处于不可用状态，可能是由于磁盘故障、网络问题等原因导致的。在这种状态下，索引不能进行任何操作，需要进行修复或者恢复操作。

(Elasticsearch 权威指南)[https://www.elastic.co/guide/cn/elasticsearch/guide/current/foreword_id.html]

KQL(Kibana Query Language)

## spring-data-elasticsearch

## record

1. 数据流（Data Stream）

- 概念：数据流是一种逻辑上的概念，它不是一个物理存储单元，而是一个指向一系列索引的逻辑命名空间。这些索引通常遵循特定的生命周期策略，如基于时间滚动创建新索引。每个数据流关联一个模板，该模板定义了如何创建数据流下的索引，包括索引的设置、映射等。

- 自动管理：数据流内的索引管理是自动化的，包括索引的创建、 rollover（滚动）和删除。当数据写入数据流时，Elasticsearch 会根据配置的条件（如文档数量、大小或时间间隔）自动创建新的索引并将其添加到数据流中。

- 生命周期管理：数据流支持生命周期管理，这意味着你可以设置策略来自动处理旧索引，例如定期将不再需要的索引归档或删除，以优化存储和维护成本。

2. 索引的动态映射(Dynamic Mapping)

(动态映射)[https://www.elastic.co/guide/en/elasticsearch/reference/8.9/dynamic-field-mapping.html]

3. RestHighLevelClient

- elasticsearch-rest-high-level-client

## 资料

(ELKstack 中文指南)[https://elkguide.elasticsearch.cn/logstash/source-code-analysis/pipeline.html]

(pipeline定义的处理器用法)[https://blog.csdn.net/chunqiqian1285/article/details/100977188]
