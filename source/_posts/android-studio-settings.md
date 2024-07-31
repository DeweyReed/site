---
layout: post
title: 个人使用的Android Studio配置
date: 2024/2/12
categories: Android
---

Android Studio的每个新版本都或多或少有点问题，因此每次都重新安装并手动配置来避免配置继承的问题。

<!--more-->

- Export `Live Template`
- `Appearance & Behavior`
  - `Appearance`
    - `Theme`: `Darcula`/`Dark`
    - `UI Options` - Disable `Smooth scrolling`
  - `System Settings`
    - `HTTP Proxy`
    - `Memory Settings`
- `Keymap`(Delete old keys)
  - `ShowProjectStructureSettings`: Empty
  - `Type alias`: Empty
  - `Show vcs Log`: Alt + 3
  - `Show Logcat`: Alt + 4
  - `Build`(Tool Window): Alt + 5
  - `Show Local Changes`: Alt + 2
  - `Maximize/Restore Active Dialog`: Ctrl + Alt + Shift + M
  - `Compare with Clipboard`: Ctrl + Alt + Shift + C
- `Editor`
  - `General` - `On Save` - Enable `Ensure every saved file ends with a line break`
    - `Auto Import`: Enable 2 `Add unambiguous imports on the fly`, `Optimize imports on the fly`
    - `Appearance`
      - Disable `Caret blinking`
      - Disable `Show line numbers`
      - Enable `Show method separators`
      - Disable `Render documentation comments` and `Reader Mode`
    - `Console` - `Default Encoding`: `UTF-8`
    - `Smart Keys`: Enable `Use "CamelHumps" Words`
  - `Font` - `Font`: `Consolas`; `Size`: `22.0`
  - `Color Scheme` - `Console Font` - `Size`: `16`; `Line height`: `0.8`
  - `Code Style`: `Scheme: IDE`
    - `Groovy`
      - `Code Generation`
        - Disable `Line comment at first column`
        - Enable `Add a space at line comment start` and `Enforce on reformat`
    - `Kotlin`
      - Set from `Kotlin style guide`
      - `Tabs and Indents` - `Continuation Indent`: 4
      - `Wrapping and Braces`: Disable `New line after multiline entry`
      - `Code Generation`
        - Disable `Line comment at first column`
        - Enable `Add a space at line comment start` and `Enforce on reformat`
  - `Design Tools`: `Split`, `Split`, `Split`, `Code`, `Code`
  - `Inlay Hints`: Disable `Code Vision`
- `Plugins`
  - `ADB Idea`
  - `Archive Browser`
  - `JsonToKotlinClass` + Configure manually
  - `Nyan Progress Bar`
  - `String Manipulation`
  - [Optinal] `Kotlin Multiplatform Mobile`
  - [Optinal] `GLSL Support`
  - [Optinal] `Wavefront OBJ`
- `Version Control` - `Commit`
  - Disable `Use non-modal commit interface`
  - `Before Commit`: Enable all
- `Build, Execution, Deployment`
  - `Deployment`: Enable all
- `Tools`
  - `External Tools`
    - `Avocado`
      - `Program`: `...\Yarn\bin\avocado.cmd`(`Show Hidden Files and Directories`)
      - `Argument`: `-i $FileName$`
      - `Working directory`: `$FileDir$`
  - `Emulator`: Disable `Launch in the Running Devices tool window`
- Open a project
- Clean `Gutter Icons` and reorder tabs manually
- Reorder window layouts and `Store Current Layout as Default`
- `Editor`
  - `Live Template`: Disable compose related; Import from settings zip
- `Ctrl` + `Shift` + `Alt` + `L`
  - Enable all options
- Disable `Show Project Gradient` in the toolbar
