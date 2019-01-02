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
Metricbeat是一个服务器监视代理程序，它定期从服务器上运行的操作系统和服务中收集指标。配合kibana的dashboard可以直观的监控系统状态。你可以在服务器上安装它，定期从操作系统和服务器上运行的服务收集指标，Metricbeat取得它收集的指标和统计数据，并将它们发送到你指定的输出，例如Elasticsearch或Logstash。
Metricbeat通过从运行在服务器上的系统和服务收集指标来帮助你监视服务器。
有关支持的服务的完整列表，请参阅Modules。Metricbeat可以将收集到的指标直接插入Elasticsearch或将其发送到Logstash、Redis或Kafka。
[参考链接](https://book.gitlore.com/operatesystem/ELKStack%E4%B8%AD%E6%96%87%E6%8C%87%E5%8D%97/beats/metric.html)
## 2. 需求说明
需要通过MetricBeat来监控CPU/内存/IO的使用情况,同时可以查看Ubuntu上的进程使用情况.
## 3.MetricBeat下载
> 由于我的计算机是Ubuntu18.04,所以选择

[MetricBeat下载链接](https://www.elastic.co/downloads/past-releases)
![enter description here](http://800wifi.com/ng-sec/1546410876155.png)

## 4.MetricBeat安装

``` shell?linenums
#sudo dpkg -i metricbeat-6.4.2-amd64.deb
[sudo] *** 的密码： 
正在选中未选择的软件包 metricbeat。
(正在读取数据库 ... 系统当前共安装有 278465 个文件和目录。)
正准备解包 metricbeat-6.4.2-amd64.deb  ...
正在解包 metricbeat (6.4.2) ...
正在设置 metricbeat (6.4.2) ...
正在处理用于 systemd (237-3ubuntu10.6) 的触发器 ...
正在处理用于 ureadahead (0.100.0-20) 的触发器 ...
ureadahead will be reprofiled on next reboot

```

## 5.MetricBeat配置和启动

- 1) 编辑配置脚本 /etc/metricbeat/metricbeat

``` ruby?linenums
## 设置默认导入dashboards
setup.dashboards.enabled: true
## 如果存在模板,选择不覆盖
setup.template.overwrite: false
## 设置kibana的地址
setup.kibana:
  host: "192.168.80.161:5601"
## 设置es的地址和协议,我这里有两台:分别是192.168.80.161和192.168.80.162
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["192.168.80.161:9200","192.168.80.162:9200"]
  protocol: "http"

```

- 2) 编辑/etc/metricbeat/modules.d/system.yml,如下图所示
![enter description here](http://800wifi.com/ng-sec/1546412998374.png)

- 3) 启动metricbeat服务

``` ruby?linenums
#sudo systemctl start metricbeat 
#sudo systemctl status metricbeat 
● metricbeat.service - Metricbeat is a lightweight shipper for metrics.
   Loaded: loaded (/lib/systemd/system/metricbeat.service; disabled; vendor preset: enabled)
   Active: active (running) since Wed 2019-01-02 15:17:22 CST; 4s ago
     Docs: https://www.elastic.co/products/beats/metricbeat
 Main PID: 26602 (metricbeat)
    Tasks: 13 (limit: 4915)
   CGroup: /system.slice/metricbeat.service
           └─26602 /usr/share/metricbeat/bin/metricbeat -c /etc/metricbeat/metricbeat.yml -path.home /usr/share/metricbe

1月 02 15:17:22 robin-ubuntu systemd[1]: Started Metricbeat is a lightweight shipper for metrics..

```

## 5.Kibana上查看metricbeat状态

> 初次配置metricbeat后,kibana会导入默认模板.
- 选择Dashboard,打开[Metricbeat System] Overview

 ![enter description here](http://800wifi.com/ng-sec/1546413710565.png)
 
- 查看仪表盘

![enter description here](http://800wifi.com/ng-sec/1546415406562.png)

![enter description here](http://800wifi.com/ng-sec/1546415435558.png)
