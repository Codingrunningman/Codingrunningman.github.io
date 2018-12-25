---
layout: post
title: Yii框架的学习记录(一)
date: 2018-3-25 19:24:00
categories: Yii
tags: 框架 后端
---

* content
{: toc}

最近入职了一家新的公司做后端开发，公司项目使用的框架是[Yii框架](http://www.yiichina.com/doc/guide/2.0)。在上一家公司实习时使用Symfony框架，学习的过程还是比较辛苦的(辛苦的主要原因是
Symfony在国内还不够流行，导致中文资料实在有点少，每次出现问题都要查半天)。我一直认为学习框架的最好方法是实践，光看是很难学会一个框架的。现在正好有项目帮助我学习，正是再好不过。




使用Git在本地安装完项目后，需要用composer加载依赖包。对一般的项目来说，都不会直接将依赖放在代码服务器上，而是通过composer去加载。
	
	composer install -vv

**加载依赖的过程一般会比较长，加上-vv可以了解后台的加载情况。**

Yii2.0有basic和advanced两个版本，区别在于advanced版本自带了前台和后台两个应用，而且你可以通过增加文件和修改配置增加更多个应用，而basic版本只有一个应用。

Yii框架和Symfony一样是基于MVC模式设计的框架，具有controller/view/model。类似地，Yii同样可以通过配置文件来设置应用的一些功能，如数据库等等。

### Yii应用的生命周期有以下几个阶段:
* 入口脚本(一般为web下的index.php文件)加载应用主体的配置数组
* 入口脚本创建一个应用主体实例
* 入口脚本调用yii\base\application::run()方法运行应用主体实例
* 入口脚本接收应用主体传过来的退出状态并完成请求的处理

### application中通过behaviors来过滤用户行为
	
	public function behaviors() {
			return [ 
				'access' => [ 
					'class' => AccessControl::className(),
					'rules' => [ 
						[ 
							'actions' => [ 
								'login',
								'error',
								'admin' ,
								'site',
							],
							'allow' => true 
						],
						[ 
							'actions' => [ 
								'logout',
								'index',
								'notice' ,
								'read-express',
							],
							'allow' => true,
							'roles' => [ 
								'@' 
							] 
						] 
					] 
				],
				'verbs' => [ 
					'class' => VerbFilter::className(),
					'actions' => [ 
						'logout' => [ 
							'post'
						] 
					] 
				] 
			];
		}

Yii定义的用户权限有两种:
1. guest: ？ 未登录
2. authenticated: @ 已登录的

action定义了两种用户权限可以访问的不同控制器或方法	

view(视图)中布局是一种特殊的视图，它是不同视图的公共部分，默认存储在view/layouts文件夹下，在Symfony中，可以通过twig模板的继承功能来实现公共模板；
当控制器使用render方法渲染视图时，会同时使用布局到渲染结果中，默认使用 /view/layouts/main.php文件
yii\base\Application::$layout 管理所有控制器的布局
yii\base\Controller::$layout 通过覆盖全局布局来实现单个布局

### PHP的HereDoc格式

HereDoc是PHP为了方便代码编写而引入的语法糖，它通过开始标记和结束标记来存储内容，在标记中的内容会被当作字符串处理，类似于双引号；

使用方式
	
	<<<doc
	doc;
	
其中结束标记必须顶头写，且末尾必须有引号，并且在该行不能有任何其他字符。
