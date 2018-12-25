---
layout: post
title: JavaScript的变量及作用域
date: 2018-3-12 21:33:00
categories: JavaScript
tags: 作用域
---

* content
{:toc}

## 变量
JavaScript是一门动态弱类型语言，在定义变量时不必为它定义任何数据类型。对于JavaScript来说，变量可能包含两种不同类型的值：引用类型和基本类型。




### 基本类型
基本类型有5种
* Number
* String
* Boolean
* Undefined
* Null

**Null是一个奇特的变量，Null作为一种基本类型，用typeof检测其类型时**
	
	typeof null

它返回的值是Object而不是null，原因在于null表示的是空指针，即变量指向的地址为空(如果是未初始化，变量的值均为undefined)，在大多数平台下为0X00。而JavaScript的值是由表示类型的标签和实际数值
组成的，当类型标签为0时，表示的恰好是Object，因此，这可以算是JavaScript的一个漏洞。可以访问[The history of null](http://2ality.com/2013/10/typeof-null.html)获取详细。

基本类型是按值访问的，如下图    
![Markdown](http://p5ze3murq.bkt.clouddn.com/%E6%8C%89%E5%80%BC%E8%AE%BF%E9%97%AE.jpg)

### 引用类型
引用类型有以下几种
* Object
* Array
* Date
* RegExp
* Function
* 基本包装类型(Boolean,String,Number)
* 单体内置对象(Global,eval())

JavaScript的引用类型的值是保存在内存中的对象，JavaScript不允许直接操作对象的内存空间，操作对象时，实际上是在操作对象的引用(这种说法还不够严谨，当你为对象添加属性时，实际上是在直接操作对象
)，如下图     
![Markdown](http://p5ze3murq.bkt.clouddn.com/%E6%8C%89%E5%BC%95%E7%94%A8%E8%AE%BF%E9%97%AE.jpg)

## 作用域
在ES5前，JavaScript没有块级作用域，即用花括号括起来的域，如for(){}、if(){}等等；它仅包含函数作用域和全局作用域(ES6后，用let定义的变量具有块级作用域)。
 
### 执行环境
在JS中，有一个重要的概念叫做**执行环境**，它定义了在该执行环境中变量或函数可以访问的数据。每个执行环境都对应了一个**变量对象**，该对象中存储了该环境中定义的变量和函数。某个执行环境中的所有代码
被执行完成后，该执行环境会被销毁，保存在其中的变量和函数也会被销毁。

当执行流执行到一个执行环境时，将该执行环境压栈，待到执行环境中的代码执行完成，将其弹出，将控制权交给栈顶的执行环境。

###  作用域链
变量对象存储了某个执行环境中的变量和函数，而当代码在某个执行环境中运行时，会创建一个该变量对象的作用域链。此时，该环境对应的变量对象处于作用域链的末端。而往上则是包含了该运行环境的其他
运行环境的变量对象。(这样，我们就知道了JS的一个特性，内部函数可以访问外部函数的变量)。当然，最外层的变量对象一定是全局执行环境的变量对象。这条作用域链决定了在该执行环境中，可以访问哪些
变量或函数，查找的方式是从作用域链的最前端一层层往上查找。
	
	var num = 13;
	function parent(){
	    var name = "Niol";
		function son1(){
		    var name1;
		}
	    function son2(){
		    var name2 = name;
			var age = 12;
		}
        return num;
	}

当函数运行到son2时，它的作用域链是这样的：

![Markdown](http://p5ze3murq.bkt.clouddn.com/%E4%BD%9C%E7%94%A8%E5%9F%9F%E9%93%BE.jpg)

### 延长作用域链
* with
* try-catch

虽然执行环境党的类型只有两种(函数执行环境和全局执行环境)，然而我们可以通过上面两种语句在作用域链的前端临时加上一个变量对象来延长作用域链，观察如下代码：
	
	function update()
	{
	   lastName = 'Niol';
	   with(person){
	     var name = firstName + lastName;
	   }
	   return name;
	}

其中person是一个对象，firstName是该对象的属性，在with中定义了一个变量name，经过和lastName组合后，再在外部函数中返回。这其实相当于在update函数的执行环境的作用域链的前端添加了一个with的临时变量对象，
with中的变量和函数也可以在该环境中被访问，等到函数执行结束后，该变量对象被移除。









