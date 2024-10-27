---
layout: post
title: "虚拟机centos循环登录"
date: 2024-10-27 18:10:00 +0800
categories: [Software]
tags: [Experience]  
---
# 原因

环境变量路径冲突。

# 解决办法

1. 登录页面按ctrl+alt+f2，进入命令行模式
2. vi ~/.bashrc打开环境变量设置
3. 删除最后一行export PATH="/XXX"，保存退出
4. ctrl+alt+f1退出命令行模式，回到图形界面，即可正常登录