---
title: ImageX常见用法
date: 2015/08/09 21:50:29
categories: 
- 教程
- 笔记
tags: 
- Win10
---
&emsp;&emsp;很久不折腾了，想抽空做个PE，动起手来生疏得很，这里复习一下imageX，没什么技术含量，只为省去看官方文档的麻烦！网上down来的一个PE，现在要修改BOOT.wim，在E盘新建test文件夹作为 工作目录。 

### 查看映像信息
`imagex /info D:\BOOT.wim`    
### 只读挂载  
`imagex /mount D:\BOOT.wim 1 E:\test`    
### 可写挂载
`imagex /mountrw  D:\BOOT.wim 1 E:\test`    
以可写方式装载后，可以直接增删或编辑文件，达到定制目的。  
### 卸载映像（不保存更改） 
`imagex /unmount E:\test `    
### 保存并卸载 
`imagex /unmount /commit E:\test`        
### 捕获映像
`imagex /capture E:\test D:\new.wim “description” /compress maximum`    
/compress参数指定压缩类型，maximum |fast | none分别表示最大压缩快速压缩和不压缩， 默认为快速。此处的test目录是本地目录，不能从当前挂载目录直接捕获。 
若将E盘捕获为一个wim映像，就这样写：    
`imagex /capture E: D:\new.wim "Drive E"`    
引号部分为可选描述，不想写可以不写。       
如果是制作PE 映像，需加/boot参数，将卷映像标记为可启动映像，并且只能将.wim 文件 中的一个卷映像标记为可启动映像。     
<!--more-->
### 导出映像
`imagex /export d:\BOOT.wim 1 E:\sample.wim /compress maximum`     
这条命令比较精髓，可以做的事情很多，分开来说：     
**给wim减肥**     
修改wim后，ImageX 将存储额外的资源文件，若直接保存，映像容量将增加，而导出映像 将删除不必要的资源文件，从而减小wim体积。如    
`imagex /export d:\BOOT.wim 1 E:\sample.wim /compress maximum`     
 sample.wim的体积会比6中的new.wim小。     
**删除多映像WIM中不需要的映像**     
imageX有专门的删除命令，但要在PE里进行，可以用export命令殊途同归。     
如 A.wim中有5个映像，我们只需要1号映像和2号映像，可以这样：     
`imagex /export E:\A.WIM 1 E:\X.WIM`     
`imagex /export E:\B.WIM 2 E:\X.WIM`     
**映像整合**       
A.WIM中有5个映像，B.WIM中有4个映像，现在把B.WIM中的4个映像导出到A.WIM中      
`imagex /export E:\B.WIM 1 E:\A.WIM`     
`imagex /export E:\B.WIM 2 E:\A.WIM`     
`imagex /export E:\B.WIM 3 E:\A.WIM`     
`imagex /export E:\B.WIM 4 E:\A.WIM`     
这样，A.WIM就增加了4个来自B.WIM的映像。A.WIM中共有9个映像，B.WIM中仍有4 个映像。需要注意的是A.WIM的映像名称不能与A.WIM已有映像名称相同。若名称相同， 需先对B.WIM中的映像重命名：     
`imagex /info E:\B.WIM 1 "NAME" "description"`     
第一个双引号内是名称，第二双引号是描述。同一WIM文件中映像名称不要一样，描述可 以一样，也可以不一样。     
**调整映像顺序**     
Y.WIM中分别有3个映像，a是1号映像，b是2号，c是3号。调整后X.wim中c是1号， b是2号，a是3号     
`imagex /export e:\Y.wim 3 e:\X.wim`     
`imagex /export e:\Y.wim 2 e:\X.wim`     
`imagex /export e:\Y.wim 1 e:\X.wim`     
 X.wim与Y.wim其实是一样的内容，只不过内部映像排列顺序不一样而已。 

### 应用映像
`imagex /apply D:\X.wim 1 H:\`     
这个常用于PE环境下装系统，或者在windows系统下灌装VHD。     
`imagex /apply D:\X.wim 1 D:\New_Directory /verify`     
将映像应用到文件夹，相当于一个解压的过程。     
&emsp;&emsp;注：ImageX 工具已在 Windows(R) 8 中取消，且已被用于映像管理的 DISM 所代替。该工 具还取代了之前部署工具包中包括的程序包管理器 (Pkgmgr.exe)、PEimg 和 Intlcfg。这些工 具中包括的功能现在被合并到一个工具 (DISM.exe) 中，并且已添加了新功能，从而改善了 脱机服务的体验。（此工具依然随WADK套件一同分发）    

**ImageX更多说明**:    
https://technet.microsoft.com/zh-cn/library/dd799302(v=ws.10).aspx  