---
layout: post
title: "Yii框架整合UEditor上传附件到阿里云OSS"
date: 2018-07-30 15:40:00 +0800
categories: linux
description: "Yii框架整合UEditor上传附件到阿里云OSS"
tag: yii
---

> UEditor上传图片、视频等，默认是传到本地，而实际开发过程中，往往需要把附件保存至单独的文件服务器，而使用阿里云OSS来存储图片等附件的越来越多。本文主要介绍在Yii框架中，通过把UEditor改造成widget的方式，将上传的附件保存至OSS。

### UEditor简介

UEditor是由百度WEB前端研发部开发的所见即所得的开源富文本编辑器，具有轻量、可定制、用户体验优秀等特点。开源基于BSD协议，所有源代码在协议允许范围内可自由修改和使用。

在附件上传方面，UEditor在前端渲染完成后，会根据serverUrl配置项(可自定义)请求后端配置(主要是文件配置)，请求成功后，接下来附件上传操作都会通过请求serverUrl来实现，并在上传成功后，根据已请求到的后端配置进行本地处理(比如加上Url前缀)。


<br>
### 开始整合

**1.下载UEditor源码，并调整目录结构**

&nbsp;（1）下载源码，本文中以 [1.4.3.3 PHP 版本] UTF8 为例

&nbsp;（2）打开yii根目录，创建 common/widgets/ueditor/assets 目录，把UEditor源码拷贝到该目录下

&nbsp;（3）把 common/widgets/ueditor/assets/php 目录下的文件都拷贝到 common/widgets/ueditor/ 目录下

&nbsp;（4）在 common/widgets/ueditor/ 创建 config_oss.json、 UEditor.php、 UEditorAction.php、 UEditorAsset.php 这四个文件

**2.在view中正常调用小部件**

&nbsp;（1）创建Assets管理器，编辑UEditorAsset.php文件，如下：

{% highlight php linenos %}
    <?php
	namespace common\widgets\ueditor;

	use yii\web\AssetBundle;

	class UEditorAsset extends AssetBundle
	{

	    public $js = [
	        'ueditor.config.js',
	        'ueditor.all.js',
	    ];
	   
	    public function init()
	    {
	        $this->sourcePath = dirname(__FILE__) . DIRECTORY_SEPARATOR . 'assets';
	    }
	}
    ?>
{% endhighlight %}

&nbsp;（2）创建Widget，编辑UEditor.php文件(注意init方法中serverUrl的设置)，如下：

{% highlight php linenos %}
    <?php
	namespace common\widgets\ueditor;

	use Yii;
	use yii\helpers\ArrayHelper;
	use yii\helpers\Html;
	use yii\helpers\Json;
	use yii\helpers\Url;
	use yii\web\View;
	use yii\widgets\InputWidget;

	class UEditor extends InputWidget
	{
	    //配置选项，参阅Ueditor官网文档(定制菜单等)
	    public $clientOptions = [];

	    //默认配置
	    protected $_options;

	    /**
	     * @throws \yii\base\InvalidConfigException
	     */
	    public function init()
	    {
	        if (isset($this->options['id'])) {
	            $this->id = $this->options['id'];
	        } else {
	            $this->id = $this->hasModel() ? Html::getInputId($this->model,
	                $this->attribute) : $this->id;
	        }
	        $this->_options = [
	            'serverUrl' => Url::to(['upload']),	 //与后端交互的接口地址
	            'initialFrameWidth' => '100%',
	            'initialFrameHeight' => '400',
	            'lang' => (strtolower(Yii::$app->language) == 'en-us') ? 'en' : 'zh-cn',
	        ];
	        $this->clientOptions = ArrayHelper::merge($this->_options, $this->clientOptions);
	        parent::init();
	    }

	    public function run()
	    {
	        $this->registerClientScript();
	        if ($this->hasModel()) {
	            return Html::activeTextarea($this->model, $this->attribute, ['id' => $this->id]);
	        } else {
	            return Html::textarea($this->id, $this->value, ['id' => $this->id]);
	        }
	    }

	    /**
	     * 注册客户端脚本
	     */
	    protected function registerClientScript()
	    {
	        UEditorAsset::register($this->view);
	        $clientOptions = Json::encode($this->clientOptions);
	        $script = "UE.getEditor('" . $this->id . "', " . $clientOptions . ");";
	        $this->view->registerJs($script, View::POS_READY);
	    }
	}
    ?>
{% endhighlight %}

&nbsp;（3）在视图文件中，使用以下代码调用UEditor：
{% highlight php linenos %}
	<?= $form->field($model,'content')->widget('common\widgets\ueditor\UEditor',[]) ?>
{% endhighlight %}

### 3.配置并实现上传

&nbsp;（1）上一步中，在UEditor.php文件中配置了serverUrl，前端渲染完成后，会通过请求 serverUrl??action=config&&noCache=1532939514330 的方式获取后端配置(主要是上传方面)，因此除了原有的config.json，再增加一个config.json

{% highlight json linenos %}
	/* OSS相关的配置,注释只允许使用多行方式 */
	{
	    "oss_open" : true,
	    "oss_config" : {
	        "accessKeyId" : "accessKeyId_val",
	        "accessKeySecret" : "accessKeySecret_val",
	        "bucket" : "bucket_val",
	        "endPoint" : "oss-cn-beijing.aliyuncs.com"
	    },
	    "imageUrlPrefix" : "http://img.com",
	    "videoUrlPrefix" : "http://haohao-cn.oss-cn-beijing.aliyuncs.com",
	    "fileUrlPrefix" : "http://haohao-cn.oss-cn-beijing.aliyuncs.com",
	    "scrawlUrlPrefix" : "http://img.com"
	}
{% endhighlight %}


&nbsp;（2）UEditor请求配置及上传操作需要请求serverUrl，而这一步需要在控制器(这里是WikiController.php)中设置一个 独立操作，如下：

{% highlight php linenos %}
    <?php
	/**
     * @inheritdoc
     */
    public function actions()
    {
        return [
            'error' => [
                'class' => 'yii\web\ErrorAction',
            ],
            'upload' => [
                'class' => 'common\widgets\ueditor\UEditorAction',
                'config' => [
                    // "imageUrlPrefix"  => "http://www.baidu.com",//图片访问路径前缀
                    // "imagePathFormat" => "/upload/image/{yyyy}{mm}{dd}/{time}{rand:6}" //上传保存路径
                    // "imageRoot" => Yii::getAlias("@webroot"),
                    // "imageActionName" => Url::toRoute(['/attach/upload']),
                ],
            ]
        ];
    }
    ?>
{% endhighlight %}

&nbsp;（2）修改UEditorAction.php文件，根据controller.php修改，如下：

{% highlight php linenos %}
    <?php
	namespace common\widgets\ueditor;

	//header('Access-Control-Allow-Origin: http://www.baidu.com'); //设置http://www.baidu.com允许跨域访问
	//header('Access-Control-Allow-Headers: X-Requested-With,X_Requested_With'); //设置允许的跨域header
	// date_default_timezone_set("Asia/chongqing");
	// error_reporting(E_ERROR);
	// header("Content-Type: text/html; charset=utf-8");

	use Yii;
	use yii\base\Action;
	use yii\helpers\ArrayHelper;

	class UEditorAction extends Action
	{
	    /**
	     * @var array
	     */
	    public $config = [];

	    public function init()
	    {
	        //close csrf
	        Yii::$app->request->enableCsrfValidation = false;  //不关闭的话，上传时会出现400错误

	        parent::init();
	    }

	    public function run()
	    {
	        $CONFIG = json_decode(preg_replace("/\/\*[\s\S]+?\*\//", "", file_get_contents(__DIR__ . "/config.json")), true);

	        //增加以下三行
	        $CONFIG_oss = json_decode(preg_replace("/\/\*[\s\S]+?\*\//", "", file_get_contents(__DIR__ . "/config_oss.json")), true);
	        $CONFIG_manual = $this->config; //在 controller的独立动作 中配置的，优先级最高
	        $CONFIG = ArrayHelper::merge($CONFIG, ArrayHelper::merge($CONFIG_oss, $CONFIG_manual));

	        $action = Yii::$app->request->get('action');
	        switch ($action) {
	            case 'config':
	                unset($CONFIG['oss_config']);  //增加这一行，oss配置不能传到前端(安全考虑)
	                $result = json_encode($CONFIG);
	                break;

	            /* 上传图片 */
	            case 'uploadimage':
	            /* 上传涂鸦 */
	            case 'uploadscrawl':
	            /* 上传视频 */
	            case 'uploadvideo':
	            /* 上传文件 */
	            case 'uploadfile':
	                $result = include("action_upload.php");
	                break;

	            /* 列出图片 */
	            case 'listimage':
	                $result = include("action_list.php");
	                break;
	            /* 列出文件 */
	            case 'listfile':
	                $result = include("action_list.php");
	                break;

	            /* 抓取远程文件 */
	            case 'catchimage':
	                $result = include("action_crawler.php");
	                break;

	            default:
	                $result = json_encode(array(
	                    'state'=> '请求地址出错'
	                ));
	                break;
	        }

	        /* 输出结果 */
	        if (isset($_GET["callback"])) {
	            if (preg_match("/^[\w_]+$/", $_GET["callback"])) {
	                echo htmlspecialchars($_GET["callback"]) . '(' . $result . ')';
	            } else {
	                echo json_encode(array(
	                    'state'=> 'callback参数不合法'
	                ));
	            }
	        } else {
	            echo $result;
	        }
	    }
	}
    ?>
{% endhighlight %}

&nbsp;（3）上传文件的方法主要用到action_upload文件，注意uploadscrawl中注释的配置项：

{% highlight php linenos %}
	<?php
	namespace common\widgets\ueditor;

	/**
	 * 上传附件和上传视频
	 * User: Jinqn
	 * Date: 14-04-09
	 * Time: 上午10:17
	 */
	include "Uploader.class.php";

	/* 上传配置 */
	$base64 = "upload";
	switch (htmlspecialchars($_GET['action'])) {
	    case 'uploadimage':
	        $config = array(
	            "pathFormat" => $CONFIG['imagePathFormat'],
	            "maxSize" => $CONFIG['imageMaxSize'],
	            "allowFiles" => $CONFIG['imageAllowFiles']
	        );
	        $fieldName = $CONFIG['imageFieldName'];
	        break;
	    case 'uploadscrawl':
	        $config = array(
	            "pathFormat" => $CONFIG['scrawlPathFormat'],
	            "maxSize" => $CONFIG['scrawlMaxSize'],
	            // "allowFiles" => $CONFIG['scrawlAllowFiles'],   //这一项配置信息里没有，需要注释掉
	            "oriName" => "scrawl.png"
	        );
	        $fieldName = $CONFIG['scrawlFieldName'];
	        $base64 = "base64";
	        break;
	    case 'uploadvideo':
	        $config = array(
	            "pathFormat" => $CONFIG['videoPathFormat'],
	            "maxSize" => $CONFIG['videoMaxSize'],
	            "allowFiles" => $CONFIG['videoAllowFiles']
	        );
	        $fieldName = $CONFIG['videoFieldName'];
	        break;
	    case 'uploadfile':
	    default:
	        $config = array(
	            "pathFormat" => $CONFIG['filePathFormat'],
	            "maxSize" => $CONFIG['fileMaxSize'],
	            "allowFiles" => $CONFIG['fileAllowFiles']
	        );
	        $fieldName = $CONFIG['fileFieldName'];
	        break;
	}

	//增加这两句，加入oss配置
	$config['oss_open'] = $CONFIG['oss_open'];
	$config['oss_config'] = $CONFIG['oss_config'];

	/* 生成上传实例对象并完成上传 */
	$up = new Uploader($fieldName, $config, $base64);

	/**
	 * 得到上传文件所对应的各个参数,数组结构
	 * array(
	 *     "state" => "",          //上传状态，上传成功时必须返回"SUCCESS"
	 *     "url" => "",            //返回的地址
	 *     "title" => "",          //新文件名
	 *     "original" => "",       //原始文件名
	 *     "type" => ""            //文件类型
	 *     "size" => "",           //文件大小
	 * )
	 */

	/* 返回数据 */
	return json_encode($up->getFileInfo());
    ?>
{% endhighlight %}

&nbsp;（4）上传功能主要在Uploader.class.php中实现，对该文件进行修改：

* 在最上方加入命名空间(namespace common\widgets\ueditor;)，并引入OSS扩展(use OSS\OssClient;)
* 在构造方法中，加入判断，如果($config['oss_open'] == true)，则执行上传到OSS的方法  
* 增加两个方法，upFileToOss() 和 upBase64ToOss()，在upFile()和upBase64()基础上，增加上传至OSS的代码(思路：先上传到本地，然后调用OSS的方法上传到OSS，上传成功后，删除本地文件)


<br>
完整代码见 [ueditor.zip]({{ "/images/yiiUeditorOss/ueditor.zip" | prepend: site.baseurl }})