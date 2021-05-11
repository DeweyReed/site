---
layout: post
title: 关于计时机器在Google Play渠道引入订阅制云备份的决定
date: 2020/9/7
categories: Android
---

计时机器在4.6.0开始，在Google Play下载的应用将包含需要订阅后可使用的云备份功能。在酷安下载的应用将不包含此功能。在此进行一些记录和说明。

<!--more-->

## Google Play版本和酷安版本区别

Google Play版本独占：云备份、未来可能加入到订阅计划中所有服务

酷安版本独占：开发日志、打赏

## 从酷安版本迁移到Google Play版本

1. 打开应用，打开侧边栏，选择“备份与还原”，选择“导出应用数据”，根据步骤将应用数据导出为一个文件。
1. 在Google Play搜索“计时机器”并安装。
1. 打开Google Play下载的应用，打开侧边栏，选择“备份与还原”，选择“导入应用数据”，将第一步导出的文件导入。
1. 把新应用的系统白名单设置一下，以免应用锁屏后被系统强制关闭。
1. 如果要使用云备份，且手机不是一直处于魔法上网的环境，请在订阅后将自动打开的自动云备份关闭，然后在需要时手动备份。

## 原因

订阅使用了Google Play的In-app Billing，云备份使用了Firebase，而上述两个服务在中国大陆由于众所周知的原因无法使用。虽然这个问题有一个常见的解决方案：在大陆使用支付宝或微信支付，和Firebase的同类替代（像之前用过的LeanCloud），但由于**国内个人支付资格难以获得、域名备案困难繁琐、应用曝光渠道狭窄、应用市场分散混乱等**原因，故暂时放弃在中国大陆提供该服务的想法。