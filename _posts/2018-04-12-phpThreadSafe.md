---
layout: post
title: "php Thread Safe(线程安全)和None Thread Safe(NTS，非线程安全)的区别"
date: 2018-04-12 17:19:00 +0800
categories: php
description: "php Thread Safe(线程安全)和None Thread Safe(NTS，非线程安全)的区别"
tag: php
---

**Windows版的PHP从版本5.2.1开始有Thread Safe(线程安全)和None Thread Safe(NTS，非线程安全)之分，这两者不同在于何处？到底应该用哪种？这里做一个简单的介绍。**

从2000年10月20日发布的第一个Windows版的PHP3.0.17开始的都是线程安全的版本，这是由于与Linux/Unix系统是采用多进程的工作方式不同的是Windows系统是采用多线程的工作方式。如果在IIS下以CGI方式运行PHP会非常慢，这是由于CGI模式是建立在多进程的基础之上的，而非多线程。

一般我们会把PHP配置成以ISAPI的方式来运行，ISAPI是多线程的方式，这样就快多了。但存在一个问题，很多常用的PHP扩展是以Linux/Unix的多进程思想来开发的，这些扩展在ISAPI的方式运行时就会出错搞垮IIS。因此在IIS下CGI模式才是PHP运行的最安全方式，但CGI模式对于每个HTTP请求都需要重新加载和卸载整个PHP环境，其消耗是巨大的。

为了兼顾IIS下PHP的效率和安全，微软给出了FastCGI的解决方案。FastCGI可以让PHP的进程重复利用而不是每一个新的请求就重开一个进程。同时FastCGI也可以允许几个进程同时执行。这样既解决了CGI进程模式消耗太大的问题，又利用上了CGI进程模式不存在线程安全问题的优势。

**因此，如果是使用ISAPI的方式来运行PHP就必须用Thread Safe(线程安全)的版本；而用FastCGI模式运行PHP的话就没有必要用线程安全检查了，用None Thread Safe(NTS，非线程安全)的版本能够更好的提高效率。**


PHP官方http://php.net/上关于widows的版本有4个：VC9 x86 Non Thread Safe，VC9 x86 Thread Safe，VC6 x86 Non Thread Safe，VC6 x86 Thread Safe；那么有什么区别呢？

这是对windows系统中运行库依赖 

PHP是用C语言开发的所以依赖VC库运行

Visual C++ 2003 运行库(VC7)

Visual C++ 2005 运行库(VC8)

Visual C++ 2008 运行库(VC9)

Visual C++ 2010 运行库(VC10)

Visual C++ 2012 运行库(VC11)

Visual C++ 2013 运行库(VC12)

只有安装了相应的运行库才能运行PHP,如php5.5要求VC11的安装 

<br>
### 运行方式的不同

ISAPI执行方式是以DLL动态库的形式使用，可以在被用户请求后执行，在处理完一个用户请求后不会马上消失，所以需要进行线程安全检查，这样来提高程序的执行效率，所以如果是以ISAPI来执行PHP，建议选择Thread Safe版本；

而FastCGI执行方式是以单一线程来执行操作，所以不需要进行线程的安全检查，除去线程安全检查的防护反而可以提高执行效率，所以，如果是以FastCGI来执行PHP，建议选择Non Thread Safe版本。

对于apache服务器来说一般选择isapi方式，而对于nginx服务器则选择FastCGI方式。