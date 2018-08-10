---
layout: post
title: "PHP 命名空间与自动加载机制介绍，及Psr-4规范介绍"
date: 2018-08-10 17:32:00 +0800
categories: PHP
description: "PHP 命名空间与自动加载机制介绍，及Psr-4规范介绍"
tag: PHP
---

> * include 和 require 是PHP中引入文件的两个基本方法。在小规模开发中直接使用 include 和 require 没哟什么不妥，但在大型项目中会造成大量的 include 和 require 堆积。这样的代码既不优雅，执行效率也很低，而且维护起来也相当困难。
> * include 和 require 功能是一样的，它们的不同在于 **include 出错时只会产生警告，而 require 会抛出错误终止脚本**。  
> * include_once 和 include 唯一的区别在于 **include_once 会检查文件是否已经引入，如果是则不会重复引入**。

> * 为了解决这个问题，部分框架会给出一个引入文件的配置清单，在对象初始化的时候把需要的文件引入。但这只是让代码变得更简洁了一些，引入的效果仍然是差强人意。PHP5 之后，随着 PHP 面向对象支持的完善，\_\_autoload 函数才真正使得自动加载成为可能。


### 一、自动加载

实现自动加载最简单的方式就是使用 **\_\_autoload 魔术方法。当需要使用的类没有被引入时，这个函数会在PHP报错前被触发，未定义的类名会被当作参数传入。至于函数具体的逻辑，这需要用户自己去实现**。

首先创建一个 autoload.php 来做一个简单的测试：

{% highlight php linenos %}
    // 类未定义时，系统自动调用
	function __autoload($class)
	{
	    /* 具体处理逻辑 */
	    echo $class;// 简单的输出未定义的类名
	}

	new HelloWorld();

	/**
	 * 输出 HelloWorld 与报错信息
	 * Fatal error: Class 'HelloWorld' not found
	 */
{% endhighlight %}

通过这个简单的例子可以发现，在类的实例化过程中，系统所做的工作大致是这样的：

{% highlight php linenos %}
    /* 模拟系统实例化过程 */
	function instance($class)
	{
	    // 如果类存在则返回其实例
	    if (class_exists($class, false)) {
	        return new $class();
	    }
	    // 查看 autoload 函数是否被用户定义
	    if (function_exists('__autoload')) {
	        __autoload($class); // 最后一次引入的机会
	    }
	    // 再次检查类是否存在
	    if (class_exists($class, false)) {
	        return new $class();
	    } else { // 系统：我实在没辙了
	        throw new Exception('Class Not Found');
	    }
	}

{% endhighlight %}

明白了 __autoload 函数的工作原理之后，那就让我们来用它去实现自动加载。

首先创建一个类文件（建议文件名与类名一致），代码如下：

{% highlight php linenos %}
	class [ClassName] 
	{
	    // 对象实例化时输出当前类名
	    function __construct()
	    {
	        echo '<h1>' . __CLASS__ . '</h1>';
	    }
	}
{% endhighlight %}

（我这里创建了一个 HelloWorld 类用作演示）接下来我们就要定义 \_\_autoload 的具体逻辑，使它能够实现自动加载：

{% highlight php linenos %}
	function __autoload($class)
	{
	    // 根据类名确定文件名
	    $file = $class . '.php';

	    if (file_exists($file)) {
	        include $file; // 引入PHP文件
	    }
	}

	new HelloWorld();

	/**
	 * 输出 <h1>HelloWorld</h1>
	 */
{% endhighlight %}

<br>

### 二、命名空间

其实命名空间并不是什么新生事物，很多语言（例如C++）早都支持这个特性了。只不过 PHP 起步比较晚，直到 PHP 5.3 之后才支持。

命名空间简而言之就是一种标识，它的主要目的是解决命名冲突的问题。

就像在日常生活中，有很多姓名相同的人，如何区分这些人呢？那就需要加上一些额外的标识。

把工作单位当成标识似乎不错，这样就不用担心 “撞名” 的尴尬了。

{% highlight php linenos %}
	//这里我们来做一个小任务，去介绍百度的CEO李彦宏：
	namespace 百度;

	class 李彦宏
	{
	    function __construct()
	    {
	        echo '百度创始人';
	    }
	}
	//↑ 这就是李彦宏的基本资料了，namespace 是他的单位标识，class 是他的姓名。
{% endhighlight %}

*** 命名空间通过关键字 namespace 来声明。如果一个文件中包含命名空间，它必须在其它所有代码之前声明命名空间。**

*** 在当前命名空间没有声明的情况下，限定类名和完全限定类名是等价的。因为如果不指定空间，则默认为全局（\）。**
{% highlight php linenos %}
	namespace 谷歌;

	new 百度\李彦宏(); // 限定类名，谷歌\百度\李彦宏（实际结果）
	new \百度\李彦宏(); // 完全限定类名，百度\李彦宏（实际结果）
	// ↑ 如果你在谷歌公司向他们的员工介绍李彦宏，一定要指明是 "百度公司的李彦宏"。否则他会认为百度是谷歌的一个部门，而李彦宏只是其中的一位员工而已。
	// 这个例子展示了在命名空间下，使用限定类名和完全限定类名的区别。（完全限定类名 = 当前命名空间 + 限定类名）
{% endhighlight %}

以下展示了如何导入命名空间和设置别名

{% highlight php linenos %}
	/* 导入命名空间 */
	use 百度\李彦宏;
	new 李彦宏(); // 百度\李彦宏（实际结果）

	/* 设置别名 */
	use 百度\李彦宏 AS CEO;
	new CEO(); // 百度\李彦宏（实际结果）

	/* 任何情况 */
	new \百度\李彦宏();// 百度\李彦宏（实际结果）
	// ↑ 第一种情况是别人已经认识李彦宏了，你只需要直接说名字，他就能知道你指的是谁。第二种情况是李彦宏就是他们的CEO，你直接说CEO，他可以立刻反应过来。
{% endhighlight %}

**归纳：**

*** 使用命名空间只是让类名有了前缀，不容易发生冲突，系统仍然不会进行自动导入。**

*** 如果不引入文件，系统会在抛出 "Class Not Found" 错误之前触发 __autoload 函数，并将限定类名传入作为参数。**

*** 所以上面的例子都是基于你已经将相关文件手动引入的情况下实现的，否则系统会抛出 " Class '百度\李彦宏' not found"。**

<br>

### 三、spl_autoload实现自动加载

*** 接下来让我们要在含有命名空间的情况下去实现自动加载。这里我们使用 spl_autoload_register() 函数来实现，这需要你的 PHP 版本号大于 5.12。**

*** spl_autoload_register 函数的功能就是把传入的函数（参数可以为回调函数或函数名称形式）注册到 SPL __autoload 函数队列中，并移除系统默认的 __autoload() 函数。**

*** 一旦调用 spl_autoload_register() 函数，当调用未定义类时，系统就会按顺序调用注册到 spl_autoload_register() 函数的所有函数，而不是自动调用 __autoload() 函数。**

现在，我们来创建一个 Linux 类，它使用 os 作为它的命名空间（建议文件名与类名保持一致）：
{% highlight php linenos %}
	namespace os; // 命名空间

	class Linux // 类名
	{
	    function __construct()
	    {
	        echo '<h1>' . __CLASS__ . '</h1>';
	    }
	}
{% endhighlight %}

接着，在同一个目录下新建一个 PHP 文件，使用 spl_autoload_register 以函数回调的方式实现自动加载：

{% highlight php linenos %}
	spl_autoload_register(function ($class) { // class = os\Linux

	    /* 限定类名路径映射 */
	    $class_map = array(
	        // 限定类名 => 文件路径
	        'os\\Linux' => './Linux.php',
	    );

	    /* 根据类名确定文件名 */
	    $file = $class_map[$class];

	    /* 引入相关文件 */
	    if (file_exists($file)) {
	        include $file;
	    }
	});

	new \os\Linux();
{% endhighlight %}

**这里我们使用了一个数组去保存类名与文件路径的关系，这样当类名传入时，自动加载器就知道该引入哪个文件去加载这个类了。**

**但是一旦文件多起来的话，映射数组会变得很长，这样的话维护起来会相当麻烦。如果命名能遵守统一的约定，就可以让自动加载器自动解析判断类文件所在的路径。接下来要介绍的PSR-4 就是一种被广泛采用的约定方式。**

<br>

### 四、PSR-4规范

PSR-4 是关于由文件路径自动载入对应类的相关规范，规范规定了一个完全限定类名需要具有以下结构：

\\<顶级命名空间>(\<子命名空间>)*\<类名>

*如果继续拿上面的例子打比方的话，顶级命名空间相当于公司，子命名空间相当于职位，类名相当于人名。那么李彦宏标准的称呼为 "百度公司 CEO 李彦宏"。*

**PSR-4 规范中必须要有一个顶级命名空间，它的意义在于表示某一个特殊的目录（文件基目录）。子命名空间代表的是类文件相对于文件基目录的这一段路径（相对路径），类名则与文件名保持一致（注意大小写的区别）。**



<br><br>
转自:[《PHP 命名空间与自动加载机制介绍》](https://www.cnblogs.com/woider/p/6443854.html)