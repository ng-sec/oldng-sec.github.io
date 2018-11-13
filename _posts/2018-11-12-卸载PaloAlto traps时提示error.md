---
layout: post  
title: 卸载PaloAlto traps时报密码错误
date:  2018/11/12 14:53:01
categories: cases
tags: traps PA
author: ng-sec  
---

* content  
{:toc}

## 问题描述
发现通过终端的控制面板卸载traps程序，提示输入密码时，当我们无论使用默认卸载密码还是自己设定的卸载密码都会提示error.


## 问题解决方案

此时可以通过两种方式解决：

1) 当traps终端可以连接到ESM时，可以通过ESM下发一条密码同步策略，将现有的卸载密码同步到此终端上。
2) 当traps终端无法连接到ESM时， 需要通过手动卸载。
<!-- more -->
### 手动卸载步骤

- 开机进入安全模式：

To remove the traps client if you don't remember the uninstall password,

WIndows7/10进入安全模式可以参考如下链接：

https://support.microsoft.com/en-us/help/17419/windows-7-advanced-startup-options-safe-mode
https://support.microsoft.com/en-us/help/12376/windows-10-start-your-pc-in-safe-mode

 - 备份注册表
 
Please backup registry table and manual remove all the registry key per below process
- 按照如下修改注册表

``` shell

展开注册表： HKEY_LOCAL_MACHINE > SYSTEM > CURRENTCONTROLSET > services

向下滚动找到 Traps drivers and services (CyServer, CyveraK, CyveraService, Cyvrfsfd, cyvrmtgn)

继续展开注册表： HKEY_LOCAL_MACHINE > SYSTEM
点击右键删除 Cyvera文件夹

展开注册表：HKEY_LOCAL_MACHINE > SOFTWARE
点击右键删除  Cyvera文件夹

3.2以上版本: 还需要删除 Palo Alto Networks 文件夹

展开注册表：HKEY_LOCAL_MACHINE > SOFTWARE > Microsoft > Windows > CurrentVersion > Uninstall
点击右键删除  the registry key that represents “Traps”. 

To determine which program that each key presents, click the key, and then view the “DisplayName” data value in the details pane on the right.

展开注册表： HKEY_CLASSES_ROOT > Installer > Products
点击右键删除  the registry key that represents “Traps”.

To determine which program that each key presents, click the key, and then view the “ProductName” data value in the details pane on the right.
######################


- 重启电脑

```
### 使用4.1.4工具清理

Once those registry key removed, reboot unit and run the cleaner tool again to clean up rest of the exe files.

I attached the 4.1.4 traps cleaner to the case, please download it.