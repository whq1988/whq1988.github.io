---
layout: post
title: "Nginx配置(全局 && event模块 && http模块)"
date: 2018-08-17 15:31:00 +0800
categories: nginx
description: "Nginx配置(全局 && event模块 && http模块)"
tag: nginx
---

### 以lnmp中的nginx.conf为例，路径为/usr/nginx/conf 

**include vhost/\*.conf;** ：加载各个虚拟主机配置文件，以下为示例：

server
    {
        listen 80;
        #listen [::]:80;
        server_name wechat.wanghaiquan.cn;
        index index.html index.htm index.php default.html default.htm default.php;
        root  /home/wwwroot/yii-wechat.wanghaiquan.cn/api/web;

        location / {
          # Redirect everything that isn't a real file to index.php
          try_files $uri $uri/ /index.php?$args;
        }

        # 与上面的location设置都可以
        #location / {
        #    if (!-e $request_filename){
        #        rewrite ^/(.*) /index.php last;
        #    }
        #}

        include none.conf;
        #error_page   404   /404.html;

        

        # Deny access to PHP files in specific directory
        #location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }

        include enable-php.conf;

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires      30d;
        }

        location ~ .*\.(js|css)?$
        {
            expires      12h;
        }

        location ~ /.well-known {
            allow all;
        }

        location ~ /\.
        {
            deny all;
        }

        access_log off;
    }