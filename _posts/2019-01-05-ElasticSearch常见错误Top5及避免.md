---
layout: post  
title: ElasticSearch常见错误Top5及避免
date:  2019/01/05 01:21:32  
categories: 安全运营
tags: ELK
author: ng-sec  
---

* content  
{:toc}

Elasticsearch是开源软件索引，并将信息存储在基于Lucene搜索引擎的NoSQL数据库中 - 它恰好也是当今最流行的索引引擎之一。Elasticsearch也是ELK Stack的一部分。

该软件被不断发展的初创公司（如DataDog）以及已建立的企业（如Guardian，StackOverflow和GitHub）使用，以使其基础架构，产品和服务更具可扩展性。

尽管Elasticsearch越来越受欢迎，但在使用该软件时，用户往往会犯下一些常见且严重的错误。让我们仔细看看五个错误以及如何避免错误。

## 1.未定义ElasticSearch Mapping
你可能启动Elasticsearch，创建索引并使用JSON文档提供它，而不包含模式。然后，Elasticsearch将遍历JSON文档的每个索引字段，估计其字段，并创建相应的映射。虽然这看起来很理想，但Elasticsearch映射并不总是准确的。例如，如果选择了错误的字段类型，则会出现索引错误。

要解决此问题，您应该定义映射，尤其是在生产线环境中。索引一些文档是最佳做法，让Elasticsearch猜测该字段，然后使用GET / index_name / doc_type / _mapping获取它创建的映射。然后，你可以自己动手处理并进行适当的更改，而不会留下任何可能性。

例如，如果您将第一个文档编入索引，如下所示：

``` json?linenums
{
“action”: “Some action”,
“payload”: “2016-01-20”
}
```
ElasticSearch 将会标记"payload"字段为"date"
假设你的文档如下所示:
``` json?linenums
{
“action”: “Some action 1”,
“payload”: “USER_LOCKED”
}
```
那么在这里"payload"并不是一个"date",所以此时由于错误的将payload字段标记为"date"类型,会出现一条错误消息,