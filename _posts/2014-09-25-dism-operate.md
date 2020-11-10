---
title: DISM常见用法
date: 2014/09/25 09:30:00
categories: 
- 教程
- 笔记
tags: 
- DISM
- win10
---
部署映像服务和管理 (DISM.exe) 是一个命令行工具，可用于处理 Windows(R) 映像或准备 Windows 预安装环境 (Windows PE) 映像。DISM 可用于处理 Windows 映像 (.wim) 文件或虚拟硬盘驱动器（.vhd 或 .vhdx）。

这里结合个人使用实践，简单整理一下DISM的常见用法，适用于windows8.1及以上系统，其中系统部署和备份需在WinPE/WinRE环境下使用。

## 查看映像信息
查看E:\install.wim或.esd文件的详细信息：    
```Dism /Get-imageInfo /imagefile:E:\install.wim```    
```Dism /Get-WimInfo /Wimfile:E:\install.wim```    
```Dism /get-wiminfo /wimfile:E:\install.esd```    
## 系统部署
将E:\install.wim部署到C盘：    
```Dism /Apply-Image /ImageFile:E:\install.wim /Index:1 /ApplyDir:C:\```
<pre>
/Apply-Image：应用映像
/ImageFile：指定映像文件路径
/ApplyDir：指定应用目录
/Index：指定映像索引
</pre>
注1：由于Windows系统原始WIM映像中没有启动文件，需要手动创建：

`bcdboot C:\windows /s C: /l zh-cn`       （BIOS启动）

`bcdboot c:\windows /s o: /f uefi /l zh-cn`  （UEFI启动）

/s o:     指定esp分区所在磁盘，根据实际情况修改

/f uefi    指定启动方式为uefi

/l zh-cn  指定uefi启动界面语言为简体中文

另：如果是把Windows 8/10安装到USB设备中作Windows To Go，也应添加启动：

`bcdboot X:\windows /s X: /l zh-cn /f ALL` （X为 USB 设备的盘符）。

注2：如果映像文件是分卷文件，则使用以下命令部署：    
```Dism /apply-image /imagefile:E:\install.swm /swmfile:E:\install*.swm /index:1 /applydir:C:\```    
注3：如果映像文件是.esd文件，则使用以下命令部署：

```Dism /Apply-Image /ImageFile:install.esd /Index:4 /ApplyDir:C:\```

 （此处/Index:4为示例，官方标准.esd文件通常包含4个及以上的卷，以实际为准。如需以压缩模式部署映像，添加/Compact参数即可）
<!--more-->
## 系统备份
1）初始备份：（例如：将C分区的系统备份到D盘根目录下，备份文件为Winos.wim）
```
Dism /Capture-Image /ImageFile:D:\winos.wim /CaptureDir:C:\ /Name:Drive-C /Description:”Backup” /compress:maximum
```
<pre>
/Capture-Image：捕获映像
/ImageFile：指定映像文件路径               
/CaptureDir：指定捕获目录
/Name：指定映像名称
/Description：添加映像描述，输入任何文字都可以。此项可省略
/Compress：用于指定对初始捕捉操作使用的压缩类型，此项可省略。
    maximum 选项能提供最佳压缩效果，但是捕捉映像所用的时间较长。
    fast选项能提供更快速的映像压缩，但生成的文件比使用 maximum 选项压缩的文件大，这也是在未指定参数时所用的默认压缩类型。
    none 选项不会压缩捕捉的映像。
/Bootable：用于将卷映像标记为可启动映像。此参数仅适用于 Windows PE 映像。在 .wim 文件中，只能将一个卷映像标记为可启动。此项可省略
/ConfigFile：指定映像捕捉和压缩命令排除列表配置文件的位置，需自行准备WimScript.ini文件。此项可省略
/CheckIntegrity：用于在捕捉、卸载、导出和提交操作中使用 .wim 文件时检测和跟踪 .wim 文件的损坏情况。/CheckIntegrity 用于在 DISM 检测到 .wim 文件在应用和装载操作中发生损坏时停止操作。此项可省略
/Verify：用于检查错误和文件重复。此项可省略
</pre>
2）增量备份：（例如：把 C 分区的系统增量备份到D:\winos.wim中）
```
Dism /Append-Image /ImageFile:D:\winos.wim /CaptureDir:C:\ /Name:Drive-C-1 /Description:”Backup append”
```
<pre>
/Append-Image：附加映像
/ImageFile：指定映像文件路径               
/CaptureDir：指定捕获目录
/Name：指定名称
/Description：添加映像描述，输入任何文字都可以，此项可省略。
</pre>
Dism 允许各卷映像之间同名，但为了以后应用方便，各卷映像之间的名称最好不同。Dism 可以指定卷映像索引号或名称来对卷映像进行操作。索引号可能因删除某卷映像而发生改变，但名称是不会变的，如果各卷映像之间同名，用指定名称来对卷映像进行操作时就会出现问题。

请确保有足够的磁盘空间可用于运行 /Append-Image 选项。如果在附加映像期间磁盘空间不足，则可能会损坏 .wim 文件。

## 导出映像
 从 D:\winos.wim中提取增量备份(即卷2) 到D:\ 中，保存为Myext.wim
```
Dism /Export-Image /SourceImageFile:D:\winos.wim /SourceIndex:2 /DestinationImageFile:D:\Myext.wim
```
<pre>
/Export-Image：导出映像
/SourceImageFile：指定映像文件来源路径
/SourceIndex：指定来源映像文件索引
/DestinationImageFile：指定目标映像文件路径
/Compress：用于指定对初始捕捉操作使用的压缩类型，使用 /Export-Image命令时，/Compress还可用 recovery 选项导出一键重置映像。
</pre>
如果 winos.wim 中有五个备份，我们只想保留其中第二与第五个备份，同样可以用 /Export-Image 命令把其中 Index:2 与 Index:5 提取出来，保存为 Myext.wim。命令如下：
```
Dism /Export-Image /SourceImageFile:D:\winos.wim /SourceIndex:2 /DestinationImageFile:D:\Myext.wim
```
```
Dism /Export-Image /SourceImageFile:D:\winos.wim /SourceIndex:5 /DestinationImageFile:D:\Myext.wim
```
这样Myext.wim中就包含两个卷映像，卷映像的索引号会发生改变，但名称不会变。编辑过的wim文件使用export命令导出后可以减小映像文件的体积。

将.wim文件转换成.esd文件
```
Dism /export-image /sourceimagefile:D:\winos.wim /sourceindex:1 /destinationimagefile:D:\install.esd /compress:recovery /checkintegrity
```
将.esd文件转换成.wim文件（对于加密的.esd文件，需先解密方可转换）
```
Dism /export-image /sourceimagefile:D:\install.esd /sourceindex:1 /destinationimagefile:D:\winos.wim /Compress:Max /checkintegrity
```
将.wim文件合并到.esd文件
```
Dism /export-image /sourceimagefile: D:\winos.wim /sourceindex:1 /destinationimagefile:install.esd /compress:recovery /checkintegrity
```
将.esd文件合并到.esd文件
```
Dism /export-image /sourceimagefile: D:\new.esd /sourceindex:1 /destinationimagefile:install.esd /compress:recovery /checkintegrity
```
将一个包含核心版和专业版的install.wim转换为install.esd，假设有两个索引
```
Dism /export-image /sourceimagefile:install.wim /sourceindex:1 /destinationimagefile:install.esd /compress:recovery /checkintegrity
```
```
Dism /export-image /sourceimagefile:install.wim /sourceindex:2 /destinationimagefile:install.esd /compress:recovery /checkintegrity
```
附：将swm文件合并成wim：
```
Dism /Export-Image /Sourceimagefile:E:\install.swm /SWMFile:E:\install*.swm /SourceIndex:1 /DestinationImagefile:E:\install.wim / CheckIntergrity
```
## 映像编辑
1）将备份文件winos.wim装载到E:\test
```
Dism /Mount-Image /ImageFile:D:\winos.wim /index:1 /MountDir:E:\test
```
<pre>
/Mount-Image：装载映像
/ImageFile：指定映像文件
/index：指定映像索引
/MountDir：指定装载目录
</pre>
2）卸载映像
```
Dism /Unmount-Image /MountDir:E:\test /commit
```
```
Dism /Unmount-Image /MountDir:E:\test /discard
```
<pre>
/Unmount-Image：卸载映像
/commit：保存更改
/discard：忽略更改
</pre>
3）重新装载已装载但不能访问的映像并使其可供处理    
```
Dism /Remount-Image /MountDir:E:\test
```
<pre>/Remount-Image：重新装载映像</pre>
4）将WIM文件拆分为多个SWM文件
```
Dism /Split-Image /ImageFile:D:\winos.wim /SWMFile:D:\data.wim /FileSize:650 /CheckIntegrity
```
<pre>
/Split-Image：拆分映像
/ImageFile：指定映像文件
/SWMFile：指定目标目录
/FileSize：指定每个已创建文件的最大大小 (MB)
</pre>
5）删除卷映像 （例如：删除D:\winos.wim中的增量备份）    
```
Dism /Delete-Image /ImageFile:D:\winos.wim /Index:2
```
此命令仅删除卷映像名称与描述，而不会删除卷映像数据。可用于防止误应用该卷映像。

## 驱动管理
1.添加驱动（例如：给挂载在e:\winpe目录的映像添加驱动，G:\SurfaceDriver为驱动文件的路径）

a）添加单个驱动
```
Dism /image:e:\winpe /add-driver /driver:"G:\SurfaceDriver\wlan\mrvlpcie8897.inf" /ForceUnsigned
```
<pre>
/image：指定脱机映像
/Add-Driver：向脱机 Windows 映像中添加第三方驱动程序包
/Driver：指定INF 文件
/ForceUnsigned：强制添加添加未签名的驱动程序
</pre>
b）批量添加
```
Dism /image:e:\winpe /add-driver /driver:"G:\SurfaceDriver" /recurse /ForceUnsigned
```
<pre>
/Driver：指定驱动程序所在文件夹
/Recurse：查询/driver指向文件夹下的子文件夹以确定要添加的驱动程序
</pre>
2.删除驱动
```
Dism /image:e:\winpe /Remove-Driver /driver:oem1.inf
```
```
Dism /image: e:\winpe /Remove-Driver /driver:oem1.inf /driver:oem2.inf
```
<pre>
/Remove-Driver：从脱机映像中删除第三方驱动程序
/driver：指定驱动程序名称（可多次使用 /Driver 选项在命令行上指定多个驱动程序）
</pre>
注：无法删除默认驱动程序。

3、导出驱动
```
Dism /Online /Export-Driver /Destination:D:\destpath
```
```
Dism /Image:C\test\offline /Export-Driver /Destination:D:\destpath
```
<pre>
/Export-Driver：将所有第三方驱动程序包从 Windows 映像导出到目标路径
/Online：联机系统
/Image：指定脱机映像
/Destination：指定目标路径
</pre>
## 清理系统
1.清理当前系统：
```
Dism /online /Cleanup-Image /StartComponentCleanup /ResetBase
```

（执行此操作前需关闭Windows搜索服务和微软拼音输入法， 清理完毕后再重新开启）

2.清理离线系统：
```
Dism /Image:C:\ /Cleanup-Image /StartComponentCleanup /ResetBase
```

3.对于启动故障（如更新失败等情况），也可用Cleanup-Image命令解决
```
Dism /image:C:\ /cleanup-image /revertpendingactions
```
注：/revertpendingactions选项将从以前的服务操作中还原所有挂起的操作，因为以前的操作可能就是启动故障的原因所在。应仅在未启动的 Windows 映像上的系统恢复方案中使用。

## 修复系统
A、检查映像是否可修复

1.扫描映像来检查损坏

`Dism /Online /Cleanup-Image /ScanHealth`

这条命令将扫描全部系统文件并和官方系统文件对比，扫描计算机中的不一致情况。

2.检查映像以查看是否有检测到损坏

`Dism /Online /Cleanup-Image /CheckHealth`

这条命令必须在前一条命令执行完以后，发现系统文件有损坏时使用。当使用 /CheckHealth 参数时，DISM 工具将报告映像是状态良好、可以修复、还是不可修复。如果映像不可修复，必须放弃该映像，并重新开始。如果映像可以修复，可以使用 /RestoreHealth 参数来修复映像。

B、修复映像

若扫描出计算机中的不一致情况，检测到系统的损坏，并报告映像是状态良好、可以修复，就可以用 /RestoreHealth 参数进行修复。

1、若要修复映像,在管理员命令提示符下键入：（需联网）

`Dism /Online /Cleanup-image /RestoreHealth`

这条命令是把那些不同的系统文件还原成官方系统源文件，其他的第三方软件和用户设置完全保留，比重装好多了。而且在扫描与修复的时候系统未损坏部分正常运行，电脑可以照常工作。

2、若要使用自己的一些来源，不使用 Windows 更新来修复一个联机映像，则键入：
```
Dism /Online /Cleanup-Image /RestoreHealth /Source:c:\test\mount\windows /LimitAccess
```
## Windows 版本服务
1.显示指定映像的版本

```Dism /Image:C:\test\offline /Get-CurrentEdition```

`Dism /Online /Get-CurrentEdition`

2.显示可以将映像更改为的 Windows 版本的列表

`Dism /Image:C:\test\offline /Get-TargetEditions`

`Dism /Online /Get-TargetEditions`

3.将脱机 Windows 映像更改到更高版本

`Dism /Image:C:\test\offline /Set-Edition: <edition name>`

4.指定脱机 Windows 映像的产品密匙

```
Dism /Image: C:\test\offline /Set-ProductKey:XGVPP-NMH47-7TTHJ-W3FW7-8HV2C
```

更多用法，请见[微软官方文档](https://msdn.microsoft.com/zh-cn/library/windows/hardware/dn898549(v=vs.85).aspx)