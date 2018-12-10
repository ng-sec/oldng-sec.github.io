---
layout: post  
title: Traps安装- Linux版本
date:  2018/12/07 14:21:32  
categories: 安全产品
tags: 安装手册   
author: ng-sec  
---

* content  
{:toc}

> 本文主要介绍如何在LInux安装Traps客户端

# 1. 生成Linux
- 选择Settings>Agent>Installation Package,点击“三条杠”，点击Generate Package
![enter description here](http://800wifi.com/ng-sec/1544418138635.png)
- 选择Package,点击Generate
![enter description here](http://800wifi.com/ng-sec/1544422264169.png)
# 2. Linux 下安装traps

## 2.1 导入根证书

- 导入根证书到本地CA证书目录下
``` shell?linenums
# 1）将根证书拷贝到CA证书目录下
sudo cp Root.crt /usr/local/share/ca-certificates/Root.crt 
```
- 如果需要


# 2）更新根证书
## 2.2 解压安装Traps
 
``` shell?linenums
# 1）创建目录 /tmp/traps
sudo mkdir /tmp/traps
# 2）将traps安装包copy到刚刚创建的目录
cp  /tmp/traps
# 3) 解压/tmp/traps,
tar xvzf Traps_Linux_installer_4.2.1.701.tar.gz
# 4) 执行安装脚本
sudo ./traps-installer.sh
# 5) 查看traps服务状态
sudo cd/opt/traps/bin
sudo ./cytool runtime query 
```


## 2.2 查看


## 2.3 卸载Traps

``` shell?linenums
# 
cd /opt/traps/scripts/
```


