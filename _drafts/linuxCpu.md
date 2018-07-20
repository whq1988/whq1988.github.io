---
layout: post
title: "Linux 查看CPU相关信息"
date: 2018-07-19 14:37:00 +0800
categories: linux
description: "Linux 查看CPU相关信息"
tag: linux
---

### 1.查看CPU信息

&nbsp;# 总核数 = 物理CPU个数 * 每颗物理CPU的核数   

&nbsp;# 总逻辑CPU数 = 物理CPU个数 * 每颗物理CPU的核数 * 超线程数

本人使用着一台阿里云4核服务器，在这上面输入命令：

{% highlight shell %}

	#查看CPU型号
	[root@nash ~]# cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c   
	4  Intel(R) Xeon(R) CPU E5-2682 v4 @ 2.50GHz

	#查看物理CPU的个数，1代表只有一个物理CPU
	[root@nash ~]# cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l
	1

	#查看每个物理CPU中core的个数(即核数)
	[root@nash ~]# cat /proc/cpuinfo |grep "cpu cores"|uniq
	cpu cores       : 2

	#查看逻辑CPU个数（也可以在top命令视图中，按数字1，可以显示出有几个逻辑CPU，即几核）
	[root@iZ2zeegjg2v2grvvi3kn5tZ ~]# cat /proc/cpuinfo| grep "processor"| wc -l
	4

{% endhighlight %}

综上，该服务器只有一个物理CPU，该CPU是双核，每个核有2个超线程


### 2.查看CPU的负载

平均负载是指上一分钟同时处于就绪状态的平均进程数。在CPU中可以理解为CPU可以并行处理的任务数量，就是CPU个数X核数。

如果CPU Load等于CPU个数乘以核数，那么就说CPU正好满负载，再多一点，可能就要出问题了，有些任务不能被及时分配处理器，那要保证性能的话，最好要小于CPU个数X核数X0.7。

Load Average是指CPU的Load。它所包含的信息是在一段时间内CPU正在处理及等待CPU处理的进程数之和的统计信息，也就是CPU使用队列的长度的统计信息。

Load Average的值应该小于CPU个数X核数X0.7，Load Average会有3个状态平均值，分别是1分钟、5分钟和15分钟平均Load。
如果1分钟平均出现大于CPU个数X核数的情况，还不需要担心；如果5分钟的平均也是这样，那就要警惕了；15分钟的平均也是这样，就要分析哪里出现问题，防范未然。