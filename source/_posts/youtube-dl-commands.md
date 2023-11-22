---
layout: post
title: 个人常用的视频下载及处理命令整理
date: 2023/11/23
categories: Notes
---

RT

<!--more-->

## 下载视频的音频

```Bash
yt-dlp --extract-audio --audio-format mp3 
```

<https://askubuntu.com/a/178991>

## 下载视频列表的音频

```Bash
yt-dlp --extract-audio --audio-format mp3 -o "%(title)s.%(ext)s" 
```

<https://askubuntu.com/a/564571>

## 转换视频文件夹为音频文件夹

```cmd
for %i in (*.mp4) do ffmpeg -i "%i" "%~ni.mp3"
```

<https://stackoverflow.com/a/56244203/5507158>
