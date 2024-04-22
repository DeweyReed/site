---
layout: post
title: sed 使用笔记
date: 2024/4/21
categories: Notes
---

`sed`

<!--more-->

<https://regex-vis.com/>

- 替换`oldfile`文件中的**每行第一个**`find`为`replace`，保存内容到`newfile`中

  `sed 's/find/replace/' <oldfile >newfile`

- 替换`oldfile`文件中的**每一个**`<space>find`为`replace`，保存内容到`newfile`中

  `sed 's/ find/replace/g' <oldfile >newfile`

- 替换`filename`文件中的每一个`find`为`replace`

  `sed -i 's/find/replace/g' filename`

- 替换`filename`文件中的包含`Replace`文字的每一行中的`the`为`THE`

  `sed -i '/Replace/s/the/THE/g' filename`

- 删除`filename`文件中的包含`Replace`文字的每一行

  `sed -i '/Replace/d' filename`

- 不使用`-i`则打印内容

- 运行多条命令

  - `sed -i '...;...' filename`
  - `sed -i -e '...' -e '...' filename`

- `\/`转义`/`或使用`s#find#replace#g`, `s|find|replace|g`, `.`
- 打印出包含`usr`的每一行: `sed '/user/p'`
- 删除`redacted`: `sed "s/redacted//g filename`
- 删除空行: `sed "/^$/d" filename`
