---
layout: post
title: JavaScript的拖拽功能实现
date: 2018-3-7 11:11:11
categories: JavaScript 
tags: 拖拽
---

* content
{:toc}

学习了一段时间JavaScript后，我发现自己将重心放在了理论学习上，然而仅仅通过看书来学习JS，这种方式效率是比较低的。一是没有实践，你不会了解自己对这个知识点的掌握程度；二是不动手的话，书上
存在的一些问题，你也发现不了。所以，在以后会加强在开发方面的实践，并通过blog记录学习过程。





## 事件处理
### 事件流的三个阶段
DOM2级事件规定了事件流包括三个阶段：

* 事件捕获阶段
* 目标接收到事件
* 冒泡阶段

在捕获阶段和冒泡阶段都有机会触发事件

### 事件处理程序
事件就是用户或浏览器执行的动作。一般为DOM绑定处理事件有两种方法：

* 将执行函数赋值给事件对象的响应事件属性
* 为事件对象增加监听器

两种方法都可以实现事件处理，不同点是第二种方法可以为同一个事件对象的响应事件属性绑定多个函数

	dom.onclick = function(){}
	//第一种
	dom.addEventListener('click',function(){})
	//第二种

关键又是这麻烦的兼容，IE实现了与DOM中addEventListener相似的方法：attachEvent。为了处理浏览器的兼容问题，我们可以编写这样的对象：

		var EventUtil = {
		    addHandler:function(el,type,handler){
			  if(el.addEventListener){
			     el.addEventListener(type,handler);
			  }else if(el.attachEvent){
			     el.attachEvent(type,handler);
			  }else{
			     el['on'+type] = handler;
			  }
			},
			removeHandler:function(el,type,handler){
			    if(el.removeEventListener){
			     el.removeEventListener(type,handler);
			  }else if(el.detachEvent){
			     el.detachEvent(type,handler);
			  }else{
			     el['on'+type] = null;
			  }
			}
		}

好了，简单了解完事件处理之后，接下来就是具体的实践

## 使用mousedown和mousemove实现的拖拽
拖拽过程分为三步：按下鼠标、按住移动、松开鼠标

实现的原理很简单：
* 在按下鼠标时，记录鼠标的位置，通过鼠标位置计算出节点的初始位置；
* 移动鼠标时，用鼠标位置的改变来设置节点的位置；
* 松开鼠标，解除移动鼠标和按下鼠标的绑定事件。

>对象的位置信息保存在事件对象的clientX和clientY中，这个值表示光标在视口中的水平和垂直坐标，如果页面向下滚动，它的值也只是相对浏览器视口而言，而不是针对整个页面；   
真正表示光标在页面中位置的是：pageX和pageY,表示光标在屏幕中位置的是：screenX和screenY，offsetX和offsetY表示光标相对于节点边界的偏移量,   
所以我们可以通过：initX = clientX-offsetX,initY = clientY-offsetY 来计算节点初始时距视口的横纵距离.

>光标移动时计算光标位置与节点初始位置的差值，即节点与视口的横纵方向的距离；   
clientX-initX+'px',clientY-initY,然后将这两个值分别赋值给元素的left和top属性，就可以改变元素在界面中的位置。   
此时需要元素的position属性为absolute

>光标松开时解除对鼠标移动和鼠标按下动作的事件绑定，这个比较简单，就不说了

**值得注意的地方是mousemove和mouseup事件的绑定对象应当是document，请思考一下是为什么？**

可以访问链接 [javascript实现小球的拖拽](https://codingrunningman.github.io/demo/drag-demo1.html),尝试一下

## 使用网页的原生拖放功能实现拖放
网页的原生拖放主要包含两个对象：拖动对象和放置目标

### 原生拖放规定事件：
当拖动元素时：

* dragstart：在按下光标并开始时移动时触发
* drag：元素被拖动时会持续触发该事件
* dragend：元素拖动停止时触发

当拖动元素到一个允许放置的对象上时：

* dragenter：元素被拖动到放置对象上时触发
* dragover：拖动元素在放置对象有效范围内移动时会持续触发
* drop：元素被放置到放置对象上时触发
* dragleave：拖动元素离开放置对象时触发

### 设置拖动对象和放置目标
默认情况下，只有图片、文字和链接是可以被拖动的，如果你希望自定义拖动对象，可以通过设置draggable的值为true来让元素变得可以拖动

**值得注意的是，在火狐浏览器中，如果没有为元素拖动事件的dataTransfer对象设置数据，那么即使你将该元素的draggable设置为true也无法拖动该元素**

网页中的所有元素都可以是放置目标，但有些元素默认不允许放置，此时，我们可以通过阻止放置目标的dragover和dragenter的默认事件使得该元素允许放置

	EventUtil.addHandler(target,'dragenter',function(e){
	    e.preventDefault();
	});
	EventUtil.addHandler(target,'dragover',function(e){
	    e.preventDefault();
	});

### dataTransfer对象
dataTransfer对象用于从拖动目标向放置对象传递数据，包含两个主要方法：setData()和getData()，IE对保存的数据类型定义了两种：text和URL，HTML5对类型进行了扩展

通过以下方式设置和获取数据：
	
	event.dataTransfer.setData('text','id'); //设置数据
	
	event.dataTransfer.getData('text')  //获取数据

### 详细过程
如果大家对上面的内容有了详细阅读和了解的话，那么过程也就非常简单了

>在拖动开始时，设置数据为该拖动元素的id   
拖动结束时，获取元素id，并将该元素作为子元素插入放置目标   
是不是非常简单呢？^_^ 但是要注意的是放置元素的**padding值**，如果padding值过大，可能会导致呈现出来的效果有问题

### 问题记录
>火狐在每次放置元素成功时，都会弹出新的标签页，为了阻止浏览器的默认行为，我在drop行为的事件中添加了e.preventDefault()语句，
然而在火狐浏览器中，依旧会有新标签页弹出；经过大量的查找，终于找到了这么一个语句
>>  e.stopPropagation()    // 这个语句可以阻止事件向上级元素冒泡，从而阻止document弹出页面

>第二个问题直到现在还没找到解决方案= =，就是拖拽未成功时松开光标，浏览器会打开新页面。我在dragend行为的事件中添加上面两句话，却没有产生效果；接着我想会不会是document被
设置成为了放置目标，于是我在document的drop行为的事件中也添加了这两句话，结果仍然失败==，如果有人有关于这个问题的解答，请在评论中告诉我~谢谢

这是第二个demo:[拖拽小球](https://codingrunningman.github.io/demo/drag-demo2.html)













