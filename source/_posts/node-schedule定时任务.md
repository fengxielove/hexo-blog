---
title: node-schedule定时任务
excerpt: 定时任务推送在很多业务场景中会经常使用，那么前端人如何自己用node来实现定时任务推送呢？
date: 2021-05-14 23:57:40
tags: [node.js, 定时任务]
categories:
- [node.js]
post_sticky: 100
index_img: https://rmt.dogedoge.com/fetch/fluid/storage/fluid-write/cover.jpg?w=480&fmt=webp  
---
# 前言
定时任务推送在很多业务场景中会经常使用，那么前端人如何自己用node来实现定时任务推送呢？下面我将分享一下我在写毕业设计时遇到的定时任务推送，希望可以对大家有所帮助。

# 我的使用场景
在下发作业之后，获取作业的截止时间，在截止时间之前两小时，推送催缴作业提醒给未提交作业的同学。
