---
layout: post
title: "Linux的tmpfs文件系统"
date: 2018-08-21 13:42:00 +0800
categories: Linux
description: "Linux的tmpfs文件系统"
tag: Linux
---

### tmpfs介绍

前几天发现服务器的内存(ram)和swap使用率非常低，于是就想这么多的资源，不用岂不浪费了？google了一下，认识了tmpfs，总的来说tmpfs是一种虚拟内存文件系统,正如这个定义它最大的特点就是它的存储空间在VM里面，这里提一下VM(virtual memory)，VM是由linux内核里面的vm子系统管理的东东，现在大多数操作系统都采用了虚拟内存管理机制?更详细的说明请参考<<UnderStanding The Linux Virtual Memory Manager>)。

linux下面VM的大小由RM(Real Memory)和swap组成,RM的大小就是物理内存的大小，而Swap的大小是由你自己决定的。Swap是通过硬盘虚拟出来的内存空间，因此它的读写速度相对RM(Real Memory）要慢许多，我们为什么需要Swap呢？当一个进程申请一定数量的内存时，如内核的vm子系统发现没有足够的RM时，就会把RM里面的一些不常用的数据交换到Swap里面，如果需要重新使用这些数据再把它们从Swap交换到RM里面。 如果你有足够大的物理内存，根本不需要划分Swap分区。

通过上面的说明，你该知道tmpfs使用的存储空间VM是什么了吧？ 前面说过VM由RM+Swap两部分组成，因此tmpfs最大的存储空间可达（The size of RM + The size ofSwap）。但是对于tmpfs本身而言，它并不知道自己使用的空间是RM还是Swap，这一切都是由内核的vm子系统管理的。

### tmpfs的用途

由于tmpfs使用的是VM，因此它比硬盘的速度肯定要快，因此我们可以利用这个优点使用它来提升机器的性能。（本博客中有个示例，就是配置nginx中‘fastcgi_pass  unix:/dev/shm/php-cgi.sock;’，这里的/dev/shm是linux默认自带的tmpfs）

### 如何分配tmpfs

\#mount -t tmpfs -o size=20m tmpfs /mnt/tmp

上面这条命令分配了上限为20m的VM到/mnt/tmp目录下，用df命令查看一下，确实/mnt/tmp挂载点显示的大小是20m，但是tmpfs一个优点就是它的大小是随着实际存储的容量而变化的，换句话说，假如/mnt/tmp目录下什么也没有，tmpfs并不占用VM。上面的参数20m只是告诉内核这个挂载点最大可用的VM为20m，如果不加上这个参数，tmpfs默认的大小是RM的一半，假如你的物理内存是128M，那么tmpfs默认的大小就是64M

### 配置开机自动挂载

当然有，由于它的数据是在VM里面，因此断电或者你卸载它之后，数据就会立即丢失，这也许就是它叫tmpfs的原故。不过这其实不能说是缺点。可以在/etc/fstab(*系统开机时会主动读取/etc/fstab这个文件中的内容，根据文件里面的配置挂载磁盘*)里面加上下面的语句：

tmpfs /mnt/tmp tmpfs size=20m 0 0

重启电脑之后就一切OK了。