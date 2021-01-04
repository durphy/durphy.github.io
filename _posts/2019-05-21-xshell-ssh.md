---
title: 使用SSH工具xshell登录VPS
date: 2019/05/21 13:22:38
categories: 
- VPS
- 笔记
tags: 
- 开发环境
- ssh
---

# 使用xshell生成密钥
1. 打开[xshell](https://www.netsarang.com/zh/xshell-download/)-工具-新建用户密钥生成向导
<div align="center"><img src="https://i.loli.net/2021/01/04/8zPWhR9OdYSFXoi.jpg"/></div>
2.密匙参数设定  

<div align="center"><img src="https://i.loli.net/2021/01/04/joB4ZDpM5G86AhF.jpg"/></div>
3.生成密钥对  
<div align="center"><img src="https://i.loli.net/2021/01/04/TWc4LgFbPay6mrl.jpg"/></div>
4.密钥信息设置  
<div align="center"><img src="https://i.loli.net/2021/01/04/SbpiH4Pu1LrMJaN.jpg"/></div>
5.保存密钥到本地（公钥，*myid_rsa_2048.pub即公钥文件*），**公钥需要在服务器上注册**  

<div align="center"><img src="https://i.loli.net/2021/01/04/8wBaoYeT6sqvWry.jpg"/></div>  
<!--more-->
6.导出密钥（私钥），私钥用于在客户机登录时验证    

<div align="center"><img src="https://i.loli.net/2021/01/04/p2EWT7KgnSoswqI.jpg"/></div>

7.输入第4步设定的密钥密码完成导出操作  （*myid_rsa_2048即私钥文件*）

![2019-05-21_130702.jpg](https://i.loli.net/2021/01/04/rO9ywuAf6ZdISxG.jpg)

新建密钥的操作完成后得到如下两个文件，要连接到远程VPS，还需要先在服务器上注册公钥。
![2019-05-21_130807.jpg](https://i.loli.net/2021/01/04/EH3LOoYij4AzWwu.jpg)
公钥和私钥匹配方可连接成功，可以想象成古代的虎符O(∩_∩)O

# 远程服务器配置
### 谷歌云([Google Cloud Platform](https://cloud.google.com/)) 
登录[控制台](https://console.cloud.google.com/)，点左上角的导航菜单--计算-Compute Engine--元数据--SSH密钥--修改--添加一项  

<div align="center"><img src="https://i.loli.net/2021/01/04/jVlkBhZ9q8f1rPt.png"/></div>
粘贴上方第5步中的内容。并在末尾添加 空格+用户名 ，保存即可。  
<div align="center"><img src="https://i.loli.net/2021/01/04/AlkiTmdrFoxGItj.jpg"/></div>

### [阿里云 ](https://www.aliyun.com/)

方法一：登录 [控制台](https://home.console.aliyun.com/)，点左上角的菜单--云服务器ECS--网络与安全--密钥对--[创建密钥对](https://help.aliyun.com/document_detail/51793.html?spm=a2c4g.11186623.6.926.64664636cyMwgF)--按照提示操作即可。可以自动创建新密钥对，也可以导入已有的密钥，然后[绑定](https://help.aliyun.com/document_detail/51796.html?spm=5176.2020520101keyPair.0.dexternal.16bc4df5oeisdw)到目标实例。

方法二：先用密码登录到服务器，然后把上方第5步中的内容写入 **authorized_keys**
```shell 
vim /root/.ssh/authorized_keys
```

保存退出，然后重启 sshd服务
```shell  
systemctl restart sshd
```
# 使用xshell远程连接VPS

![2021-01-04_174003.jpg](https://i.loli.net/2021/01/04/BDrAaVOqCY2EoXc.jpg)