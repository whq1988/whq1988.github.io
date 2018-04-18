---
layout: post
title: "Linux技巧"
date: 2018-04-18 17:19:00 +0800
categories: linux
description: "Linux技巧"
tag: linux
---

## 基础篇  

### 一、使用linux命令统计文本中某个单词的出现频率
{% highlight shell linenos %}
    more {filename} | grep -o {word} | wc -l
    或
    cat {filename} | grep -o {word} | wc -l
{% endhighlight %}


<br><br>
## 进阶篇  