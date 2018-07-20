---
layout: post
title: "使用Jmeter进行性能测试"
date: 2018-07-16 16:36:00 +0800
categories: jmeter
description: "使用Jmeter进行性能测试"
tag: jmeter
---

> 测试思路：在window本机上创建测试计划形成 .jmx，然后拿到linux系统去跑测试计划进行打压，最后把打压生成的 .jtl文件导入windows的jmeter中查看结果。 （windows和linux上安装的jmeter版本尽量要一致，这里是jmeter3.3版本）

### 一、在windows本机创建测试计划

（1）jmeter打开后会自动生成一个空的test plan，用户可以基于该test plan建立自己的test plan
，一个性能测试的负载必须有一个线程组完成，而一个测试计划必须有至少一个线程组。添加线程组操作如下：

在测试计划处右键单击：添加→Threads（Users）→线程组

<div align="center"><img src="{{ "/images/jmeterUse/1.png" | prepend: site.baseurl }}" width="600" /></div>

每个测试计划都必须包含至少一个线程组，当然，也可以包含多个，多个线程组的运行在jmeter中采用的是并行的方式，即：同时被初始化且同时执行其下的sampler

<div align="center"><img src="{{ "/images/jmeterUse/2.png" | prepend: site.baseurl }}" width="600" /></div>

线程组主要包含三个参数：

**线程数**：虚拟用户的数量，一个线程指一个线程或者进程

**Ramp—Up Period(in seconds)**：准备时长。设置的线程数需要多久全部启动，比如上图，线程数为6000，启动时间为60，那么需要60S内启动6000个线程；

**循环次数**：每个线程发送请求的次数。如上图，6000个线程，每个线程发送1次，如果勾选了永远，那么它将永远发送下去，直到停止脚本；

（2）添加sampler

添加完线程组后，在线程组上右键单击：添加→Sampler→HTTP请求

<div align="center"><img src="{{ "/images/jmeterUse/3.png" | prepend: site.baseurl }}" width="600" /></div>

取样器（Sampler）是与服务器进行交互的单元。一个取样器通常进行三部分的工作：向服务器发送请求，记录服务器的响应数据和记录相应时间信息

<div align="center"><img src="{{ "/images/jmeterUse/4.png" | prepend: site.baseurl }}" width="600" /></div>

（3）添加监听器

在线程组上右键单击，添加你需要的监听器，一般常用的就是结果树和聚合报告

<div align="center"><img src="{{ "/images/jmeterUse/5.png" | prepend: site.baseurl }}" width="600" /></div>

（4）点击保存按钮，保存为.jmx文件

<br>
### 二、在linux下执行测试计划

（1）把脚本上传到 linxu环境，可以在脚本里面直接修改参数（并发数、运行时间、参数文件的位置）

（2）执行测试计划，命令：jmeter -n -t /root/test.jmx -l /root/test.jtl

&nbsp;&nbsp;参数说明：   
&nbsp;&nbsp;&nbsp;&nbsp;-h 帮助 -> 打印出有用的信息并退出   
&nbsp;&nbsp;&nbsp;&nbsp;-n 非 GUI 模式 -> 在非 GUI 模式下运行 JMeter   
&nbsp;&nbsp;&nbsp;&nbsp;-t 测试文件 -> 要运行的 JMeter 测试脚本文件   
&nbsp;&nbsp;&nbsp;&nbsp;-l 日志文件 -> 记录结果的文件   
&nbsp;&nbsp;&nbsp;&nbsp;-r 远程执行 -> 启动远程服务   
&nbsp;&nbsp;&nbsp;&nbsp;-H 代理主机 -> 设置 JMeter 使用的代理主机   
&nbsp;&nbsp;&nbsp;&nbsp;-P 代理端口 -> 设置 JMeter 使用的代理主机的端口号   

&nbsp;&nbsp;执行命令后还需要观察打压过程是否有报错，监控linux服务器的cpu 、内存、负载等。

<br>
### 三、运行结果的查看

1.将运行脚本产生的test.jtl文件到处到windows系统下（注意：window下安装的Jmeter和JDK要和Linux的保持一致）

2.在windows系统下打开Jmeter，创建一个线程组，在线程组下添加监听器，如下图：

<div align="center"><img src="{{ "/images/jmeterUse/6.png" | prepend: site.baseurl }}" width="600" /></div>

3.点击界面上的浏览按钮，将到处的文件添加进来即可看到脚本测试的报告，如下图：

<div align="center"><img src="{{ "/images/jmeterUse/7.png" | prepend: site.baseurl }}" width="600" /></div>