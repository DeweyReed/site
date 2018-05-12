---
layout: post
title: 使用Splinter模拟网页操作
date: 2018/4/1
categories: Automation
tags: 不再维护
---

之前需要用代码来代替一些繁琐重复的网页操作，自然想到用Python自动化这些工作。

然后找到了[这篇文章](https://hackernoon.com/mastering-python-web-scraping-get-your-data-back-e9a5cc653d88)。它用了Splinter，一个Selenium的包装。尝试之后发现意外地简单。

内容大同小异，说明几个坑。

<!--more-->

我用的是Py3.6和Chrome.

## Chrome驱动

需要提前在[这里](https://sites.google.com/a/chromium.org/chromedriver/)下载Chrome的驱动，再把下载到的`chromedriver.exe`放到Python代码的目录下。就可以使用了。

```python
executable_path = {'executable_path': 'chromedriver.exe'}
browser = Browser("chrome", **executable_path)
browser.driver.set_window_size(1200, 800)
```

## 技巧

- `browser.driver.execute_script("window.open('');")` 新建Tab
- `browser.windows.current = browser.windows[0]` 切换Tab
- `Pandas`读数据：
    ```Python
    def get_dict(name):
        my_df = pd.read_csv(name)
        titles = my_df['Title'].tolist()
        links = my_df['Link'].tolist()
        return {titles[i]: links[i] for i in range(0, len(titles))}
    ```

用Splinter和Selenium完成一些简单的签到、爬数据还算方便，不需要理会用爬虫时Cookie、异步、认证等问题。

开头提到的文章中讲的太详细以致于我已无法可说了。
