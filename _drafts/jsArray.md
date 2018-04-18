---
layout: post
title: "js 数组方法笔记"
date: 2018-04-18 17:07:00 +0800
categories: js
description: "js 数组方法笔记"
tag: js
---

## 基础篇  

### 一、获取数组中的最大值和最小值
{% highlight javascript linenos %}
    //一维数组
    var a=[1,2,3,5];
    alert( Math.max.apply(null, a) );   //5
    alert( Math.min.apply(null, a) );   //1
    //多维数组
    var a=[1,2,3,[5,6],[1,4,8]];
    var ta=a.join(",").split(",");  //转化为一维数组
    alert(Math.max.apply(null,ta)); //8
    alert(Math.min.apply(null,ta)); //1
{% endhighlight %}


<br><br>
## 进阶篇  