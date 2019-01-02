---
layout: post  
title: 2019-01-02-ELK MetricBeat配置监控主机性能
date:  2019/01/02 14:24:45
categories: 安全认证 
tags: elk   大数据
author: ng-sec  
---

* content  
{:toc}

## 1.MetricBeat介绍
Beats是开源数据发送者，可以将其作为代理安装在您的服务器上，以将不同类型的运营数据发送到Elasticsearch。Beats可以直接发送数据到Elasticsearch或通过Logstash发送到Elasticsearch，可以使用它来分析和转换数据。
Metricbeat是一个服务器监视代理程序，它定期从服务器上运行的操作系统和服务中收集指标。配合kibana的dashboard可以直观的监控系统状态。

Metricbeat是一个轻量级的托运工，你可以在服务器上安装它，定期从操作系统和服务器上运行的服务收集指标，Metricbeat取得它收集的指标和统计数据，并将它们发送到你指定的输出，例如Elasticsearch或Logstash。
Metricbeat通过从运行在服务器上的系统和服务收集指标来帮助你监视服务器。
有关支持的服务的完整列表，请参阅Modules。Metricbeat可以将收集到的指标直接插入Elasticsearch或将其发送到Logstash、Redis或Kafka。

## 2. 需求说明
需要通过MetricBeat来监控CPU/内存/IO的使用情况,同时可以查看Ubuntu上的进程使用情况.
## 2.MetricBeat下载
> 由于我的计算机是Ubuntu18.04,所以选择

[MetricBeat下载链接](https://www.elastic.co/downloads/past-releases)
![enter description here](http://800wifi.com/ng-sec/1546410876155.png)

## 3.MetricBeat安装

## 4.MetricBeat配置