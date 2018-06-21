---
layout: post
title: Linux安装python出错
categories: Python
tags: pip
---

申请了一个免费的亚马逊主机 EC2，使用 Ubuntu16.04 LTS，发现里面的 Python 版本是 3.5.2，果断升级到 3.6.5，pip 也升级到 10.0.1，结果发现 pip 用不了，出现如下错误：
```shell
ModuleNotFoundError: No module named 'pip._internal'
```
**解决方法：**

1. sudo easy_install pip
2. which pip3
3. pip3 -V

