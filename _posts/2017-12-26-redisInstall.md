---
layout: post
title: "linux安装redis && php安装redis扩展"
date: 2017-12-26 17:19:00 +0800
categories: redis
description: "linux安装redis && php安装redis扩展"
tag: redis
---

### linux安装redis  

**1.下载安装包https://redis.io/download，解压到/home/下**

**2.进入redis目录，执行make**(make完后 redis 目录下会出现编译后的redis服务程序redis-server,还有用于测试的客户端程序redis-cli,两个程序位于安装目录 src 目录下)

**3.启动redis服务(src目录下):**
* 使用默认配置: ./redis-server
* 使用自定义配置(*不推荐，最好写在配置文件中*): 如./redis-server redis-server –port 6389 –timeout 3000
* 指定配置文件: ./redis-server /home/redis/redis.conf
* 后台运行：./redis-server &

*最好是使用 ./redis-server /home/redis/redis.conf & ,指定配置文件，如果不指定该配置文件，修改的redis.conf（比如密码项）不会生效*

**4.关闭redis服务（两种方式）**

* 通过redis-cli连接服务器后执行shutdown命令，则执行停止redis服务操作。shutdown还有一个参数，代表关闭redis服务前是否生产持久化文件：shutdown save\|nosave
* 使用kill+进程号的方式关闭redis服务。

&nbsp;&nbsp;a.执行 ps -ef \|grep redis   

&nbsp;&nbsp;b.找到进程号，执行 kill -9 <进程号>
      最好不要使用Kill 9方式关闭redis进程，这样redis不会进行持久化操作，除此之外，还会造成缓冲区等资源不能优雅关闭，极端情况下会造成AOF和复制丢失数据的情况

**5.启动redis客户端命令行(src目录下), 命令：**./redis-cli

**6.设置密码，两种方式：**

* **方式一：**在redis命令行中设置(仅本次有效，重启reids服务后失效)

&nbsp;&nbsp;a.查看是否设置了密码验证：CONFIG get requirepass

&nbsp;&nbsp;b.设置密码：CONFIG set requirepass {password}

&nbsp;&nbsp;c.设置密码后，客户端连接 redis 服务就需要密码验证，命令：AUTH {password}

* **方式二：**修改redis.conf文件，找到 requirepass 项进行配置（配置后，开启redis服务必须要指定该配置文件，不然不会生效）

**7.配置项(在cli下)**

&nbsp;&nbsp;a.查看某个配置：CONFIG GET ，例如：CONFIG GET loglevel

&nbsp;&nbsp;b.查看所有配置：CONFIG GET *

&nbsp;&nbsp;c.编辑某个配置：CONFIG SET ，例如：CONFIG SET loglevel “notice”

*注：配置文件中默认只允许本地连接(bind 127.0.0.1)，如果需要外部连接，需要把这句注释掉*

**8.参数说明**

&nbsp;&nbsp;(1)Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程

&nbsp;&nbsp;&nbsp;&nbsp;**daemonize no**

&nbsp;&nbsp;(2)当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定

&nbsp;&nbsp;&nbsp;&nbsp;**pidfile /var/run/redis.pid**

&nbsp;&nbsp;(3)指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字

&nbsp;&nbsp;&nbsp;&nbsp;**port 6379**

&nbsp;&nbsp;(4)绑定的主机地址

&nbsp;&nbsp;&nbsp;&nbsp;**bind 127.0.0.1**

&nbsp;&nbsp;(5)当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能

&nbsp;&nbsp;&nbsp;&nbsp;**timeout 300**

&nbsp;&nbsp;(6)指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose

&nbsp;&nbsp;&nbsp;&nbsp;**loglevel verbose**

&nbsp;&nbsp;(7)日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null

&nbsp;&nbsp;&nbsp;&nbsp;**logfile stdout**

&nbsp;&nbsp;(8)设置数据库的数量，默认数据库为0，可以使用SELECT <dbid>命令在连接上指定数据库id

&nbsp;&nbsp;&nbsp;&nbsp;**databases 16**

&nbsp;&nbsp;(9)指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合

    save <seconds> <changes>

    Redis默认配置文件中提供了三个条件：

    save 900 1

    save 300 10

    save 60 10000

    分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。

&nbsp;&nbsp;(10)指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大

&nbsp;&nbsp;&nbsp;&nbsp;**rdbcompression yes**

&nbsp;&nbsp;(11)指定本地数据库文件名，默认值为dump.rdb

&nbsp;&nbsp;&nbsp;&nbsp;**dbfilename dump.rdb**

&nbsp;&nbsp;(12)指定本地数据库存放目录

&nbsp;&nbsp;&nbsp;&nbsp;**dir ./**

&nbsp;&nbsp;(13)设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步

&nbsp;&nbsp;&nbsp;&nbsp;**slaveof \<masterip\> \<masterport\>**

&nbsp;&nbsp;(14)当master服务设置了密码保护时，slav服务连接master的密码

&nbsp;&nbsp;&nbsp;&nbsp;**masterauth \<master-password\>**

&nbsp;&nbsp;(15)设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH \<password\>命令提供密码，默认关闭

&nbsp;&nbsp;&nbsp;&nbsp;**requirepass foobared**

&nbsp;&nbsp;(16)设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息

&nbsp;&nbsp;&nbsp;&nbsp;**maxclients 128**

&nbsp;&nbsp;(17)指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区

&nbsp;&nbsp;&nbsp;&nbsp;**maxmemory \<bytes\>**

&nbsp;&nbsp;(18)指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no

&nbsp;&nbsp;&nbsp;&nbsp;**appendonly no**

&nbsp;&nbsp;(19)指定更新日志文件名，默认为appendonly.aof

&nbsp;&nbsp;&nbsp;&nbsp;**appendfilename appendonly.aof**

&nbsp;&nbsp;(20)指定更新日志条件，共有3个可选值： 
&nbsp;&nbsp;&nbsp;&nbsp;no：表示等操作系统进行数据缓存同步到磁盘（快） 
&nbsp;&nbsp;&nbsp;&nbsp;always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全） 
&nbsp;&nbsp;&nbsp;&nbsp;everysec：表示每秒同步一次（折衷，默认值）

&nbsp;&nbsp;&nbsp;&nbsp;**appendfsync everysec**

&nbsp;&nbsp;(21)指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）

&nbsp;&nbsp;&nbsp;&nbsp;**vm-enabled no**

&nbsp;&nbsp;(22)虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享

&nbsp;&nbsp;&nbsp;&nbsp;**vm-swap-file /tmp/redis.swap**

&nbsp;&nbsp;(23)将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0

&nbsp;&nbsp;&nbsp;&nbsp;**vm-max-memory 0**

&nbsp;&nbsp;(24)Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值

&nbsp;&nbsp;&nbsp;&nbsp;**vm-page-size 32**

&nbsp;&nbsp;(25)设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。

&nbsp;&nbsp;&nbsp;&nbsp;**vm-pages 134217728**

&nbsp;&nbsp;(26)设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4

&nbsp;&nbsp;&nbsp;&nbsp;**vm-max-threads 4**

&nbsp;&nbsp;(27)设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启

&nbsp;&nbsp;&nbsp;&nbsp;**glueoutputbuf yes**

&nbsp;&nbsp;(28)指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法

&nbsp;&nbsp;&nbsp;&nbsp;**hash-max-zipmap-entries 64**

&nbsp;&nbsp;&nbsp;&nbsp;**hash-max-zipmap-value 512**

&nbsp;&nbsp;(29)指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）

&nbsp;&nbsp;&nbsp;&nbsp;**activerehashing yes**

&nbsp;&nbsp;(30) 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件

&nbsp;&nbsp;&nbsp;&nbsp;**include /path/to/local.conf**

<br>

### linux安装redis   

**1.https://github.com/phpredis/phpredis/releases 下载扩展包**

{% highlight php linenos %}
  $ cd phpredis-x.x.x                      # 进入 phpredis 目录
  $ /usr/local/php/bin/phpize              # php安装后的路径
  $ ./configure --with-php-config=/usr/local/php/bin/php-config
  $ make && make install   #安装信息最后会显示一行：Installing shared extensions:  /usr/local/php/lib/php/extensions/no-debug-non-zts-20131226/
{% endhighlight %}

**2.编辑php.ini,加入以下两行**

&nbsp;&nbsp;extension_dir = “/usr/local/php/lib/php/extensions/no-debug-non-zts-20131226″ #上面安装信息最后一行里面的

&nbsp;&nbsp;extension=”redis.so”

**3.重启web服务**