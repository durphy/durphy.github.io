---
title: Windows常用注册表项整理
date: 2016/09/09 18:45:09
categories: 
- 笔记
tags: 
- win10
---



- 电脑名称：

```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\ComputerName
```

- 注册信息：

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion
```

- OEM 信息：

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\OEMInformation
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WinSAT\WindowsExperienceIndexOemInfo
```

- 软件默认安装路径：

```
HKEY_LOCAL_MACHINE-SOFTWARE-Microsoft-Windows-CurrentVersion-ProgramFilesDir
```

- 网络配置文件：

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Profiles
```

<!--more-->

- 开机启动项：

```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run
```

注：开机启动项也可以通过添加快捷方式到 开始菜单--启动 来实现，具体路径为：

Windows10

```
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\
```

Windows 7

```
C:\Users\UserName\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```

Windows XP

```
C:\Documents and Settings\UserName\Start Menu\Programs\Startup
```

- 任务栏布局：

```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Taskband
```

- WIN+L 锁屏功能 是否开启 （默认是开启）

```
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System
```

"DisableLockWorkstation"=dword:0000000为开启，"DisableLockWorkstation"=dword:00000001为关闭。

- 右键 打开方式 菜单 (方法1仅从列表里删除)

```
HKEY-CURRENT-USER\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\.extension\openWithList
HKEY-CLASSES-ROOT\\APPlications
```

- IE右键菜单

```
HKEY_CURRENT_USER\Software\Microsoft\Internet Explorer\MenuExt
```

- intel 集成显卡右键

```
HKEY_CLASSES_ROOT\Directory\Background\shellex\ContextMenuHandlers
```

删除igfxDTCM和igfxcui 就不会在右键里出现，恢复方法：regsvr32 igfxpph.dll

- Windows图标缓存大小

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer
```

右击新建一个名为 Max Cached Icons 的字符串值，建立完成后双击填入 4096，默认 Windows 的图标缓存大小为 500 KB，可以填入需要的任意大小，建议设置为 4096 即 4MB。

- 关闭Windows10 壁纸压缩

```
HKEY_CURRENT_USER\Control Panel\Desktop
```

 右击新建DWORD（32位）值，命名为JPEGImportQuality ，把数值数据调整为100（可调整范围是60至100, 默认值为85，100即无压缩）