---
title: 关闭 Firefox 更新提醒和自动更新
date: 2018/07/10 21:30:35
categories: 
- 笔记
- 教程
tags: 
- 网页
---

[Mozilla](https://www.mozilla.org/en-US/) 出于安全因素的考虑，自 [Firefox](https://www.mozilla.org/en-US/firefox/all/#product-desktop-release) 63.0a 以后彻底移除了不检查更新的选项， [配置编辑器](https://support.mozilla.org/en-US/kb/about-config-editor-firefox)（about:config）里面的 app.update.enable 参数也一并移除了，想要改变这一设置，可以按以下方法实现。

### 关闭更新提醒

找到安装目录下的 defaults\pref\channel-prefs.js 文件，删除 release/beta ,将该字段置空

```text
pref("app.update.channel", "");
```
即pref的第二个冒号里什么都不填。

<!--more-->

### 彻底禁止自动更新

通过 Firefox 的 [Enterprise Policy](https://support.mozilla.org/en-US/products/firefox-enterprise/policies-customization-enterprise/policies-overview-enterprise) （企业策略）实现

在安装目录下创建一个 `distribution`文件夹，在此文件夹下创建一个`policies.json`文件，内容如下：

```
{
"policies": {
"DisableAppUpdate": true
}
}
```
