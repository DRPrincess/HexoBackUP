---
title: flutter-学习笔记1-Hello,Flutter!
date: 2018-01-24 18:18:18
tags:
---

# 实现思路
- recyclerview 实现列表
- snapHelpler 帮助实现列表停止在中间位置
- 一屏显示一个完整的Item和左右两个item 的一部分
  - 结合snapHelpler可以将当前Item显示中间，那么只需要控制 item的宽度即可
- 如何判断中间位置与左右的Item
  - 通过滑动距离/理论距离判断
- 滑动过程中的对Item的放大和缩小效果
    - 放大和缩小效果可以使用动画
    - 动画播放时机在滚动过程中 scrollListener 中的 onScroll 方法
- 待解决问题：放大和缩小过程中，为什么改变x,为什么改变x后不能看到左右两边的view了
    - 宽度缩小后，之间空隙变大了
