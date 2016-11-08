---
title: ceph 批量 自动 给 osd 磁盘 分区
date: 2016-11-08 11:41:56
tags: ceph
categories: 所有文章
---

如果初始化 ceph 集群，而osd节点很多的情况下，手动分区的工作量就体现出来。
所有写一个脚本，自动完成分区工作
  当前系统磁盘情况：
  说明：/dev/sda 为系统盘, /dev/sdb-/dev/sdi 为osd磁盘，/dev/sdj-/dev/sdk 为journal磁盘,/dev/sdl为mon磁盘
  最终效果: 

<!--more-->

	NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
	sda      8:0    0 558.4G  0 disk 
	├─sda1   8:1    0     2M  0 part 
	├─sda2   8:2    0   977M  0 part /boot/efi
	├─sda3   8:3    0  14.9G  0 part [SWAP]
	└─sda4   8:4    0 542.4G  0 part /
	sdb      8:16   0   3.7T  0 disk 
	└─sdb1   8:17   0   3.7T  0 part /var/lib/ceph/osd/ceph-0
	sdc      8:32   0   3.7T  0 disk 
	└─sdc1   8:33   0   3.7T  0 part /var/lib/ceph/osd/ceph-1
	sdd      8:48   0   3.7T  0 disk 
	└─sdd1   8:49   0   3.7T  0 part /var/lib/ceph/osd/ceph-2
	sde      8:64   0   3.7T  0 disk 
	└─sde1   8:65   0   3.7T  0 part /var/lib/ceph/osd/ceph-3
	sdf      8:80   0   3.7T  0 disk 
	└─sdf1   8:81   0   3.7T  0 part /var/lib/ceph/osd/ceph-4
	sdg      8:96   0   3.7T  0 disk 
	└─sdg1   8:97   0   3.7T  0 part /var/lib/ceph/osd/ceph-5
	sdh      8:112  0   3.7T  0 disk 
	└─sdh1   8:113  0   3.7T  0 part /var/lib/ceph/osd/ceph-6
	sdi      8:128  0   3.7T  0 disk 
	└─sdi1   8:129  0   3.7T  0 part /var/lib/ceph/osd/ceph-7
	sdj      8:144  0 446.6G  0 disk 
	├─sdj1   8:145  0 111.7G  0 part 
	├─sdj2   8:146  0 107.2G  0 part 
	├─sdj3   8:147  0 107.2G  0 part 
	└─sdj4   8:148  0 107.2G  0 part 
	sdk      8:160  0 446.6G  0 disk 
	├─sdk1   8:161  0 111.7G  0 part 
	├─sdk2   8:162  0 107.2G  0 part 
	├─sdk3   8:163  0 107.2G  0 part 
	└─sdk4   8:164  0 107.2G  0 part 
	sdl      8:176  0 446.6G  0 disk 
	└─sdl1   8:177  0 446.6G  0 part /var/lib/ceph/mon

  脚本文件：

	#!/bin/bash

	PATH=/bin:/sbin:/usr/bin:/usr/sbin
	export PATH  

	##osd磁盘分区8块 97表示a
	i=1
	while [ $i -lt 10 ] 
	do
	j=`echo $i|awk '{printf "%c",97+$i}'`
	echo "Start build : /dev/sd${j}"
	/usr/bin/expect<<EOF
	spawn parted /dev/sd$j
	send "mklabel gpt\r"
	expect {
	"*Ignore/Cancel?" { send "Cancel\r"; exp_continue }
	"*Yes/No" { send "Yes\r"; exp_continue }
	}
	send "mkpart primary xfs 0% 100%\r"
	send "quit\r"
	expect eof
	EOF
	sleep 15
	echo "Start format : /dev/sd${j}"
	mkfs.xfs /dev/sd${j}1
	i=$(($i+1))
	done

	##日志分区2块，97+9 表示i

	i=9
	while [ $i -lt 11 ] 
	do
	j=`echo $i|awk '{printf "%c",97+$i}'`
	echo "Start build : /dev/sd${j}"
	/usr/bin/expect<<EOF
	spawn parted /dev/sd$j
	send "mklabel gpt\r"
	expect {
	"*Ignore/Cancel?" { send "Cancel\r"; exp_continue }
	"*Yes/No" { send "Yes\r"; exp_continue }
	}
	send "mkpart primary 0% 25%\r"
	send "mkpart primary 26% 50%\r"
	send "mkpart primary 51% 75%\r"
	send "mkpart primary 76% 100%\r"
	send "quit\r"
	expect eof
	EOF
	printf $j
	i=$(($i+1))
	done

	##mon分区 97+11 等于i
	i=11
	j=`echo $i|awk '{printf "%c",97+$i}'`
	printf $j
	echo "Start build : /dev/sd${j}"
	/usr/bin/expect<<EOF
	spawn parted /dev/sd$j
	send "mklabel gpt\r"
	expect {
	"*Ignore/Cancel?" { send "Cancel\r"; exp_continue }
	"*Yes/No" { send "Yes\r"; exp_continue }
	}
	send "mkpart primary 0% 100%\r"
	send "quit\r"
	expect eof
	EOF
