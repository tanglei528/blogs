---
title: 解决too many PGs per OSD的问题
date: 2016-11-01 14:14:32
tags: ceph
categories: all
---
临时方法：
`ceph tell 'mon.*' injectargs "--mon_pg_warn_max_per_osd 1000"`
使用tell命令修改的配置只是临时的，只要服务一重启，配置就会回到解放前，从ceph.conf 中读取配置。所以长久之计是把这个配置加到Ceph Mon节点的配置文件里，然后重启Mon服务。
<!--more-->

永久方法：
`vim ceph.conf:
[global]
...
mon_pg_warn_max_per_osd = 1000  ##具体的值
...`
重启mon服务
