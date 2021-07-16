---
layout: post
title: "如何方便的在Markdown中插入数学公式（不用记那么多语法！！）"
date: 2018-04-09 10:26:45 +0800
categories: blog
description: "如何方便的在Markdown中插入数学公式（不用记那么多语法！！）"
tag: 博客使用教程
---

在markdown插入数学公式,可以利用”在线latex数学公式”来帮助生成数学公式代码。

比如三个求和公式的键入步骤(其他任何公式同理)：

![求和公式]({{ "/images/markdownMathematicalFormula/1.png" | prepend: site.baseurl }})

1.进入[在线latex数学公式](http://latex.codecogs.com/eqneditor/editor.php)来生成我们需要的公式代码。

![代码生成]({{ "/images/markdownMathematicalFormula/2.png" | prepend: site.baseurl }}){:width="100%" height="auto"}

2.如果是插入<u>行内</u>公式那就输入：<textarea>${X}$</textarea>，其中的‘{X}’就是我们在步骤1中得到的公式代码，把他复制进去就完成了。

&nbsp;如果是插入<u>块级</u>公式那就输入：<textarea>$${X}$$</textarea>，其中的‘{X}’就是我们在步骤1中得到的公式代码，把他复制进去就完成了。

3.在Typora中插入行内公式，需在 ‘文件 -> 偏好设置’ 中，勾选‘内联公式’选项(可能需要重启才能生效)，然后直接在相应位置输入两个 $ ，再在中间输入公式。

&nbsp;在Typora中插入行间公式，点击 ’段落 -> 公式块‘，即可弹出公式编辑栏

4.Jekyll模板不支持LaTeX公式，在Typora里面编辑数学公式后，在基于Jekyll模板的GitHub page上是不能渲染的。有一种解决方法，那就是引入外部的 js脚本，如下：

{% highlight javascript linenos %}
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
{% endhighlight %}

*引入后，‘行内公式’可正常渲染，但是‘块级样式’也会显示为‘行内样式’，经测试，使用<textarea>\$\${X}\$\$</textarea>即可正常展示*

行内公式试验效果：$\sum_{i=1}^{n}\sum_{j=1}^{i}\sum_{k=1}^{j}$

块级公式试验效果：
\$\$
\sum_{i=1}^{n}\sum_{j=1}^{i}\sum_{k=1}^{j}
\$\$