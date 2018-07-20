---
layout: post
title: "CentOS6.5安装SVN & 可视化管理工具iF.SVNAdmin"
date: 2018-07-18 14:01:00 +0800
categories: svn
description: "CentOS6.5安装SVN & 可视化管理工具iF.SVNAdmin"
tag: svn
--- 

### 1.安装apache

通常系统都已经装好了，但我的服务器上却没有安装，所以要安装： 

&nbsp;&nbsp;# yum install httpd

*由于之前已经安装了nginx，故把apache的监听端口改为8080，打开httpd.conf文件，找到‘Listen 80’，改为‘Listen 8080’*

### 2.安装SVN(版本最好是1.7+)

根据SVN官网指南使用yum进行安装：	

&nbsp;&nbsp;# yum install subversion mod_dav_svn

*由于CentOS6.5默认自带的svn是1.6版本，因此需要先做一些设置*

(1)查看svn版本： # svn --version  

(2)先删除旧版本： # yum remove subversion

(3)更新subversion源：

{% highlight php %}
	// 创建wandisco-svn.repo文件并修改
    # touch /etc/yum.repos.d/wandisco-svn.repo
    # vim /etc/yum.repos.d/wandisco-svn.repo
    // 修改为以下内容
    [WandiscoSVN]
	name=Wandisco SVN Repo
	baseurl=http://opensource.wandisco.com/centos/6/svn-1.7/RPMS/$basearch/
	enabled=1
	gpgcheck=0
{% endhighlight %}

(4)安装subversion

{% highlight php %}
    # yum clean all
    # yum install subversion
{% endhighlight %}

### 3.配置SVN

装完SVN后默认生成/etc/httpd/conf.d/subversion.conf文件，修改为以下：

<div align="center"><img src="{{ "/images/svnInstall/1.jpg" | prepend: site.baseurl }}" width="600" /></div>

### 4.创建SVN repo目录和权限信息目录

&nbsp;&nbsp;# mkdir /var/www/svn

&nbsp;&nbsp;# mkdir /var/www/svnconfig

### 5.创建SVN权限文件和密码文件

&nbsp;&nbsp;# touch /var/www/svnconfig/accessfile

&nbsp;&nbsp;# touch /var/www/svnconfig/passwdfile

### 6.安装php

&nbsp;&nbsp;# yum install php

&nbsp;&nbsp;*安装时会自动在apache的conf.d目录下生成php.conf文件，因为apache的配置文件httpd.conf中有‘Include conf.d/*.conf’这样一句，因此只需重启下apache即可把两者关联上*

### 7.安装iF.SVNAdmin

下载：

&nbsp;&nbsp;# wget http://sourceforge.net/projects/ifsvnadmin/files/svnadmin-1.6.2.zip

解压：

&nbsp;&nbsp;# unzip svnadmin-1.6.2.zip

把解压后的文件 iF.SVNAdmin-stable-1.6.2拷贝到/var/www/html/svnadmin：

&nbsp;&nbsp;# cp -r iF.SVNAdmin-stable-1.6.2/ /var/www/html/svnadmin

更改data目录的读写模式：

&nbsp;&nbsp;# chmod -R 777 /var/www/html/svnadmin/data/ 

更改/var/www/html/svnadmin/权属：

&nbsp;&nbsp;# chown -R apache:apache /var/www/html/svnadmin/

更改 /var/www/svn的读写模式：

&nbsp;&nbsp;#chmod -R 777 /var/www/svn

更改下列两个文件的读写模式：

&nbsp;&nbsp;# chmod 777 /var/www/svnconfig/accessfile

&nbsp;&nbsp;# chmod 777 /var/www/svnconfig/passwdfile

### 8.启动apache服务

&nbsp;&nbsp;# /etc/init.d/httpd restart

*注：启动时可能会出现‘httpd: apr_sockaddr_info_get() failed for {hostname}’这样的错误提示，这是由于主机名设置有问题导致的。可执行 # hostname {主机名}，执行后重启apache即可*

<br>
**以上安装完成，启动后浏览器输入http://服务器地址/svnadmin/ 后登录，默认用户名和密码都是admin，如下图:**

<div align="center"><img src="{{ "/images/svnInstall/2.jpg" | prepend: site.baseurl }}" width="200" /></div>

登录后如下，输入各个配置文件的路径后点击Test进行测试是否成功，全部成功后保存配置Save configration：

<div align="center"><img src="{{ "/images/svnInstall/3.jpg" | prepend: site.baseurl }}" width="700" /></div>

*注1：添加新仓库时，可能会出现以下情况，这通常是svn版本低造成的，需要升级svn版本：*

<div align="center"><img src="{{ "/images/svnInstall/4.jpg" | prepend: site.baseurl }}" width="600" /></div>

*注2：在浏览器中浏览代码仓库时，可能会出现错误提示‘/usr/bin/svn error=svn: warning: W000013: Can't open file '/root/.subversion/servers': Permission denied’，解决方案：修改/var/www/html/svnadmin/include/config.inc.php文件，设置define("IF_SVNBaseC_ConfigDir", "/tmp/");*

<br>
*参考自：[CentOS6.5安装SVN & 可视化管理工具iF.SVNAdmin](https://www.linuxidc.com/Linux/2015-12/126486.htm)*