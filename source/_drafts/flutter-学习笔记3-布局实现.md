---
title: flutter-学习笔记3-布局实现
date: 2018-01-22 18:18:18
tags:
---

# 前言

Android 中的几大布局方式
- LinearLayout
- RelativeLayout
- FrameLayout
- TableLayout
- and so on

Flutter 中统统都没有，那么 Flutter 中有什么呢？
- Row
- Column
- Stack
- Container
- Scaffold
- and so on

首先，清醒点，这不是演习！这不是演习！两者之间没有替代对应关系的，例如想实现 RelativeLayout 的效果，使用Column、Row和Stack的组合才能实现。那么是为什么呢？

因为 Flutter 布局的核心就是 Widget。在Flutter中，几乎所有东西都是一个widget。图像是 widget 文本是widget，布局模型是widget，页面也是 widget。



# Row
