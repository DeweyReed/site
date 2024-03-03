---
layout: post
title: 个人使用的Android Studio配置
date: 2024/2/12
categories: Android
---

Android Studio的每个新版本都或多或少有点问题，因此每次都重新安装并手动配置来避免配置继承的问题。

<!--more-->

- `Appearance & Behavior`
  - `Appearance`
    - `Theme`: `Darcula`
    - `UI Options` - Disable `Smooth scrolling`
  - `System Settings`
    - `HTTP Proxy`
    - `Memory Settings`
- `Keymap`
  - `ShowProjectStructureSettings`: Empty
  - `Type alias`: Empty
  - `Show vcs Log`: Alt + 3
  - `Logcat`: Alt + 4
  - `Build`: Alt + 5
  - `Show Local Changes`: Alt + 2
  - `Maximize/Restore Active Dialog`: Ctrl + Alt + Shift + M
  - `Compare with Clipboard`: Ctrl + Alt + Shift + C
- `Editor`
  - `General` - `On Save` - Enable `Ensure every saved file ends with a line break`
    - `Auto Import`: Enable 2 `Add unambiguous imports on the fly`
    - `Appearance`
      - Disable `Caret blinking`
      - Disable `Show line numbers`
      - Enable `Show method separators`
      - Disable `Render documentation comments`
    - Clean `Gutter Icons` manually
    - `Smart Keys`: Enable `Use "CamelHumps" Words`
  - `Font` - `Font`: `Consolas`; `Size`: `22.0`
  - `Code Style` - `Kotlin`: Set from `Kotlin style guide`
  - `Live Template`: Import from settings zip
  - `Design Tools`: `Split`, `Split`, `Code`, `Code`
  - `Inlay Hints`: Disable `Code Vision`
  - Disable `Reader Mode`
- `Plugins`
  - `ADB Idea`
  - `Archive Browser`
  - [Optinal] `GLSL Support`
  - `JsonToKotlinClass`
  - `Kotlin Multiplatform Mobile`
  - `Nyan Progress Bar`
  - `String Manipulation`
  - [Optinal] `Wavefront OBJ`
- `Version Control` - `Commit` - `Before Commit`: Enable all
- `Build, Execution, Deployment` - `Deployment`: Enable all
- `Tools`
  - `External Tools`
    - `Avocado`
      - `Program`: `...\Yarn\bin\avocado.cmd`
      - `Argument`: `-i $FileName$`
      - `Working directory`: `$FileDir$`
  - `Emulator`: Disable `Launch in the Running Devices tool window`
