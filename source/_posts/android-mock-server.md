---
layout: post
title: 使用json-server模拟后台
date: 2019/10/2
categories: [Android, Notes]
---

笔记。

<!--more-->

[json-server](https://github.com/typicode/json-server)

1. `yarn global add json-server`

1. `json-server --watch db.json --port 80 --host $ip --routes routes.json`

    `db.json`: 数据文件

    `$ip`: 本地IP，好让手机连接。一般是机器的IP。

    `routes.json`: 似乎默认只能单路径，使用这个支持子路径
