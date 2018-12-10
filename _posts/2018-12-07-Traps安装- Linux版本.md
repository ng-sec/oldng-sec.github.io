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

# 1. 生成Linux安装包
- 选择Settings>Agent>Installation Package,点击“三条杠”，点击Generate Package
![enter description here](http://800wifi.com/ng-sec/1544418138635.png)
- 选择Package,点击Generate
![enter description here](http://800wifi.com/ng-sec/1544422264169.png)
- 下载生成的安装包
![enter description here](http://800wifi.com/ng-sec/1544424713199.png)

# 2. Linux 下安装traps

## 2.1 导入根证书

- 导入根证书到本地CA证书目录下
``` shell?linenums
# 1）将根证书拷贝到CA证书目录下
sudo cp Root.crt /usr/local/share/ca-certificates/Root.crt 
# 2) 更新证书
sudo update-ca-certificates 
```
- 将证书格式更改为 crt
> 导入证书时，要求格式为crt，如果证书是其他格式，请参考以下进行格式的转换
   
 -  更改pfx格式为crt
 ``` shell?linenums
openssl pkcs12 -in Root.pfx -nokeys -out Root.crt -nodes  
```
 - 更改DER格式为crt
``` shell?linenums
openssl x509 -inform DER -in Root.cer -out Root.crt 
```
## 2.2 解压安装Traps
 
``` shell?linenums
# 1）创建目录 /tmp/traps
sudo mkdir /tmp/traps
# 2）将traps安装包copy到刚刚创建的目录
cp ~/Donwload/Traps_Linux_installer_4.2.1.701.tar.gz  /tmp/traps
# 3) 解压/tmp/traps,
tar xvzf Traps_Linux_installer_4.2.1.701.tar.gz
# 4) 执行安装脚本
sudo ./traps-installer.sh
# 5) 查看traps服务状态
sudo cd/opt/traps/bin
sudo ./cytool runtime query 
```
## 2.2 cytools工具使用
``` shell?linenums
## 1) 启动traps服务
./cytools runtime start all
# 2）关闭traps服务
./cytools runtime stop all
```
## 2.3 卸载Traps

``` shell?linenums
sudo cd /opt/traps/scripts/
sudo ./uninstall.sh
This operation will uninstall Traps, are you sure? [y/N]: y
```


