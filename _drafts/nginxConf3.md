---
layout: post
title: "Nginx配置3(Fastcgi,以php为例)"
date: 2018-08-21 09:31:00 +0800
categories: nginx
description: "Nginx配置3(fastcgi,以php为例)"
tag: nginx
---

### Fastcgi介绍

Fastcgi一个可伸缩地，高速地在HTTP服务器和动态脚本语言间通信的接口。

主要优点把动态语言和HTTP服务器分离开来。多数流行的HTTP服务器都支持FsatCGI包括Apache/Nginx/lighttpd等。

Nginx连接fastcgi的方式有2种：TCP 和 unix domain socket

支持语言比较流行的是PHP，接口方式采用C/S架构，可以将HTTP服务器和脚本解析器分开，同时在脚本解析服务器上启动一个或者多个脚本解析守护进程。

Nginx服务器通过例（Nginx fastgi_pass)FastCGI客户端和动态语言FastCGI服务端通信（例如：php-fpm）

当HTTP服务器每次遇到动态程序时，可以将其直接交付给FastCGI进程来执行，然后将得到的结果返回给浏览器。这种方式可以让HTTP服务器专一地处理静态请求或者将动态脚本服务器的结果返回给客户端，这在很大程度上提高了整个应用系统的性能。


### fastcgi_pass 监听端口 unix socket 和 tcp socket 对比

TCP是使用TCP端口连接127.0.0.1:9000 （fastcgi_pass  127.0.0.1:9000）

Socket是使用unix domain socket连接套接字/dev/shm/php-cgi.sock（很多教程使用路径/tmp，而路径/dev/shm是个tmpfs（详见另一篇博客《LINUX的TMPFS文件系统》），速度比磁盘快得多）（fastcgi_pass  unix:/tmp/php-cgi.sock）

在服务器压力不大的情况下，tcp和socket差别不大，但在压力比较满的时候，用套接字方式，效果确实比较好。


### 实例

下面是php 5.3以上版本将TCP改成socket方式的配置方法：

（1）修改php-fpm.conf（/usr/local/php/etc/php-fpm.conf）

\#listen = 127.0.0.1:9000

listen = /dev/shm/php-cgi.sock

（2）修改nginx配置文件server段的配置，将http的方式改为socket方式

{% highlight shell linenos %}
location ~ .\*\.(php|php5)?$  {
    #fastcgi_pass  127.0.0.1:9000;
    fastcgi_pass   unix:/dev/shm/php-cgi.sock;
    fastcgi_index index.php;
    include fastcgi.conf;
}
{% endhighlight %}

重启php-fpm与nginx

service nginx restart

service php-fpm restart

使用命令：ls -al /dev/shm，可看到该目录下面多了个php-cgi.sock文件