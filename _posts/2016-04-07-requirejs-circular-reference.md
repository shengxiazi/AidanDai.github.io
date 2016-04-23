---
layout: post
title:  "【笔记】Requirejs 循环依赖报模块 undefined 处理方案"
date: 2016-04-07
categories: javascript
tags: javascript requirejs
keyword: "Requirejs, 循环依赖, 报模块 undefined , Requirejs 循环依赖报模块 undefined 处理方案"
author: Aidan Dai
---

开始学习使用RequireJS之后做了几个小例子，之后想着用RequireJS把百度前端技术学院-2016-spring的迷宫小游戏写了。感觉胜利就在前方了，忽然发现始终卡在一个问题上：

忽然想到之前看API的时候提到的“循环依赖”，当时“循环依赖比较罕见，它也是一个重构代码重新设计的警示灯。”让我直接忽略了这一部分的内容。

循环依赖，指的是两个模块之间相互依赖，即a依赖b，同时b依赖a，那么在这种情况下当b的模块函数被调用时，将会提示模块a undefined。

解决方法是b可以在模块已经定义好后用require()方法再获取，需要把require作为依赖注入进来。

即本来的写法是：

```javascript
define(["jquery","a"],
	function($, a){
		return {
			loading : function(data){
			a.addBag(data);
		}
	}
});
```

一般说来你无需使用require()去获取一个模块，而是应当使用注入到模块函数参数中的依赖。现在为了解决循环依赖带来的问题，首先要引入require的依赖，使用require()方法去获取模块a。问题解决了。

```javascript
define(["require","jquery","a"],
	function(require, $, a){
		return {
			loading : function(data){
			require("a").addBag(data);
		}
	}
});
```