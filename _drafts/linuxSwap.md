---
layout: post
title: "Linux SWAP 交换分区配置说明"
date: 2018-07-31 16:45:00 +0800
categories: linux
description: "Linux SWAP 交换分区配置说明"
tag: linux
---

### 一、SWAP 概述

当系统的物理内存不够用的时候，就需要将物理内存中的一部分空间释放出来，以供当前运行的程序使用。那些被释放的空间可能来自一些很长时间没有什么操作的程序，这些被释放的空间被临时保存到Swap空间中，等到那些程序要运行时，再从Swap中恢复保存的数据到内存中。这样，系统总是在物理内存不够时，才进行Swap交换。

### 二、查看和修改swappiness参数

实际上，并不是等所有的物理内存都消耗完毕之后，才去使用swap的空间，什么时候使用是由swappiness 参数值控制。

[root@rhce ~]# cat /proc/sys/vm/swappiness  

0

*swappiness=0的时候表示最大限度使用物理内存，然后才是 swap空间* 

*swappiness＝100的时候表示积极的使用swap分区，并且把内存上的数据及时的搬运到swap空间里面*

现在服务器的内存动不动就是上百G，所以我们可以把这个参数值设置的低一些，让操作系统尽可能的使用物理内存，降低系统对swap的使用，从而提高系统的性能。

**临时性修改：**

[root@rhce ~]# sysctl vm.swappiness=10

vm.swappiness = 10

[root@rhce ~]# cat /proc/sys/vm/swappiness

10

这里我们的修改已经生效，但是如果我们重启了系统，又会变成0.

**永久修改：**

在/etc/sysctl.conf 文件里添加如下参数：

vm.swappiness=10

或者：

[root@rhce ~]# echo 'vm.swappiness=10' >>/etc/sysctl.conf

保存，重启，就生效了。

### 三、使用文件创建交换分区

* 首先用命令free查看系统内 Swap 分区大小。

&nbsp;&nbsp;free -m

* 创建一个 Swap 文件。

&nbsp;&nbsp;mkdir /swapfile

&nbsp;&nbsp;cd /swapfile

&nbsp;&nbsp;sudo dd if=/dev/zero of=swap bs=1024 count=2000000

* 把生成的文件转换成 Swap 文件

&nbsp;&nbsp;sudo mkswap -f swap

* 激活 Swap 文件

&nbsp;&nbsp;sudo swapon swap

* 再次查看 free -m 的结果

PS:

&nbsp;&nbsp;1.如果需要卸载这个 swap 文件，可以进入建立的 swap 文件目录。执行下列命令：

&nbsp;&nbsp;&nbsp;&nbsp;sudo swapoff swap

&nbsp;&nbsp;2.重启后，这个swap会消失，如果需要一直保持这个 swap ，可以把它写入 /etc/fstab 文件。

&nbsp;&nbsp;&nbsp;&nbsp;/swapfile/swap none swap defaults 0 0
