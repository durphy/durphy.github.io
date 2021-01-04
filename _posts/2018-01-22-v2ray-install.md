---
title: Linux下安装v2Ray
date: 2018/01/16 19:20:35
categories: 
- VPS
- 教程
tags: 
- 网络穿透
---

## 简介

V2Ray 是 Victoria Raymond开发的 [Project V](https://www.v2ray.com/)下的一个工具。[Project V](https://www.v2fly.org/) 是一个工具集合，它可以帮助你打造专属的基础通信网络。Project V 的核心工具称为V2Ray，其主要负责网络协议和功能的实现，与其它 Project V 通信。V2Ray 可以单独运行，也可以和其它工具配合，以提供简便的操作流程。

 V2Ray 不区分服务器版和客户端版，也就是说在服务器和客户端运行的 V2Ray 是同一个软件，区别只是配置文件的不同。因此 V2Ray 的安装在服务器和客户端上是一样的，但是通常情况下 VPS 使用的是 Linux 而 PC 使用的是 Windows，因此本文默认服务器为 Linux VPS，客户端为 Windows PC。如果你的 PC 使用的是 Linux 操作系统，那么请参考本文的服务器安装；VPS 使用的是 Windows，参考本文的客户端安装；如果你使用的是 MacOS ，请你自行研究怎么安装吧，安装完了跳过本节继续往下看。

------

## 安装前的准备

### 时间校准

对于 V2Ray，它的验证方式包含时间，就算是配置没有任何问题，如果时间不正确，也无法连接 V2Ray 服务器的，服务器会认为你这是不合法的请求。所以系统时间一定要正确，只要保证时间误差在**90 秒**之内就没问题。
对于 VPS(Linux) 可以执行命令 `date -R` 查看时间：

```text
date -R
Sun, 22 Jan 2018 10:10:36 -0500
```
输出结果中的 -0500 代表的是时区为西 5 区，如果转换成东 8 区时间则为 `2018-01-22 23:10:36`。
如果时间不准确，可以使用 `date --set` 修改时间：
```text
sudo date --set="2018-01-22 16:16:23"
Sun 22 Jan 16:16:23 GMT 2018
```
如果你的服务器架构是 OpenVZ，那么使用上面的命令有可能修改不了时间，直接发工单联系 VPS 提供商的客服吧，就说你在 VPS 上运行的服务对时间有要求，要他们提供可行的修改系统时间的方法。

对 VPS 的时间校准之后接着是个人电脑，如何修改电脑上的时间我想不必我多说了。

无论是 VPS 还是个人电脑，时区是什么无所谓，因为 V2Ray 会自动转换时区，但是时间一定要准确。

### 使用 root 账户

为了方便后续脚本的执行安装，在此，我们切换成 root 账户。
执行命令：`su` 之后输入管理员密码（此处的密码是默认隐藏的，不要以为没打上去）。
执行以后命令行形如：
```text
xxx@xxx:~$ su
Password: 
root@xxx:/home/xxx# 
```
如果是以普通用户登录的，也可运行以下命令获取root权限（直到执行exit或者logout后退回普通账户）
```text
sudo -i
```
## 服务器安装

在 Linux 操作系统， V2Ray 的安装有脚本安装、手动安装、编译安装 3 种方式，选择其中一种即可，本指南仅提供使用使用脚本安装的方法，并仅推荐使用脚本安装，该脚本由 V2Ray 官方提供。该脚本仅可以在 Debian 系列或者支持 Systemd 的 Linux 操作系统使用。

**除非你是大佬，或者能够自行处理类似 command not found 的问题，否则请你使用 Debian 8.x 以上或者 Ubuntu 16.04 以上的 Linux 系统。** 本指南默认使用 Debian 10 系统作为示范。

### 安装依赖软件

首先安装脚本的依赖软件，根据你的 Linux 发行版选择以下命令。

Debian/Ubuntu:

```text
apt update
apt install curl
```

CentOS/RedHat :

```text
yum makecache
yum install curl
```

Fedora:

```text
dnf makecache
dnf install curl
```

openSUSE/SUSE:

```text
zypper refresh
zypper install curl
```

### 下载安装脚本

下载主程序[安装脚本](https://github.com/v2fly/fhs-install-v2ray/blob/master/README.zh-Hans-CN.md)：

```text
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh
```

### 执行安装

安装 V2ray 主程序：

```text
bash install-release.sh
```
耐心等待几分钟，看到类似于以下提示就算安装成功了。

```text
...
installed: /var/log/v2ray/
installed: /var/log/v2ray/access.log
installed: /var/log/v2ray/error.log
installed: /etc/systemd/system/v2ray.service
installed: /etc/systemd/system/v2ray@.service
removed: /tmp/tmp.vk9AF2EqKA/
info: V2Ray v4.31.0 is installed.
You may need to execute a command to remove dependent software: apt remove curl unzip
Please execute the command: systemctl enable v2ray; systemctl start v2ray
```
如果安装不成功脚本会有提示语句，这个时候你应当按照提示除错，除错后再重新执行一遍脚本安装 V2Ray。错误提示如果看不懂，使用翻译软件翻译一下就好。

此脚本会自动安装以下文件：

- /etc/v2ray/config.json：配置文件；
- /usr/bin/v2ray/v2ray：V2Ray 程序；
- /usr/bin/v2ray/v2ctl：V2Ray 工具；
- /usr/bin/v2ray/geoip.dat：IP 数据文件
- /usr/bin/v2ray/geosite.dat：域名数据文件


### 运行

首次安装完之后，V2Ray 不会自动启动，需要手动运行命令启动 V2Ray:

```text
systemctl start v2ray
```
设置开机自启动 V2Ray:

```text
systemctl enable v2ray
```
接下来看看 V2ray 是不是真的运行起来了:
```text
systemctl status v2ray
```
看到类似于以下的提示就算启动成功了。
```text
● v2ray.service - V2Ray Service
   Loaded: loaded (/etc/systemd/system/v2ray.service; disabled; vendor preset: enabled)
   Active: active (running) since Sun 2018-01-22 23:17:13 CST; 41min ago
 Main PID: 1984 (v2ray)
    Tasks: 6 (limit: 2359)
   Memory: 6.9M
   CGroup: /system.slice/v2ray.service
           └─1984 /usr/local/bin/v2ray -confdir /usr/local/etc/v2ray/

Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/01_api.json
Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/02_dns.json
Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/03_routing.json
Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/04_policy.json
Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/05_inbounds.json
Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/06_outbounds.json
Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/07_transport.json
Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/08_stats.json
Aug 16 23:17:13 debian v2ray[1984]: v2ctl> Read config:  /usr/local/etc/v2ray/09_reverse.json
Aug 16 23:17:13 debian v2ray[1984]: 2018/01/22 23:17:13 [Warning] v2ray.com/core: V2Ray 4.31.0 start
lines 1-19/19 (END)
```
但是由于此时你还没有为 V2ray 配置，所以咱们还是把它关掉吧：

```text
systemctl stop v2ray
```
可以使用 systemctl start\|stop\|status\|reload\|restart\|force-reload 控制 V2Ray 的运行，关于 V2ray 配置，请参考 [v2ray-examples (opens new window)](https://github.com/v2fly/v2ray-examples)内的示例。对于安装脚本，还有更多用法，在此不多说了，可以执行 `bash install-release.sh -h` 看帮助。

### 升级更新

在 VPS，重新执行一遍安装脚本就可以更新了，在更新过程中会自动重启 V2Ray，配置文件保持不变。

```text
bash install-release.sh
```
V2Ray 的更新策略是快速迭代，每周更新(无意外的情况下)。版本号的格式是 `vX.Y.Z`，如 `v2.44.0`。v 是固定的字母 v，version 的首字母；X、Y、Z 都是数字，X 是大版本号，每年更新一个大版本(现在是 v4.Y.Z，V2Ray 已经走到了第四个年头)，Y 是小版本，每周五更新一个小版本。Z 是区分正式版和测试版，Z 是 0 代表着是正式版，不是 0 说明是测试版。例如，v4.7.0 是正式版，v4.7.1 是测试版，建议只使用正式版，不手动指定的情况下 V2Ray 的安装脚本也只会安装最新的正式版。

有些细心的朋友可能会注意到有时候周五 V2Ray 刚发布了一个新版本，次日或过两日又更新一个正式版。出现这种情况是因为周五发布的正式版出现了影响使用严重的 BUG，需要立马发布一个新版本。这种情况比较烦，但是为了保证兼容性、性能优化等又需要保证版本不要太老旧。所以我比较建议在周四更新，选这么一个日子是因为有重大的 BUG 肯定在前面几天就已经修复了，小问题(恐怕都不知道有)的话不会影响使用；而且版本号与最新版相比迟那么一两个也没什么关系。
###  配置文件

编辑配置文件:
```text
 vim /usr/local/etc/v2ray/config.json
```
测试配置文件
```text
vim v2ray -test -config /usr/local/etc/v2ray/config.json
```
更多配置文件[模板](https://github.com/v2fly/v2ray-examples)参考


------

## 客户端安装

点[【这里】](https://github.com/v2fly/v2ray-core/releases)下载 V2Ray 的 Windows 压缩包，如果是 32 位系统，下载 v2ray-windows-32.zip，如果是 64 位系统，下载 v2ray-windows-64.zip（下载速度慢或无法下载请考虑挂已有的翻墙软件来下载）。下载并且解压之后会有下面这些文件：

- `v2ray.exe` 运行 V2Ray 的程序文件
- `wv2ray.exe` 同 v2ray.exe，区别在于 wv2ray.exe 是后台运行的，不像 v2ray.exe 会有类似于 cmd 控制台的窗口。运行 V2Ray 时从 v2ray.exe 和 wv2ray.exe 中任选一个即可
- `config.json` V2Ray 的配置文件，后面我们对 V2Ray 进行配置其实就是修改这个文件
- `v2ctl.exe` V2Ray 的工具，有多种功能，除特殊用途外，一般由 v2ray.exe 来调用，用户不用太关心
- `geosite.dat` 用于路由的域名文件
- `geoip.dat` 用于路由的 IP 文件
- `其它` 除上面的提到文件外，其他的不是运行 V2Ray 的必要文件。更详细的说明可以看 doc 文件夹下的 readme.md 文件，可以通过记事本或其它的文本编辑器打开查看

实际上双击 v2ray.exe （或 wv2ray.exe） 就可以运行 V2Ray 了，V2Ray 会读取 config.json 中的配置与服务器连接。默认的配置文件包含 V2Ray 官方服务器的配置，也就是说你可以不自己搭建服务器而直接使用 V2Ray 提供的服务器科学上网。在不修改 config.json 的情况下，双击运行 v2ray.exe，可以直接科学上网（V2Ray 官方服务器已下线）。

注：Windows下建议使用图形化客户端：[v2rayN](https://github.com/2dust/v2rayN) （操作友好，方便快捷，**强烈推荐**！）

​       老手也可以考虑使用： [*Clash* *for* Windows](https://github.com/Fndroid/clash_for_windows_pkg/releases)