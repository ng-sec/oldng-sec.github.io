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

## 1.没有定义ElasticSearch Mapping(而是使用默认)
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
那么在这里"payload"并不是一个"date",所以此时由于ElasticSearch错误的将payload字段标记为"date"类型,会出现一条错误消息.

2.Combinatorial Explosions(组合爆炸)

组合爆炸是计算问题，可能导致某些聚合的bucket生成呈指数增长，并可能导致不受控制的内存使用。在某些聚合中，实际环境中没有足够的记忆来支持它们的组合爆炸。

Elasticsearch“terms”字段根据您的数据构建Bucket，但无法预测将提前创建多少Bucket。对于由多个子聚合组成的父聚合，这可能会有问题。组合每个子聚合中的唯一值可能会导致创建的桶数量大幅增加。

我们来看一个例子。

假设您有一个代表运动队的数据集。如果你想特别关注那支球队的前10名球员和5名替补球员，那么聚合将如下所示：

``` json?linenums
{
“aggs” : {
“players”: {
“terms”: {
“field”: “players”,
“size”: 10
}
}
},
“aggs”: {
“other”: {
“terms” : {
“field”: “players”,
“size”: 5
}
}
}
}
```
聚合将返回前10名球员的列表以及每位顶级玩家的前5名替补球员的列表 - 这样总共将返回50个值。创建的查询将能够以最小的努力消耗大量内存。

针对每个级别,Term聚合可以通过Bucket显示为树状。因此，玩家聚合中每个顶级球员的桶将构成第1级，而另1个聚合中的每个替补球员的Bucket将构成第2级。因此，一个团队将生产n²桶。想象一下，如果您拥有5亿个文档的数据集会发生什么。

集合模式用于帮助控制子聚合的执行方式。聚合的默认收集模式称为深度优先，首先需要构建整个树，然后修剪边缘。虽然深度优先是大多数聚合的适当收集模式，但它不适用于上面的播放器聚合示例。因此，Elasticsearch允许您将特定聚合中的收集模式更改为更合适的方式。

异常（例如上面的示例）应该使用广度优先收集模式，该模式一次构建和修剪树一级以控制组合爆炸。此收集模式极大地帮助减少消耗的内存量并保持节点稳定：

``` json?linenums
{
“aggs” : {
“players”: {
“terms”: {
“field”: “players”,
“size”: 10,
“collect_mode”: “breadth_first”
}
}
},
“aggs”: {
“other”: {
“terms” : {
“field”: “players”,
“size”: 5
}
}
}
}
```
## 3.Production FLags生产标志
默认情况下，Elasticsearch启动的第一个集群称为elasticsearch。如果您不确定如何更改配置，最好坚持默认配置。但是，最好重命名生产群集以防止不需要的节点加入群集。

以下是您可能希望重命名群集和节点的示例：

``` java?linenums
cluster.name: elasticsearch_production

node.name: elasticsearch_node_001
```


恢复设置会影响群集重启时节点的恢复方式。每当发生恢复时，Elasticsearch都允许属于同一群集的节点自动加入该群集。虽然群集中的某些节点在恢复后快速启动，但是其他节点有时可能需要更长的时间（例如，由于节点在不同时间接收到重启命令）。

启动时间的这种差异可能导致数据中的不一致，这些不一致意味着在群集中的节点之间均匀分布。特别是，当涉及大量数据时，重启后重新平衡节点可能需要相当长的时间 - 从几个小时到几天 - 并且大部分超出预算：

``` java?linenums
gateway.recover_after_nodes: 10
```
此外，配置每个群集中的节点数以及在Elasticsearch中启动它们所需的时间量非常重要：
``` java?linenums
gateway.expected_nodes: 10
gateway.recover_after_time: 5m
```
通过适当的配置，可以在几秒钟内完成需要数小时或数天才能完成的恢复。此外，minimum_master_nodes对于群集稳定性非常重要。它们有助于防止分裂大脑，这是在一个群集中存在两个主节点，并可能导致数据丢失。

此设置的建议值为（N / 2）+ 1 - 其中N是符合主节点的节点数。有了这个，如果你有10个可以保存数据并成为主数据的常规节点，那么该值将是6。如果您有三个专用主节点和1,000个数据节点，则该值为两个（仅计算潜在主节点）：