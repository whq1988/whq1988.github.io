---
layout: post
title: "负数转二进制"
date: 2018-06-22 11:37:00 +0800
categories: algorithm
description: "负数转二进制"
tag: algorithm
---

### 以-5为例

**1.把5转化为二进制字节形式。得到101，然后补零，得到原码**（*一个整数按照绝对值大小转换成的二进制数，是为原码。*）

<div align="center"><img src="{{ "/images/algorithmMinusToBinary/1.png" | prepend: site.baseurl }}" width="300" /></div>

**2.对原码取反（0的变成1，1的变成0。），得到反码**

<div align="center"><img src="{{ "/images/algorithmMinusToBinary/2.png" | prepend: site.baseurl }}" width="300" /></div>

**3.把反码加1，获得补码**（*反码加一叫补码。*）

<div align="center"><img src="{{ "/images/algorithmMinusToBinary/3.png" | prepend: site.baseurl }}" width="300" /></div> 

**4.补码就是负数在计算机中的二进制表示方法。**

那么，11111011表示8位的-5，如果要表示16位的-5 ，在左边添上8个1即可。

<div align="center"><img src="{{ "/images/algorithmMinusToBinary/4.png" | prepend: site.baseurl }}" width="300" /></div> 

<br>
***如果把负数的二进制数转为十进制数，反着计算一遍（减1->取反->转为10进制->前面加负号），如下，最后结果为-13：***

<div align="center"><img src="{{ "/images/algorithmMinusToBinary/5.png" | prepend: site.baseurl }}" width="300" /></div>