---
layout: post
title: "Linux CPU&内存 占用情况查看及优化"
date: 2018-06-22 15:12:00 +0800
categories: linux
description: "Linux CPU&内存 占用情况查看及优化"
tag: linux
---

## 查看CPU和内存占用情况

### 1.vmstat命令(默认系统自带)

vmstat(VirtualMeomoryStatistics,虚拟内存统计)是Linux中监控内存的常用工具,可对操作系统的虚拟内存、进程、CPU等的整体情况进行监视。该命令可以显示关于系统各种资源之间相关性能的简要信息。

<div align="center"><img src="{{ "/images/linuxCpuAndMemory/1.png" | prepend: site.baseurl }}" width="600" /></div>

|---------+-----------------+-----------------------------------|
| 字 段   | 类 别           | 说明                               |
|:-------:|:---------------:|:---------------------------------:|
| r       | procs(进程)     | run:在运行队列中等待的进程数        |
| b       | procs(进程)     | block:在等待io的进程数             |
|---------+-----------------+-----------------------------------|
| swpd    | memory(内存)    | 已经使用的交换内存(kb)	            |
| free    | memory(内存)    | 空闲的物理内存(kb)                  |
| buff    | memory(内存)    | 用做缓冲区的内存数(kb)              |
| cache   | memory(内存)    | 用做高速缓存的内存数(kb)            |
|---------+-----------------+-----------------------------------|
| si      | swap(交换页面)	| 从磁盘交换到内存的交换页数量(kb/秒)	|
| so      | swap(交换页面)   | 从内存交换到磁盘的交换页数量(kb/秒)	|
|---------+------------+----+-----------------------------------|
| bi      | IO(块设备)      | 发送到块设备的块数(块/秒)            |
| bo      | IO(块设备)      | 从块设备中接收的块数(块/秒)          |
|---------+------------+----+-----------------------------------|
| in      | system(系统)    | 每秒的中断数，包括时钟中断           |
| cs      | system(系统)    | 每秒的上下文切换的次数              |
|---------+------------+----+-----------------------------------|
| us      | CPU(处理器)     | 用户进程使用的CPU时间(%)            |
| sy      | CPU(处理器)     | 系统进程使用的CPU时间(%)            |
| id      | CPU(处理器)     | CPU空闲时间(%)                     |
| wa      | CPU(处理器)     | 等待IO所消耗的CPU时间(%)            |
| st      | CPU(处理器)     | 从虚拟设备中获得的时间(%)           |
|---------+------------+----+-----------------------------------|

* procs标准：当r超过了CPU数目，就会出现CPU瓶颈了(使用'cat /proc/cpuinfo\|grep processor\|wc -l'查看CPU数量)
* swap标准：si,so长期不为0,说明内存不足，需要加内存
* io标准：bi+bo超过1000，而且wa值较高，说明磁盘IO有问题，应提高磁盘读写性能
* CPU标准：us 长期超过50%，用户进程消耗cpu,需要考虑优化程序或算法   
&nbsp;&nbsp;&nbsp;&nbsp;sy 长期超过50%，内核消耗的cpu资源很多  
&nbsp;&nbsp;&nbsp;&nbsp;us+sy 长期超过80%，说明可能cpu资源不足  
&nbsp;&nbsp;&nbsp;&nbsp;id(空闲率) 长期低于10%，表示CPU的资源已经非常紧张  
&nbsp;&nbsp;&nbsp;&nbsp;wa 参考值20%，如果超过20%,说明io等待严重

**Tips: vmstat中CPU的度量是百分比的。当us＋sy的值接近100的时候，表示CPU正在接近满负荷工作。
但要注意的是，CPU 满负荷工作并不能说明什么，Linux总是试图要CPU尽可能的繁忙，使得任务的吞吐量最大化。
唯一能够确定CPU瓶颈的还是r（运行队列）的值。**


### 2.top命令(默认系统自带)

top命令是Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况，类似于Windows的任务管理器

* 运行 top 命令后，CPU 使用状态会以全屏的方式显示，并且会处在对话的模式；
* 键入"M"命令根据内存的占用情况降序排列，看看内存主要由哪些进程占用
* 键入"P"命令根据CPU的占用情况降序排列，看看CPU主要由哪些进程占用
* 可以单独查看某个用户或某个进程的情况，分别使用 top -u <进程所有者> 和 top -p <进程号>
* 退出 top 的命令为 q （在 top 运行中敲 q 键一次）。


### 3.使用free -m

<div align="center"><img src="{{ "/images/linuxCpuAndMemory/2.png" | prepend: site.baseurl }}" width="500" /></div>


<br>
## 优化CPU和内存

### 1.释放缓存

在上图中可以看到，cache 占用了大量内存，此时可以释放缓存，常用的释放缓存的命令如下:

* **To free pagecache:**  echo 1 > /proc/sys/vm/drop_caches
* **To free dentries and inodes:**  echo 2 > /proc/sys/vm/drop_caches
* **To free pagecache, dentries and inodes:**  echo 3 > /proc/sys/vm/drop_caches

释放完成后，使用echo 0 > /proc/sys/vm/drop_caches 恢复系统默认设置。