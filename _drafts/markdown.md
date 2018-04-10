---
layout: post
title: "Markdown语法说明"
date: 2018-04-09 10:26:45 +0800
categories: markdown
description: "Markdown语法说明"
tag: markdown
---

### 区块元素  

#### 测试 
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.

*   Red
*   Green
*   Blue

1.  Bird
2.  McHale
3.  Parish

*   A list item with a blockquote:

    > This is a blockquote
    > inside a list item.

Here is an example of AppleScript:

    tell application "Foo"
        beep
    end tell


*single asterisks*

_single underscores_

**double asterisks**

__double underscores__


Use the `printf()` function.


{% highlight javascript %}
    var o = {
        a:10,
        b:{
            // a:12,
            fn:function(){
                console.log(this.a); //undefined
            }
        }
    }
    o.b.fn();
{% endhighlight %}