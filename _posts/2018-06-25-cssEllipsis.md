---
layout: post
title: "css 文字超过行宽后隐藏显示省略号"
date: 2018-06-25 10:44:00 +0800
categories: css
description: "文字超过行宽后隐藏显示省略号"
tag: css
--- 

{% highlight css linenos %}
	//单行
    .text{
		overflow: hidden;    //超出一行文字自动隐藏
		text-overflow: ellipsis;    //文字隐藏后添加省略号
		white-space: nowrap;    //强制不换行
	}
	//多行
	.text{
		display: -webkit-box;
    	word-break: break-all;
	    text-overflow: ellipsis;
	    font-size: 32rpx;
	    overflow: hidden;
	    -webkit-box-orient: vertical;
	    -webkit-line-clamp: 2;
	}
{% endhighlight %}

