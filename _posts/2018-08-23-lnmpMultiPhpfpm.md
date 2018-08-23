---
layout: post
title: "lnmp配置多个php-fpm实例"
date: 2018-08-23 16:19:00 +0800
categories: lnmp
description: "Linux技巧"
tag: lnmp
---

> 之前在php-fpm.conf中设置了pm.max_children=400，但是高并发时nginx发起的连接数，远远超过了php-fpm所能处理的数目，导致端口（或socket）频繁被锁，造成堵塞，就会出现502错误。

> 解决方案：启用两个php-fpm实例，把php-fpm分为两部分，每部分各听一个端口或socket，这样就减少了lock，依然保持400个php-fpm进程，每个实例启用200个，采用nginx的upstream负载均衡，轮询每个socket来处理请求。

### 具体操作：

**1.切换到 /usr/local/php/etc/ 目录下，执行：cp php-fpm.conf php-fpm2.conf**

&nbsp;&nbsp;**修改php-fpm2.conf：**

{% highlight shell linenos %}
[global]
pid = /usr/local/php/var/run/php-fpm2.pid #修改此行
error_log = /usr/local/php/var/log/php-fpm2.log #修改此行
log_level = notice

[www]
#listen = /tmp/php-cgi2.sock
listen = /dev/shm/php-cgi2.sock #修改此行，注意路径
listen.backlog = 2048
listen.allowed_clients = 127.0.0.1
listen.owner = www
listen.group = www
listen.mode = 0666
user = www
group = www
pm = static
pm.max_children = 200 #修改此行，同时把php-fpm.conf中的此项也改为200
pm.start_servers = 10
pm.min_spare_servers = 10
pm.max_spare_servers = 20
request_terminate_timeout = 120
request_slowlog_timeout = 5s
slowlog = var/log/slow2.log
{% endhighlight %}

<br>

**2.切换到 /etc/init.d/ 目录下，执行：cp php-fpm php-fpm2**

&nbsp;&nbsp;**修改php-fpm2：**

{% highlight shell linenos %}
#这里只列出了部分代码
prefix=/usr/local/php
exec_prefix=${prefix}
php_fpm_BIN=${exec_prefix}/sbin/php-fpm
php_fpm_CONF=${prefix}/etc/php-fpm2.conf #修改此行
php_fpm_PID=${prefix}/var/run/php-fpm2.pid #修改此行
{% endhighlight %}

&nbsp;&nbsp;**启动php-fpm2，命令：/etc/init.d/php-fpm2 start**

<br>

**3.配置nginx**

&nbsp;&nbsp;**修改nginx.conf文件，在http模块中加入upstream配置：**

{% highlight shell linenos %}
#添加以下代码
upstream backend
    {
        server unix:/dev/shm/php-cgi.sock;
        server unix:/dev/shm/php-cgi2.sock;
    }
{% endhighlight %}

&nbsp;&nbsp;**修改nginx关于php的配置，lnmp是在enable-php.conf中：**
{% highlight shell linenos %}
location ~ [^/]\.php(/|$)
    {
        try_files $uri =404;
        #fastcgi_pass  unix:/tmp/php-cgi.sock;
        #fastcgi_pass  unix:/dev/shm/php-cgi.sock;
        fastcgi_pass backend; #注释上一行，加入这一行
        fastcgi_index index.php;
        include fastcgi.conf;
    }
{% endhighlight %}

&nbsp;&nbsp;**平滑重启nginx，命令：/usr/local/nginx/sbin/nginx -s reload**

<br>

**4.至此配置完成，可以开始测试了。**