---
layout: post
title: "二进制、八进制、十进制与十六进制的概念以及它们之间的转换"
date: 2018-06-14 13:49:00 +0800
categories: other
description: "二进制、八进制、十进制与十六进制的概念以及它们之间的转换"
tag: other
---

### 概述
在编程中，有时候会用到进制数来作为操作数据，所以下面就讲下它们的概念以及它们之间的转换。

进制也就是进制位，对于接触过电脑的人来说应该都不陌生，我们常用的进制包括：二进制、八进制、十进制与十六进制，它们之间区别在于数运算时是逢几进一位。比如二进制是逢2进一位，十进制也就是我们常用的0-9是逢10进一位。

**二进制（英文：Binary）：**是计算技术中广泛采用的一种数制。二进制数据是用0和1两个数码来表示的数。它的基数为2，进位规则是“逢二进一”，借位规则是“借一当二”。

**八进制（英文：Octal，缩写OCT或O）：**一种以8为基数的计数法，采用0，1，2，3，4，5，6，7八个数字，逢八进1。一些编程语言中常常以数字0开始表明该数字是八进制。

**十进制（英文：Decimal）：**就是日常生活中用的最多的，如：1，2，3，……100，200，300……。十进制基于位进制和十进位两条原则，即所有的数字都用10个基本的符号表示，满十进一，同时同一个符号在不同位置上所表示的数值不同，符号的位置非常重要。基本符号是0到9十个数字。

**十六进制（英文：Hexadecimal）：**是计算机中数据的一种表示方法。同我们日常生活中的表示法不一样。它由0-9，A-F组成，字母不区分大小写。与10进制的对应关系是：0-9对应0-9；A-F对应10-15；N进制的数可以用0~(N-1)的数表示，超过9的用字母A-F。

*它们的关系如下，都是可以相互转换的:*   
<div align="center"><img src="{{ "/images/otherBinary/92525546_1.jpg" | prepend: site.baseurl }}" width="300" /></div>


<br>
### 下面就来看看它们之间的转换关系：

**十进制转二进制：**十进制数除2取余法，即十进制数除2，余数为权位上的数，得到的商值继续除2，依此步骤继续向下运算直到商为0为止。  

例如：把十进制数 150 转换为 二进制数，如下：  
<div align="center"><img src="{{ "/images/otherBinary/92525546_2.jpg" | prepend: site.baseurl }}" width="500" /></div>

**二进制转十进制：**把二进制数按权展开、相加即得十进制数。  
<div align="center"><img src="{{ "/images/otherBinary/92525546_3.jpg" | prepend: site.baseurl }}" width="500" /></div>

**二进制转八进制：**3位二进制数按权展开相加得到1位八进制数。（*注意事项，3位二进制转成八进制是从右到左开始转换，不足时补0*）。  
<div align="center"><img src="{{ "/images/otherBinary/92525546_4.jpg" | prepend: site.baseurl }}" width="500" /></div>

**八进制转成二进制：**八进制数通过除2取余法，得到二进制数，对每个八进制为3个二进制，不足时在最左边补零。  
<div align="center"><img src="{{ "/images/otherBinary/92525546_5.jpg" | prepend: site.baseurl }}" width="500" /></div>

**二进制转十六进制：**与二进制转八进制方法近似，八进制是取三合一，十六进制是取四合一。（*注意事项，4位二进制转成十六进制是从右到左开始转换，不足时补0*）。  
<div align="center"><img src="{{ "/images/otherBinary/92525546_6.jpg" | prepend: site.baseurl }}" width="500" /></div>

**十六进制转二进制：**十六进制数通过除2取余法，得到二进制数，对每个十六进制为4个二进制，不足时在最左边补零。  
<div align="center"><img src="{{ "/images/otherBinary/92525546_7.jpg" | prepend: site.baseurl }}" width="500" /></div>

**十进制转八进制或者十六进制：**有两种方法  

方法1：间接法，把十进制转成二进制，然后再由二进制转成八进制或者十六进制。这里不再做图片用法解释。

方法2：直接法—把十进制转八进制或者十六进制按照除8或者16取余，直到商为0为止。（*注意事项，最后读数时候，从最后一个余数起，一直到最前面的一个余数*）如下图：  
<div align="center"><img src="{{ "/images/otherBinary/92525546_10.jpg" | prepend: site.baseurl }}" width="500" /></div>

**八进制或者十六进制转成十进制：**把八进制、十六进制数按权展开、相加即得十进制数。  
<div align="center"><img src="{{ "/images/otherBinary/92525546_11.jpg" | prepend: site.baseurl }}" width="500" /></div>

**八进制转十六进制：**将八进制转换为二进制，然后再将二进制转换为十六进制，小数点位置不变。  
<div align="center"><img src="{{ "/images/otherBinary/92525546_12.jpg" | prepend: site.baseurl }}" width="300" /></div>

**十六进制转八进制：**将十六进制转换为二进制，然后再将二进制转换为八进制，小数点位置不变。  
<div align="center"><img src="{{ "/images/otherBinary/92525546_13.jpg" | prepend: site.baseurl }}" width="300" /></div>
  
  
*转自：[二进制、八进制、十进制与十六进制的概念以及它们之间的转换](http://www.360doc.com/content/17/0227/22/8067272_632545159.shtml)*



