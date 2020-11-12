---
title: JDK 14 下载安装和环境变量配置
date: 2020/03/20 20:30:06
categories: 
- 笔记
tags: 
- 开发环境
---
### 下载安装

① 从官网下载安装程序：(根据系统版本选择对应安装包)    
https://www.oracle.com/java/technologies/javase-jdk14-downloads.html    
② 双击安装，记住安装路径。例如：    
```C:\Program Files\Java\jdk-14```

### 环境变量配置
安装完成后右击“此电脑”，选择属性->高级系统设置->环境变量，在系统变量里新建    
变量名` JAVA_HOME`    
变量值 `C:\Program Files\Java\jdk-14`    
然后找到系统变量里的Path，点击 编辑/新建    

<!--more-->

键入`%JAVA_HOME%\bin`     
确定。    

### 确认

打开命令提示符，键入

`java -version`

回车，窗口显示java版本信息表示配置成功。