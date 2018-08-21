---
layout: post
title: "Nginx配置1(全局 && event区块 && http区块)"
date: 2018-08-17 13:14:00 +0800
categories: nginx
description: "Nginx配置1(全局 && event区块 && http区块)"
tag: nginx
---

### 以lnmp中的nginx.conf为例，路径为/usr/nginx/conf 

**user  www  www;** ：Nginx用户及组：用户 组，（windows下不指定该项）

**worker_processes  auto;** ：工作进程：数目。根据硬件调整，通常等于CPU数量或者2倍于CPU。自动的话为auto。

**error_log  /home/wwwlogs/nginx_error.log  crit;** ：错误日志。   
&nbsp;&nbsp;&nbsp;&nbsp;crit为错误日志级别，常见的错误日志级别有[debug \| info \| notice \| warn \| error \| crit \| alert \| emerg]，级别越高记录的信息越少。生产场景一般是 warn \| error \| crit 这三个级别之一，*注意：不要配置info等级较低的级别，会带来大量的磁盘I/O消耗*。

**pid  /usr/local/nginx/logs/nginx.pid;** ：pid（进程标识符）：存放路径。

**worker_rlimit_nofile  51200;** ：指定进程可以打开的最大描述符：数目。   
&nbsp;&nbsp;&nbsp;&nbsp;这个指令是指当一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均匀，所以最好与ulimit -n 的值保持一致。   
&nbsp;&nbsp;&nbsp;&nbsp;现在在linux 2.6内核下开启文件打开数为65535，worker_rlimit_nofile就相应应该填写65535。   
&nbsp;&nbsp;&nbsp;&nbsp;这是因为nginx调度时分配请求到进程并不是那么的均衡，所以假如填写10240，总并发量达到3-4万时就有进程可能超过10240了，这时会返回502错误。

<br>

* **events模块中包含nginx中所有处理连接的设置。**

**events**

&nbsp;&nbsp;**{**

&nbsp;&nbsp;&nbsp;&nbsp;**use epoll;** ：使用epoll的I/O 模型。linux建议epoll，FreeBSD建议采用kqueue，window下不指定。

&nbsp;&nbsp;&nbsp;&nbsp;**worker_connections 51200;** ：每个工作进程的最大连接数量。根据硬件调整，和前面工作进程配合起来用，尽量大，但是别把cpu跑到100%就行。每个进程允许的最多连接数，理论上每台nginx服务器的最大连接数为:worker_processes \* worker_connections

&nbsp;&nbsp;&nbsp;&nbsp;**multi_accept on;** ：设置是否允许，Nginx在已经得到一个新连接的通知时，接收尽可能更多的连接。

&nbsp;&nbsp;**}**

<br>

* **HTTP模块控制着nginx http处理的所有核心特性。因为这里只有很少的配置，所以我们只节选配置的一小部分。所有这些设置都应该在http模块中，甚至你不会特别的注意到这段设置。**

**http**

&nbsp;&nbsp;**{**

&nbsp;&nbsp;&nbsp;&nbsp;**include       mime.types;** ：mine.types内定义各文件类型映像   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;该文件格式：   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;types {   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;text/html    html htm shtml;   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;text/css     css;   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;text/xml     xml;   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}

&nbsp;&nbsp;&nbsp;&nbsp;**default_type  application/octet-stream;** ：设置默认类型是二进制流，若未设置时，比如未加载PHP时，是不予解析，用浏览器访问则出现下载窗口

<br>

&nbsp;&nbsp;&nbsp;&nbsp;**server_names_hash_bucket_size 128;** ：不能带单位！配置个主机时必须设置该值，否则无法运行Nginx或测试时不通过，该设置与server_names_hash_max_size 共同控制保存服务器名的HASH表，hash bucket size总是等于hash表的大小，并且是一路处理器缓存大小的倍数。若hash bucket size等于一路处理器缓存的大小，那么在查找键的时候，最坏的情况下在内存中查找的次数为2。第一次是确定存储单元的地址，第二次是在存储单元中查找键值。若报出hash max size 或 hash bucket size的提示，则我们需要增加server_names_hash_max_size的值。

&nbsp;&nbsp;&nbsp;&nbsp;**client_header_buffer_size 32k;** ：客户端请求头部的缓冲区大小，根据系统分页大小设置，分页大小可用命令getconf PAGESIZE取得

&nbsp;&nbsp;&nbsp;&nbsp;**large_client_header_buffers 4 32k;** ：4为个数，128k为大小，默认是4k。申请4个128k。当http 的URI太长或者request header过大时会报414 Request URI too large或400 bad request，这是很有可能是cookie中写入的值太大造成的，因为header中的其他参数的size一般比较固定，只有cookie可能被写入较 大的数据，这时可以调大上述两个值，相应的浏览器中cookie的字节数上限会增大。

&nbsp;&nbsp;&nbsp;&nbsp;**client_max_body_size 50m;** ：HTTP请求的BODY最大限制值，若超出此值，报413 Request Entity Too Large

<br>

&nbsp;&nbsp;&nbsp;&nbsp;**sendfile   on;** ：打开系统函数sendfile（）支持
        
&nbsp;&nbsp;&nbsp;&nbsp;**tcp_nopush on;** ：打开linux下TCP_CORK，sendfile打开时才有效，作减少报文段的数量之用

<br>

&nbsp;&nbsp;&nbsp;&nbsp;**keepalive_timeout 60;** ：keepalive超时时间

<br>

&nbsp;&nbsp;&nbsp;&nbsp;**tcp_nodelay on;** ：打开TCP_NODELAY在包含了keepalive才有效

<br>

&nbsp;&nbsp;&nbsp;&nbsp;**fastcgi_connect_timeout 300;** ：指定连接到后端FastCGI的超时时间

&nbsp;&nbsp;&nbsp;&nbsp;**fastcgi_send_timeout 300;** ：向FastCGI传送请求的超时时间，这个值是指已经完成两次握手后向FastCGI传送请求的超时时间。

&nbsp;&nbsp;&nbsp;&nbsp;**fastcgi_read_timeout 300;** ：接收FastCGI应答的超时时间，这个值是指已经完成两次握手后接收FastCGI应答的超时时间。

&nbsp;&nbsp;&nbsp;&nbsp;**fastcgi_buffer_size 64k;** ：这里可以设置为fastcgi_buffers指令指定的缓冲区大小

&nbsp;&nbsp;&nbsp;&nbsp;**fastcgi_buffers 4 64k;** ：指定本地需要用多少和多大的缓冲区来缓冲FastCGI的应答

&nbsp;&nbsp;&nbsp;&nbsp;**fastcgi_busy_buffers_size 128k;** ：建议为fastcgi_buffers的两倍

&nbsp;&nbsp;&nbsp;&nbsp;**fastcgi_temp_file_write_size 256k;** ：在写入fastcgi_temp_path时将用多大的数据块，默认值是fastcgi_buffers的两倍，设置上述数值设置太小时若负载上来时可能报 502 Bad Gateway

<br>

&nbsp;&nbsp;&nbsp;&nbsp;**gzip on;** ：打开GZIP压缩，实时压缩输出数据流

&nbsp;&nbsp;&nbsp;&nbsp;**gzip_min_length  1k;** ：从Content-Length中数值获取验证，小于1K会越压越大

&nbsp;&nbsp;&nbsp;&nbsp;**gzip_buffers     4 16k;** ：以16K为单位4倍的申请内存做压缩结果流缓存

&nbsp;&nbsp;&nbsp;&nbsp;**gzip_http_version 1.1;** ：

&nbsp;&nbsp;&nbsp;&nbsp;**gzip_comp_level 2;** ：压缩比率1-9，1压缩比最小处理速度最快，9压缩比最大但处理最慢且耗CPU

&nbsp;&nbsp;&nbsp;&nbsp;**gzip_types     text/plain application/javascript application/x-javascript text/javascript text/css application/xml application/xml+rss;** ：压缩类型

&nbsp;&nbsp;&nbsp;&nbsp;**gzip_vary on;** ：和http头有关系，加个vary头，给代理服务器用的，有的浏览器支持压缩，有的不支持，所以避免浪费不支持的也压缩，所以根据客户端的HTTP头来判断，是否需要压缩

&nbsp;&nbsp;&nbsp;&nbsp;**gzip_proxied   expired no-cache no-store private auth;** ：Nginx作为反向代理的时候启用，开启或者关闭后端服务器返回的结果，匹配的前提是后端服务器必须要返回包含”Via”的 header头。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;off – 关闭所有的代理结果数据的压缩   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;expired – 启用压缩，如果header头中包含 “Expires” 头信息   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;no-cache – 启用压缩，如果header头中包含 “Cache-Control:no-cache” 头信息   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;no-store – 启用压缩，如果header头中包含 “Cache-Control:no-store” 头信息   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;private – 启用压缩，如果header头中包含 “Cache-Control:private” 头信息   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;no_last_modified – 启用压缩,如果header头中不包含 “Last-Modified” 头信息   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;no_etag – 启用压缩 ,如果header头中不包含 “ETag” 头信息   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;auth – 启用压缩 , 如果header头中包含 “Authorization” 头信息   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;any – 无条件启用压缩


&nbsp;&nbsp;&nbsp;&nbsp;**gzip_disable   "MSIE [1-6]\.";** ：IE1-6对Gzip不怎么友好，不给它Gzip了

<br>

&nbsp;&nbsp;&nbsp;&nbsp;#limit_conn_zone $binary_remote_addr zone=perip:10m;   

&nbsp;&nbsp;&nbsp;&nbsp;##If enable limit_conn_zone,add "limit_conn perip 10;" to server section.

<br>

&nbsp;&nbsp;&nbsp;&nbsp;**server_tokens off;** ：该命令的作用是显示或隐藏掉版本号

&nbsp;&nbsp;&nbsp;&nbsp;**access_log off;** ：指令指定日志文件的存放路径

**以下是配置虚拟主机，要在HTTP模块内部配置，在另一篇blog中详解**

&nbsp;&nbsp;&nbsp;&nbsp;**server**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**{**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;//全局server配置

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**}**

&nbsp;&nbsp;&nbsp;&nbsp;include vhost/*.conf;


&nbsp;&nbsp;**}**