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