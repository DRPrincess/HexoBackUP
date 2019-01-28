---
title: Mac-未能打开SANGFOR SSL Virtual 网卡错误
date: 2018-11-03 18:18:18
tags:
---

#

登录VPN后有如下报错，一般情况下是安装了vpn软件占用了隧道tun0接口，按照如下操作:



查看是哪个tun占用

kextstat | grep tun  

反加载该tun

sudo kextunload -b ****.tun

然后重新拨号登录即可

如果发现没有加载的tun

去系统偏好设置里面的安全性与隐私 查看是否被拦截

如果上面这些都没问题 可能是sip导致内核无法扩展 采用下面这种办法

  1 使用csrutil ststus 查看sip是否为enable

  2 重启系统

  3 在弹出Apple Logo后 按住Command+R进入Recoverary模式

  4 点击实用工具>终端

  5 输入csrutil disable

  6 重启系统
