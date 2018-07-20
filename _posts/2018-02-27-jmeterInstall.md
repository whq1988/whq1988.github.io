---
layout: post
title: "Jmeter安装 （windows和linux下）"
date: 2018-02-27 14:23:00 +0800
categories: jmeter
description: "Jmeter安装 （windows和linux下）"
tag: jmeter
---

> 以安装jmeter3.3为例，通常在windows下设置好参数后保存jmx文件，linux下执行该jmx文件；windows和linux最好安装同样的jmeter版本

### 一、windows下安装

**第一步：安装jdk**

&nbsp;&nbsp;（1）先检查自己环境的jdk版本，window cmd下执行 java -version ，查看jdk版本， 如果低于1.8，则需要下载jdk，重新安装，否则jmeter会因为需要的jdk版本高于现有版本而无法运行

&nbsp;&nbsp;（2）jdk下载官网：
http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

&nbsp;&nbsp;（3）点击exe文件进行安装

&nbsp;&nbsp;（4）配置环境变量(我的电脑->右键：属性->系统->系统高级设置->高级->环境变量)

&nbsp;&nbsp;&nbsp;&nbsp;新建系统变量如下：

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;JAVA_HOME : {jdk安装路径}

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;CLASSPATH : .;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar (*注意不要忘记前面的点和中间的分号*)

&nbsp;&nbsp;&nbsp;&nbsp;修改系统变量里的path变量，增加两行如下：

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;%JAVA_HOME%\bin

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;%JAVA_HOME%\jre\bin

&nbsp;&nbsp;（5）在cmd中执行 java -version ，查看jdk版本，如果是安装的版本，说明安装成功


**第二步：安装jmeter**

&nbsp;&nbsp;（1）下载地址：http://jmeter.apache.org/download_jmeter.cgi

&nbsp;&nbsp;（2）解压jmeter安装包

&nbsp;&nbsp;（3）配置环境变量

&nbsp;&nbsp;&nbsp;&nbsp;JMETER_HOME : {jmeter解压路径}
&nbsp;&nbsp;&nbsp;&nbsp;CLASSPATH : %JMETER_HOME\lib\ext\ApacheJMeter_core.jar;%JMETER_HOME%\lib\jorphan.jar;%JMETER_HOME%\lib\logkit-2.0.jar;

&nbsp;&nbsp;（4）点击jmeter解压目录下bin下面的jmeter.bat，即可启动jmeter

<br>

### 二、linux下安装

**第一步：安装jdk**

&nbsp;&nbsp;（1）检查当前系统已安装jdk的版本: java -version， 如果低于1.8，则需要下载jdk，重新安装，否则jmeter会因为需要的jdk版本高于现有版本而无法运行

&nbsp;&nbsp;（2）jdk下载官网：
http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html，下载rpm安装包

&nbsp;&nbsp;（3）移动rpm包至/opt目录下，执行 rpm -ivh {文件名}.rpm，进行安装

&nbsp;&nbsp;（4）设置环境变量，执行 vim /etc/profile，在文件最前面增加以下内容，然后执行 source /etc/profile 即可生效

&nbsp;&nbsp;&nbsp;&nbsp;export JAVA_HOME=/opt/{jdk版本(如jdk1.8.0_131)}

&nbsp;&nbsp;&nbsp;&nbsp;export JRE_HOME=${JAVA_HOME}/jre   

&nbsp;&nbsp;&nbsp;&nbsp;export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib   

&nbsp;&nbsp;&nbsp;&nbsp;export PATH=${JAVA_HOME}/bin:$PATH   

&nbsp;&nbsp;（5）执行 java -version ，查看jdk版本，如果是安装的版本，说明安装成功


**第二步：安装jmeter**

&nbsp;&nbsp;（1）下载地址：http://jmeter.apache.org/download_jmeter.cgi

&nbsp;&nbsp;（2）解压jmeter安装包到/opt下

&nbsp;&nbsp;（3）配置环境变量，增加 export PATH=/opt/{jmeter解压路径}/bin/:$PATH，然后执行 source /etc/profile 即可生效

&nbsp;&nbsp;（4）检验jmeter是否可以运行：jmeter -v