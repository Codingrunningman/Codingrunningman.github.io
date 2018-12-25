---
layout: post
title: JavaScript的模块化
date: 2018-3-17 19:44:00
categories: JavaScript
tags: JS规范
---

* content
{: toc}

最近在浏览前端技术Blog时，发现如今的前端技术已经发生了很大的变化。前端如今不仅仅是简单的编写一些界面样式和JS特效，现在前端的复杂程度已经远超从前。而node的发展也使得JavaScript的模块化变得越发
重要。我的上一份实习项目，使用SeaJs来管理前端模块，然而一篇文章：[感觉一直追赶的SeaJS已死](https://github.com/seajs/seajs/issues/1605)，让我意识到，我现在的技术栈，可能已经完全落后于JS的发展了。
许多公司为了项目的稳定或管理上的方便，会采用已经不在主流之中的技术，然而对于程序员来说，这种落后是可怕的。




## 关于SeaJs
SeaJs是基于CMD规范编写的框架，CMD规范被很多人用来和AMD规范来比较，导致很多人认为SeaJs是同步加载模块，但其实AMD和CMD最大的不同是对**依赖模块的执行时机处理不同**，而不是加载的时机或者方式不同，二者
都是异步加载模块。在这里，简要介绍一下JavaScript的模块规范。

### CommonJs规范
CommonJs规范采用同步加载模块的方式，即在需要的时候引入模块，使用以下对象来进行模块化编程
* require 引入模块
* exports 导出模块
* module  标识模块

正是由于CommonJs使用require方式的推动，才出现了后来的AMD和CMD用require方式引用模块的风格。

### AMD规范
CommonJs规范的最大问题就是因为它加载模块的方式是同步加载，这样的限制，导致了CommonJs规范不适合在浏览器环境下应用，因此AMD规范产生了。AMD全称是异步模块定义。它采用异步方式加载模块，某个模块的加载不会
阻塞后续程序的运行，当所需模块加载完成后，才会执行对应的回调函数。AMD采用define语句定义模块
	
	define(id, dependency, factory)

id为模块名称，dependency为该模块的依赖模块，factory为执行函数

AMD同样适用require来引用依赖，但是具体参数和CommonJs有所不同
	
	require(dependency, callback)
	
dependency为依赖模块，callback为回调函数

### CMD规范
CMD就是SeaJs参考的规范，也是由其开发者制定的规范。CMD也采用异步方式加载模块，与AMD不同的是，CMD推崇就近依赖，即在需要依赖的时候再执行依赖；而AMD推崇依赖前置，即在执行代码之前就执行所有的依赖；
	
	AMD：require([dependency1,dependency2],function(){
	    // 需要dependency1
	     })
	
	CMD：require(dependency1)
	     //需要dependency1
	
CMD定义模块的方式与AMD相同，使用define语句定义

	define(id, dependency, function(require，exports，module){})

### ES6中的require和import
在ES6规范中，增加了require和import关键词来帮助我们引入模块。这两种方式都可以引入模块，区别是import更加官方，而require因为大家一直引用的关系也被加入到了规范中。

ES6中的标准使用是用exports导出模块，import引入模块

ES6中规定的import和exports支持的是CommonJs规范，CommonJs规范其实是一种后端规范。
	
不管是AMD还是CMD，CommonJs还是其他规范，都是为了JS模块化而做出的应对方案，而随着ES6的提出，JS的模块化已经被标准化。


在最后展示一张图片，说明各个技术的关系
![Markdown](http://i4.bvimg.com/635953/8df35ac9138d2b27.jpg)


本文参考了[javascript中的require、exports和import](https://www.cnblogs.com/libin-1/p/7127481.html)     



