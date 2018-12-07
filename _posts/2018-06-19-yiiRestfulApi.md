---
layout: post
title: "yii2 配置 RESTful api"
date: 2018-06-19 18:14:00 +0800
categories: yii
description: "yii2 配置 RESTful api"
tag: yii
---

### 1.建立单独的应用程序 

在WEB前端（frontend）和后端（backend）的同级目录，新建一个文件夹，命名api,其目录结构如下所示

{% highlight php %}
	├─assets
	│      AppAsset.php
	├─config
	│      bootstrap.php
	│      main-local.php
	│      main.php
	│      params-local.php
	│      params.php
	├─runtime
	└─web
	    │ index.php
	    ├─assets
	    └─css
{% endhighlight %}

可以看出其目录结构基本上同backend没有其他差异，因为我们就是拷贝backend项目，只是做了部分优化。此时再做两个修改：

* 1.把config/main.php中的backend全部替换为api
* 2.修改common\config\bootstrap.php文件，对新建的应用增加alias别名：Yii::setAlias('@api', dirname(dirname(\_\_DIR\_\_)) . '/api');


### 2.为新建的api应用程序美化路由

首先保证你的web服务器开启rewrite规则，细节我们就不说了，不过这是前提。接着配置api/config/main.php文件

{% highlight php %}
	'components' => [
	    // other config
	    'urlManager' => [
	        'enablePrettyUrl' => true,
	        'showScriptName' => false,
	        'enableStrictParsing' =>true,
	        'rules' => [],
	    ]
	],
{% endhighlight %}

最后只需要在应用入口同级增加.htaccess文件就好，我们以apache为例

{% highlight php %}
	Options +FollowSymLinks
	IndexIgnore */*

	RewriteEngine on

	# if a directory or a file exists, use it directly
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteCond %{REQUEST_FILENAME} !-d

	# otherwise forward it to index.php
	RewriteRule . index.php

	RewriteRule \.svn\/  /404.html
	RewriteRule \.git\/  /404.html
{% endhighlight %}


### 3.利用gii生成测试modules

用了便于演示说明，我们新建一张数据表goods表，并向其中插入几条数据。

{% highlight php %}
	CREATE TABLE `yii_goods` (
	  `id` int(11) NOT NULL AUTO_INCREMENT,
	  `name` varchar(100) NOT NULL DEFAULT '',
	  PRIMARY KEY (`id`)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;

	INSERT INTO `yii_goods` VALUES ('1', '11111');
	INSERT INTO `yii_goods` VALUES ('2', '22222');
	INSERT INTO `yii_goods` VALUES ('3', '333');
	INSERT INTO `yii_goods` VALUES ('4', '444');
	INSERT INTO `yii_goods` VALUES ('5', '555');
{% endhighlight %}

接着我们先利用gii生成modules后，再利用gii模块，按照下图中生成goods信息

(1)生成module，然后按照下方的提示，进行配置:
<div align="center"><img src="{{ "/images/yiiRestfulApi/1.png" | prepend: site.baseurl }}" width="500" /></div>

(2)生成Controller:
<div align="center"><img src="{{ "/images/yiiRestfulApi/2.png" | prepend: site.baseurl }}" width="500" /></div>

(3)生成Models:
<div align="center"><img src="{{ "/images/yiiRestfulApi/3.png" | prepend: site.baseurl }}" width="500" /></div>

现在，我们的api目录结构应该多个下面这几个目录：

{% highlight php %}
	├─models
	│      Goods.php
	│
	├─Module.php
	│
	├─controllers
	│      DefaultController.php
	│      GoodsController.php
	│
	├─views
	│      default
	│      	   index.php
{% endhighlight %}


### 4.重新配置控制器

为了实现restful风格的api,在yii2中，我们需要对控制器进行一下改写

{% highlight php %}
	<?php

		namespace api\controllers;

		use yii\rest\ActiveController;

		class GoodsController extends ActiveController
		{
			public $modelClass = 'api\models\Goods';
		}
{% endhighlight %}


### 5.为Goods配置Url规则

{% highlight php %}
	'rules' => [
        [
            'class' => 'yii\rest\UrlRule',
            'controller' => ['goods']
        ],
    ],
{% endhighlight %}


### 6.模拟请求操作

经过上面几个步骤，到此我们已经为goods成功创建了满足restful风格的api了。