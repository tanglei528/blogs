---
title: 查看 镜像 的 object 数量
date: 2016-11-01 14:39:50
tags: ceph
categories: 所有文章
---
首先, 根据镜像名称获取详细信息

    rbd info vms/ffac4ae6-9f79-4abf-b076-764e2564d774_disk
        rbd image 'ffac4ae6-9f79-4abf-b076-764e2564d774_disk':
        size 500 GB in 64000 objects 
        order 23 (8192 kB objects)
        block_name_prefix: rbd_data.5496211001d833
        format: 2
        features: layering
        flags: 
        parent: images/a8e78422-90a0-475b-bc50-555d45e44e27@snap
        overlap: 10240 MB

<!--more-->

然后， 根据根据对象前缀查询

    rados -p vms ls|grep rbd_data.5496211001d833|wc -l
