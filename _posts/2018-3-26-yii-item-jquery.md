---
layout: post
title: 今天遇到的坑
date: 2018-3-26 21:10:00
categories: 坑
tags: jquery
---

今天在做项目时，项目要求实现一个功能，大致就是点击radio转换模板，听上去很简单，但是却被一个坑堵了很久，那就是

**$.attr('checked')这个方法对radio已经无效了，真正有效的是prop('checked')~**

啧啧，学艺不精