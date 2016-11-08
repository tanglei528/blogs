---
title: shell 脚本 循环 执行 ssh 只执行一次的问题
date: 2016-11-07 14:51:40
tags: linux ssh
categories: 所有文章
---

使用场景

  通过shell脚本，批量拷贝本地脚本到远程服务器，并在远端服务器执行该命令
  这里建立一个ip.txt文件，里面存放所有的远端服务器ip地址
<!--more-->
    ip.txt:
    192.168.0.10
    192.168.0.11

  通过while循环读取ip

    fstab.sh:
    #!/bin/bash
    while read ips;
    do
      echo $ips;
    done < ip.txt

  在上述脚本里加入更多操作:
  把fstab.sh 拷贝到远端服务器
  ssh 到远端服务器，并备份/etc/fstab 文件

    fstab.sh: 
    #!/bin/bash
    while read ip;
    do
      echo 'begin $ip';
      scp fstab.sh $line:~/ && ssh $line cp /etc/fstab /etc/fstab.bk
      echo 'finish $ip';
    done < ip.txt

  但测试发现，脚本只对第一个IP做了操作，然后就直接退出了。

问题分析

  这里主要是while与ssh的原因导致:
  while使用的是重定向机制，ip.txt文件中的信息都读入并重定向给了整个while语句，所以当我们在while循环中再一次调用read语句，就会读取到下一条记录。
  而ssh语句正好会读取输入中的所有东西。为了禁止ssh读所有东西增加一个  /dev/null，将ssh 的输入重定向。


问题解决

  方式一：通过使用 ssh -n 添加 -n 这个参数

    fstab.sh: 
    #!/bin/bash
    while read ip;
    do
      echo 'begin $ip';
      scp fstab.sh $ip:~/ && ssh -n $ip cp /etc/fstab /etc/fstab.bk
      echo 'finish $ip';
    done < ip.txt

  方式二：把while循环修改为for循环

    fstab.sh: 
    #!/bin/bash
    for ip in `cat ip.txt`; do
      echo 'begin $ip';
      opt=`scp fstab.sh $ip:~/ && ssh -n $ip cp /etc/fstab /etc/fstab.bk`;
      echo $opt;
      echo 'finish $ip';
    done
