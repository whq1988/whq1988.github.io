---
layout: post
title: "使用Jekyll和GitHub Page搭建博客"
date: 2018-04-08 17:39:45 +0800
categories: jekyll
description: "使用Jekyll和GitHub Page搭建博客"
tag: 搭建博客
---

### 概述
Jekyll 是一个简单的博客形态的静态站点生产机器。它有一个模版目录，其中包含原始文本格式的文档，通过 Markdown （或者 Textile） 以及 Liquid 转化成一个完整的可发布的静态网站，你可以发布在任何你喜爱的服务器上。Jekyll 也可以运行在 GitHub Page 上，也就是说，你可以使用 GitHub 的服务来搭建你的项目页面、博客或者网站，而且是完全免费的。  

<br>
### 1.在本机搭建Jekyll环境 ，并生成博客项目
参考[Jekyll中文文档](https://www.jekyll.com.cn/)  

<br>
### 2.配置 github page  
1. 登录[github](https://github.com)  
2. 点击页面右上角的加号，选择‘New repository’进入代码库创建页面，在Repository name下填写{yourname}.github.io，点击‘Create repository’按钮  
3. 正确创建后将进入该代码库页面，点击界面右侧的Settings进入设置页面，向下滚动，直到看见GitHub Pages，点击Automatic page generator，Github将会自动替你创建出一个gh-pages的页面  
4. 以上操作没问题的话，过一会儿，{yourname}.github.io这个域名就可以正常访问了  

<br>
### 3.将本地博客项目的代码部署到github  
1. 使用git工具，克隆上一步中的代码库到本地  
2. 将博客项目的代码拷贝过来  
3. 提交代码到github代码库  
4. 以上操作没问题的话，访问{yourname}.github.io就可以看到博客的效果了  

<br>
### 4.绑定自己的域名  
1. 在博客项目根目录下创建一个名为‘CNAME’的文件，然后在里面写上自己需要绑定的域名  
2. 域名解析，解析类型为‘A记录’，记录值为 192.30.252.153 或 192.30.252.154，是固定的，详情参考 [https://help.github.com/articles/setting-up-an-apex-domain/](https://help.github.com/articles/setting-up-an-apex-domain/)  
3. 绑定成功后，可直接访问自己的域名，或者输入{yourname}.github.io会自动跳转到自己的域名  

<br>
### 5.更换模板，下载模板后进行替换
[Jekyll主题列表](http://jekyllthemes.org/)  

<br>
### 6.代码高亮（使用rouge）
1. 修改 _config.yml 文件，修改其中的 highlighter为rouge  
2. 生成 rouge.css 文件（命令：rougify style monokai.sublime > rouge.css）,放到相应位置，然后在头部加载进来  
3. 默认的 rouge 主题偏向于白色，所以设置了背景为黑色效果会更好。同时为了避免代码过长时把代码块撑开，加入了横向滚动条。代码如下：
{% highlight css linenos %}
    <link rel="stylesheet" href="{{ "/css/rouge.css" | prepend: site.baseurl }}">
    <style>
        pre{
            background: rgba(0, 0, 0, 0.95);
            overflow: hidden;
        }
    </style>
{% endhighlight %}

<br>
### 7.本地创建博客
1. 使用 jekyll serve --watch --drafts
2. 在浏览器中 http://localhost:4000/ 预览