---
layout: post
title: "js Object方法笔记"
date: 2018-04-09 10:26:45 +0800
categories: js
description: "js Object方法笔记"
tag: js
---

## 基础篇  

### 一、js计算object的长度
* **方法1**：通过Object.keys()获取对象可枚举属性所组成的数组，并通过length获取对象长度
{% highlight javascript linenos %}
    var obj = {"c1":1, "c2":2};
    var arr = Object.keys(obj);
    var len = arr.length;
    console.log(len);//结果为2 
{% endhighlight %}
* **方法2**：通过for in 遍历对象，并通过hasOwnProperty判断是否是对象自身可枚举的属性
{% highlight javascript linenos %}
    var obj = {"c1":1, "c2":2};
    function countProperties(obj){
        for(var property in obj){
            if(Object.prototype.hasOwnProperty.call(obj,property){
                count++;
            })
        }
        return count;
    }
    var len = obj.length;
    console.log(len);  //结果为2
{% endhighlight %}

<br>
### 二、js判断一个对象是否为空 
{% highlight javascript linenos %}
    var c = {};
    if(JSON.stringify(c) == "{}"){ 
        console.log('对象为空');
    }
{% endhighlight %}

<br>
### 三、js判断object里面是否包含某一字段
{% highlight javascript linenos %}
    var obj = { 
        name:"王中",
        age:"26岁",
    };
    if (obj.hasOwnProperty("age")){
        console.log("有age项")；
    }
{% endhighlight %}

<br>
### 四、js里面动态地为对象添加属性
* **通用方法**：在属性名固定或不固定的情况下都可使用，如下
{% highlight javascript linenos %}
    var a = "newkey";
    obj[a] = value;
{% endhighlight %}
* **属性名固定的情况下**：可直接 obj.newKey=value; 也可以 obj[newKey]=value;

<br><br>
## 进阶篇  

### 一、js关于对象键值为数字型时输出的对象自动排序问题的解决方法
* **问题描述**：对象键值为数字型时输出的对象自动排序问题，如下：
{% highlight javascript linenos %}
    var objs = {
        "5" : {},
        "2" : {},
        "1" : {}
    }
    console.log(objs);
    输出的对象是：
    {
        "1":{},
        "2":{},
        "5":{}
    }
    会自动按照键值大小排序，这样容易影响数据显示的顺序问题
{% endhighlight %}
* **解决方法**：将对象的键值转换为字符串类型，可以在前面加个字符串（如\'id_\'），这样就不会自动排序了，如下：
{% highlight javascript linenos %}
    var objs = {
        "id_5" : {},
        "id_2" : {},
        "id_1" : {}
    }
    console.log(objs);
    输出的对象是：
    {
        "id_5" : {},
        "id_2" : {},
        "id_1" : {}
    }
{% endhighlight %}

<br>
### 四、js对象合并
需求：设有对象 o1、o2、o3,需要得到对象 o4，如下：
{% highlight javascript linenos %}
    var o1 = { a:'a' }, o2 = { b:'b' }, o3 = { c:'c' }, , o4 = {};
    // 则
    var o4 = { a:'a', b:'b', c:'c' }
{% endhighlight %}
* **方法1**：遍历赋值法：
{% highlight javascript linenos %}
    var o1 = { a:'a' }, o2 = { b:'b' }, o3 = { c:'c' }, o4 = {};
    // 把n中的元素添加到o，然后返回o
    var extend = function(o,n){
        for (var p in n){
            if(n.hasOwnProperty(p) && (!o.hasOwnProperty(p) )){
                o[p] = n[p];
            }   
        }
        return o;
    };  
    // 挨个调用
    o4 = extend(o4, o1);
    o4 = extend(o4, o2);
    o4 = extend(o4, o3);
    console.log(o4);  //输出{a: "a", b: "b", c: "c"}
{% endhighlight %}
* **方法2**：使用 Object.assign()：
{% highlight javascript linenos %}
    var o1 = { a:'a' }, o2 = { b:'b' }, o3 = { c:'c' }, o4 = {};
    o4 = Object.assign(o1, o2, o3);
    console.log(o4);  // {a: "a", b: "b", c: "c"}
    console.log(o1);  // {a: "a", b: "b", c: "c"}, 注意目标对象自身也会改变。
    console.log(o2);  // {b: "b"}
    console.log(o3);  // {c: "c"}
{% endhighlight %}
* **方法3**：使用JQuery的extend方法