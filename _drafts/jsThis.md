---
layout: post
title: "js This详解"
date: 2018-04-13 10:54:00 +0800
categories: js
description: "js This详解"
tag: js
---

## 明确this的指向

### this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，实际上this的最终指向的是那个调用它的对象
{: .c1 #idp1 .c2 title="title"}
* **实例1**：
{% highlight javascript linenos %}
    function a(){
        var user = "Nash";
        console.log(this.user); //undefined
        console.log(this); //Window
    }
    a();  //这里的函数a实际是被Window对象所点出来的,用window.a()效果是一样的
{% endhighlight %}
* **实例2**：
{% highlight javascript linenos %}
    var o = {
        user:"Nash",
        fn:function(){
            console.log(this.user);  //Nash
        }
    }
    o.fn();  //这里fn()中的this指向对象o
{% endhighlight %}
* **实例3**：
{% highlight javascript linenos %}
    var o = {
        a:10,
        b:{
            a:12,
            fn:function(){
                console.log(this.a); //这里是12，而非10
            }
        }
    }
    o.b.fn();  //这里fn()中的this指向对象b
{% endhighlight %}
* **实例4**：
{% highlight javascript linenos %}
    var o = {
        a:10,
        b:{
            a:12,
            fn:function(){
                console.log(this.a); //undefined
                console.log(this); //window
            }
        }
    }
    var j = o.b.fn;
    j();  //这里的this指向的是window，和window.j()一样，要看它执行的时候是谁调用的
{% endhighlight %}


<br><br>
## 改变this的指向  

### 一、构造函数版this：
{% highlight javascript linenos %}
    function Fn(){
        this.user = "Nash";
    }
    var a = new Fn();
    console.log(a)  //Fn {user: "Nash"}
    console.log(a.user);  //Nash
{% endhighlight %}
* **当Fn中有return,并且返回为对象类型（null除外）时**
{% highlight javascript linenos %}
    function fn(){  
        this.user = '追梦子';  
        return {};  
    }
    var a = new fn();
    console.log(a)  //{}
    console.log(a.user);  //undefined
{% endhighlight %}
{% highlight javascript linenos %}
    function fn()  {  
        this.user = 'Nash1';  
        return {user: 'Nash2'};
    }
    var a = new fn;  
    console.log(a);  //{user: "Nash2"}
    console.log(a.user);  //Nash2
{% endhighlight %}
再看一个：
{% highlight javascript linenos %}
    function fn(){  
        this.user = '追梦子';  
        return function(){};
    }
    var a = new fn;  
    console.log(a);  //ƒ (){}
    console.log(a.user);  //undefined
{% endhighlight %}
* **当Fn中有return,并且返回不是对象时**：
{% highlight javascript linenos %}
    function fn(){  
        this.user = 'Nash';  
        return 1;
    }
    var a = new fn;  
    console.log(a);  //fn {user: "Nash"}
    console.log(a.user);  //Nash
{% endhighlight %}
{% highlight javascript linenos %}
    function fn(){  
        this.user = 'Nash';  
        return undefined;
    }
    var a = new fn;  
    console.log(a);  //fn {user: "Nash"}
    console.log(a.user);  //Nash
{% endhighlight %}
* **虽然null也是对象类型**：
{% highlight javascript linenos %}
    console.log(typeof(null))  //object
    function fn(){  
        this.user = 'Nash';  
        return null;
    }
    var a = new fn;  
    console.log(a);  //fn {user: "Nash"}
    console.log(a.user);  //Nash
{% endhighlight %}


## kramdown和markdown较大的差异比较

kramdown是markdown的超集,在Jekyll中支持, 可以用于Github搭建博客. 和Jekyll一样, 使用Ruby作为核心语言. 由于Maruku不再更新, Github推荐使用kramdown作为markdown解析. kramdown作为markdown解析器号称速度快, 比PHP markdown和Maruku都要快几倍.

kramdown有很多一般markdown所没有的语法特点, 包括和GFM也有差异.另外也可以很方便地作为文件转换使用. 这里只讨论其markdown重要特色, 不考察其作为解析器的用法.