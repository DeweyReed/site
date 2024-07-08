---
layout: post
title: 个人常用的视频下载及处理命令整理
date: 2023/11/23
categories: Notes
---

RT

<!--more-->

## 下载最佳视频+音频

```Bash
yt-dlp -S ext
```

<https://github.com/yt-dlp/yt-dlp/issues/3398>

## 合成视频加音频

```Bash
yt-dlp -F
yt-dlp -f 641+140 
```

<https://www.reddit.com/r/youtubedl/comments/qfsuxu/ytdlp_download_and_merge_multiple_format/>

## 裁剪音频

```Bash
ffmpeg -ss 1:00 -to 2:00 -i input.mp3 output.mp3
```

<https://stackoverflow.com/questions/46508055/using-ffmpeg-to-cut-audio-from-to-position>

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
