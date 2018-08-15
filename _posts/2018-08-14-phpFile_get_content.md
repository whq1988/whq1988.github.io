---
layout: post
title: "file_get_contents设置超时处理方法"
date: 2018-08-14 14:50:00 +0800
categories: php
description: "file_get_contents设置超时处理方法"
tag: php
---

> 在做官网的时候，有一个需求，是根据用户ip获取到地址，然后根据地址判断显示中文版还是英文版。

> 以前借助的是新浪的ip查询服务，使用php自带的file_get_content方法请求新浪的API，最开始挺好用，但是直到有一天这个API不好用了，然后官网就无限白屏。。。（太相信大厂商了，风险意识有待提高）

> 后来经查询，file_get_content不仅可以在context中增加诸如'method'、'header'这样的参数，还可以增加超时时间

**调整后的代码(API改为了淘宝的)如下：**

{% highlight php linenos %}
    $opts = array(   
	  'http'=>array(   
	    'method'=>"GET",   
	    'timeout'=>10,	//设置超时时间，单位是秒  
	   )   
	); 
	$res = @file_get_contents('http://ip.taobao.com/service/getIpInfo.php?ip='.$ip, false, stream_context_create($opts));
	if($res === false){
		//说明失败了，进行其他处理
	}else{
		$result = json_decode($res, true);
		if($result['code'] == 0){
			if($result['data']['country'] == '中国'){
				$lan = 'cn';
			}else{
				$lan = 'en';
			}
		}
	}
{% endhighlight %}

<br>
**同样的，'method'=>"POST"方式也可设置超时时间，代码如下：**

{% highlight php linenos %}
    function Post($url, $post = null){   
	    $context = array ();   
	    if (is_array ( $post )) {   
	        ksort ( $post );   
	        $context ['http'] = array (   
	            'timeout' => 60,    
	            'method' => 'POST',    
	            'content' => http_build_query( $post, '', '&' )   //把数组改造成URL-encode之后的请求字符串
	         );   
	    }   
	    return file_get_contents ( $url, false, stream_context_create($context) );   
	}   
	$data = array (   
	    'name' => 'test',   
	    'email' => 'admin@admin.com',   
	    'submit' => 'submit',   
	);   
	echo Post ( 'http://www.jb51.net', $data );  
{% endhighlight %}


