---
title: 解决Windows10下U盘显示奇怪图标的方法
date: 2019/11/15 21:35:19
categories: 
- 博客
- 教程
tags: 
- Win10
---
自Windows10发布以来，历次的大版本更新几乎都是带病上场，问题不断，这次也不例外，这不，插上U盘，图标变成了机柜画风。

![153744kzz.png](https://i.loli.net/2020/11/10/jGCioFHczv1E3Lb.png)



本着不麻烦微软的精神，先清除图标缓存，再重新安装USB驱动，还特意反复插拔了几次U盘。

不料一顿操作之后，u盘图标竟然又变成机柜了，设备描述变为"Device"，网上搜了一下，发现不是个例，近日很多人遇到类似情况。

<!--more-->

### 解决方法：

1、拔掉所有移动设备（移动硬盘U盘/SD卡等），右键此电脑-属性-高级系统设置，选择硬件-设备安装设置，选“是”，确认安装方式为自动

2、删除 dmrc.idx 和 f4e323bf-0f31-42b6-9ad6-5d5382d3fa83文件夹

```shell
del /f c:\ProgramData\Microsoft\Windows\DeviceMetadataCache\dmrc.idx
RD /S /Q "C:\ProgramData\Microsoft\Windows\DeviceMetadataCache\dmrccache\en-US\f4e323bf-0f31-42b6-9ad6-5d5382d3fa83"
```

3、打开设备管理器，勾选查看-显示隐藏设备，选中并右键点击**卸载所有显示为灰色**的**磁盘驱动器**、**存储卷**和**通用串行总线控制器**

4、重启电脑。
---插上U盘，图标恢复正常

*注：如果仍然无法正常显示，在插入U盘的情况下打开控制面板-设备和打印机，找到Device，右键点击删除（如果删不掉就多点几次），当图标消失时拔掉U盘，然后再插上*