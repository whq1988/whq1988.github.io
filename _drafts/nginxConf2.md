---
layout: post
title: "Nginx配置2(location区块)"
date: 2018-08-17 15:31:00 +0800
categories: nginx
description: "Nginx配置2(location区块)"
tag: nginx
---

### location的作用

location指令的作用是根据用户请求的URI来执行不同的应用，也就是根据用户请求的网站URL进行匹配，匹配成功即进行相关的操作。

2.location的语法

location    [ = \| ~ \| ~* \| ^~ \| \| / ]    uri    {...}

包含四个部分： 指令  匹配标识  匹配的网站网址  匹配URI之后要执行的配置段

其中“**=**”的优先级为最高，为精确匹配；

特殊字符“**~**”和“**~\***”的区别在于前者区分大小写，后者不区分大小写，他们还可以用逻辑操作符“**!**”来取反匹配，例如：“**!~**”和“**!~\***”，前者不区分大小写，后者区分大小写

“**^~**”的意思是匹配之后不做正则表达式的检查，就是不用匹配类似于“**\.(gif\|jpg\|jpeg)$**”的正则表达式了，也就是说“**^~**”后面跟了正则表达式也没有用的。

/ 通用匹配，任何请求都会匹配到。

多个location配置的情况下匹配顺序为（参考资料而来，还未实际验证，试试就知道了，不必拘泥，仅供参考）：
首先匹配 =，其次匹配^~, 其次是按文件中顺序的正则匹配，最后是交给 / 通用匹配。当有匹配成功时候，停止匹配，按当前匹配规则处理请求。

{% highlight shell linenos %}
    location = / {  
       #规则A
    }  
    location = /login {  
       #规则B
    }  
    location ^~ /static/ {  
       #规则C
    }  
    location ~ \.(gif|jpg|png|js|css)$ {  
       #规则D  
    }  
    location ~* \.png$ {  
       #规则E  
    }  
    location !~ \.xhtml$ {  
       #规则F  
    }  
    location !~* \.xhtml$ {  
       #规则G  
    }  
    location / {  
       #规则H  
    }  
{% endhighlight %}

那么产生的效果如下：

访问根目录/， 比如http://localhost/ 将匹配规则A

访问 http://localhost/login 将匹配规则B，http://localhost/register 则匹配规则H

访问 http://localhost/static/a.html 将匹配规则C

访问 http://localhost/a.gif, http://localhost/b.jpg 将匹配规则D和规则E，但是规则D顺序优先，规则E不起作用，而 http://localhost/static/c.png 则优先匹配到 规则C

访问 http://localhost/a.PNG 则匹配规则E， 而不会匹配规则D，因为规则E不区分大小写。

访问 http://localhost/a.xhtml 不会匹配规则F和规则G，http://localhost/a.XHTML不会匹配规则G，因为不区分大小写。规则F，规则G属于排除法，符合匹配规则但是不会匹配到，所以想想看实际应用中哪里会用到。

访问 http://localhost/category/id/1111 则最终匹配到规则H，因为以上规则都不匹配，这个时候应该是nginx转发请求给后端应用服务器，比如FastCGI（php），tomcat（jsp），nginx作为方向代理服务器存在。