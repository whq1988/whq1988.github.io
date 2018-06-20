---
layout: post
title: "关于css中父元素与子元素之间margin-top的问题"
date: 2018-06-20 17:26:00 +0800
categories: css
description: "关于css中父元素与子元素之间margin-top的问题"
tag: css
---

**之前在使用经常遇到下面的问题：**

{% highlight html linenos %}
    <html>
		<head>
	  		<style type="text/css">
			    .top{
			        width: 200px;
			        height: 300px;
			        background:gray;
			    }
			    .one{
			        width: 100px;
			        height: 100px;
			        background: red;
			        margin-top: 20px;
			        margin-bottom: 10px;
			    }
			    .two{
			        width: 100px;
			        height: 100px;
			        background: blue;
			        margin-top: 20px;
			    }
	  		</style>
		</head>
		<body>
		  <div class="top">
		    <div class="one">I'm the first!</div>
		    <div class="two">I'm the second!</div>
		  </div>
		</body>
	</html>
{% endhighlight %}

**显示效果：**

<div align="center"><img src="{{ "/images/cssMarginTop/1.png" | prepend: site.baseurl }}" width="400" /></div>


**这个问题发生的原因是根据规范，一个盒子如果没有上补白(padding-top)和上边框(border-top)，那么这个盒子的上边距会和其内部文档流中的第一个子元素的上边距重叠。**

**两个相邻的margin之间如果没有明确的界限（padding、border），则会发生重叠，重叠后相应的margin为较大的那个**

上图中第二个div的margin-top与第一个div的margin-bottom之间没有界限，所以one的margin-bottom与two的margin-top重叠了，它们之间只有一个margin的距离，而父标签top的margin-top与one的margin-top之间也没有界限，所以两者也会重合，因为top的margin-top为0小于one的margin-top值，所以重叠后的值为后者。

<br>
解决方法有以下几种：

* 修改父元素的高度，增加padding-top样式模拟（padding-top：1px；添加界限） 
* 为父元素添加overflow：hidden；样式即可（让父元素成为BFC，内部布局不受外部影响） 
* 为父元素或者子元素声明浮动（浮动元素的margin垂直方向不叠加） 
* 为父元素添加border（添加界限） 
* 为父元素或者子元素声明绝对定位（BFC）