---
layout: post
title: "Html5 根据多个点使用canvas贝赛尔曲线画一条平滑的曲线"
date: 2018-07-02 16:38:00 +0800
categories: Html5
description: "Html5 根据多个点使用canvas贝赛尔曲线画一条平滑的曲线"
tag: Html5
---

> 众所周知想用canvas画一条曲线我们可以使用这些函数：
> > *二次曲线：quadraticCurveTo(cp1x, cp1y, x, y)*  
> > *贝塞尔曲线：bezierCurveTo(cp1x, cp1y, cp2x, cp2y, x, y)*  
> > *画圆弧：arcTo(x1,y1,x2,y2,radius)*  
> 但是如果一组点给你，怎么通过这些已知点画一条平滑的曲线呢？使用二次曲线，或是圆弧？恐怕这些都没法满足曲线多变的需求，**唯一的方法就是一段贝塞尔曲线连着一段贝塞尔曲线,而其中的难点就是如何确定控制点**。

百度一下贝塞尔曲线控制点计算方法，在搜索结果第一条有一篇百度文库的文章《[怎样确定 Bezier 曲线的控制点]({{ "/images/html5Bezier/怎样确定 Bezier 曲线的控制点.doc" | prepend: site.baseurl }})》，通过浏览全文找到了计算控制点的公式，以下是核心结论:

<div align="center"><img src="{{ "/images/html5Bezier/1.png" | prepend: site.baseurl }}" width="600" /></div>

哎妈，是不是看了之后头都大了，反正公式已经给出来了，就让我们用行数去实现这个Ai和Bi点的计算吧，忘了说一句a、b可以为任意正数，等我们完成了我们的demo可以测试一下不同的a、b值对我们的曲线会造成什么影响

{% highlight javascript linenos %}
    /**
	 *根据已知点获取第i个控制点的坐标
	 *param ps	已知曲线将经过的坐标点
	 *param i	坐标点的个数
	 *param a,b	可以自定义的正数
	 */
	function getCtrlPoint(ps, i, a, b){
	    if(!a||!b){
		a=0.25;
		b=0.25;
	    }
	    var pAx = ps[i].x + (ps[i+1].x-ps[i-1].x)*a;
	    var pAy = ps[i].y + (ps[i+1].y-ps[i-1].y)*a;
	    var pBx = ps[i+1].x - (ps[i+2].x-ps[i].x)*b;
	    var pBy = ps[i+1].y - (ps[i+2].y-ps[i].y)*b;
	    return {
	    	pA:{x:pAx,y:pAy},
	    	pB:{x:pBx,y:pBy}
	    }
	}
{% endhighlight %}

但是对于最初一段和最后一段，是不能用上述方法的，因为(x<sub>-1</sub>,y<sub>-1</sub>)和(x<sub>n+1</sub>,y<sub>n+1</sub>)，这两个点其实是不存在的，文章中提到了两种解决方法，这里只看比较简单的第一种：**用(x<sub>0</sub>,y<sub>0</sub>)的值作为(x<sub>-1</sub>,y<sub>-1</sub>)的值，用(x<sub>n</sub>,y<sub>n</sub>)的值作为(x<sub>n+1</sub>,y<sub>n+1</sub>)的值**。

改进后的函数：

{% highlight javascript linenos %}
    /**
	 *根据已知点获取第i个控制点的坐标
	 *param ps	已知曲线将经过的坐标点
	 *param i	第i个坐标点
	 *param a,b	可以自定义的正数
	 */
	function getCtrlPoint(ps, i, a, b){
		if(!a||!b){
			a=0.25;
			b=0.25;
		}
		//处理两种极端情形
		if(ips.length-3){
			var last=ps.length-1
			var pBx = ps[last].x - (ps[last].x-ps[last-1].x)*b;
			var pBy = ps[last].y - (ps[last].y-ps[last-1].y)*b;
		}else{
			var pBx = ps[i+1].x - (ps[i+2].x-ps[i].x)*b;
			var pBy = ps[i+1].y - (ps[i+2].y-ps[i].y)*b;
		}
		return {
			pA:{x:pAx,y:pAy},
			pB:{x:pBx,y:pBy}
		}
	}
{% endhighlight %}

效果如下：

<div align="center"><img src="{{ "/images/html5Bezier/2.png" | prepend: site.baseurl }}" width="600" /></div>

加入辅助点说明：

<div align="center"><img src="{{ "/images/html5Bezier/3.png" | prepend: site.baseurl }}" width="600" /></div>

红色是通过点与控制点的连线，绿色是控制点与控制点之间的连线，蓝色的是直线连接各点画的线。

<br>
具体实现过程见 [demo.zip]({{ "/images/html5Bezier/demo.zip" | prepend: site.baseurl }})



<br><br>
转自:[《根据多个点使用canvas贝赛尔曲线画一条平滑的曲线》](http://www.zheng-hang.com/?id=43)