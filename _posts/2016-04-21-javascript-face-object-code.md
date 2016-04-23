---
layout: post
title:  "【总结】javascript 面向对象编程"
date: 2016-04-21
categories: javascript
tags: javascript
keyword: "javascript 面向对象编程"
author: Aidan Dai
---

要想把面向对象程序设计思想运用到 javascript 编程当中，首先我们的熟悉 javascript 面向对象的基础知识；今天花点时间总结一下 javascript 面向对象的基础知识，在加上设计模式的学习，我已经看到了面向对象编程的曙光，你看到了吗？

## 前言、javascript 面向对象编程知识结构

<svg id="kity_svg_6" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="100%" height="795" style="display: inline; background: rgb(251, 251, 251);" viewBox="0 0 590 795"><defs id="kity_defs_7"><linearGradient id="kity_linearGradient_17" x1="0" y1="0" x2="0" y2="1"><stop id="kity_stop_18" offset="0" stop-color="rgb(255, 255, 255)"></stop><stop id="kity_stop_19" offset="1" stop-color="rgb(204, 204, 204)"></stop></linearGradient><marker id="kity_marker_2" orient="auto" refX="6" refY="0" viewBox="-7 -7 14 14" markerWidth="7" markerHeight="7" markerUnits="userSpaceOnUse"><path id="kity_path_3" fill="rgb(115, 161, 191)" stroke="none" d="M6,0A6,6,0,1,1,-6,0A6,6,0,1,1,6,0"></path></marker></defs><g id="kity_g_8"><g id="minder1" text-rendering="optimize-speed"><g id="minder_connect_group1"><path id="kity_path_31" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M132,431.5A146.5,321,0,0,1,278.5,110.5"></path><path id="kity_path_33" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M348.5,110.5C359,110.5,359,45.5,369.5,45.5L435.5,45.5"></path><path id="kity_path_35" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M348.5,110.5C360.5,110.5,360.5,109.5,372.5,109.5L438.5,109.5"></path><path id="kity_path_37" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M348.5,110.5C363,110.5,363,176.5,377.5,176.5L443.5,176.5"></path><path id="kity_path_39" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M132,431.5A149.5,304,0,0,0,281.5,735.5"></path><path id="kity_path_41" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M351.5,735.5C363.5,735.5,363.5,696.5,375.5,696.5L465.5,696.5"></path><path id="kity_path_43" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M351.5,735.5C366.5,735.5,366.5,775.5,381.5,775.5L471.5,775.5"></path><path id="kity_path_45" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M132,431.5A127.5,2,0,0,0,259.5,433.5"></path><path id="kity_path_47" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M329.5,433.5C344.5,433.5,344.5,356.5,359.5,356.5L425.5,356.5"></path><path id="kity_path_49" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M425.5,356.5C441.5,356.5,441.5,206.5,457.5,206.5L535.5,206.5"></path><path id="kity_path_51" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M425.5,356.5C441,356.5,441,266.5,456.5,266.5L570.5,266.5"></path><path id="kity_path_53" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M425.5,356.5C444.5,356.5,444.5,313.5,463.5,313.5L541.5,313.5"></path><path id="kity_path_55" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M425.5,356.5C440.5,356.5,440.5,382.5,455.5,382.5L545.5,382.5"></path><path id="kity_path_57" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M425.5,356.5C440.5,356.5,440.5,434.5,455.5,434.5L545.5,434.5"></path><path id="kity_path_59" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M425.5,356.5C440.5,356.5,440.5,486.5,455.5,486.5L569.5,486.5"></path><path id="kity_path_61" fill="none" stroke="rgb(115, 161, 191)" stroke-width="1" d="M329.5,433.5C344.5,433.5,344.5,538.5,359.5,538.5L425.5,538.5"></path></g><g id="minder_node1"><path id="node_outline1" fill="rgb(115, 161, 191)" stroke="rgb(115, 161, 191)" d="M25.5,411.5h213a5,5,0,0,1,5,5v30a5,5,0,0,1,-5,5h-213a5,5,0,0,1,-5,-5v-30a5,5,0,0,1,5,-5z" stroke-width="3"></path><g id="node_text1" fill="white"><text id="kity_text_22" text-rendering="inherit" font-size="16" dy=".8em" y="421.09999990463257" x="44.5">javascript 面向对象编程</text></g></g><g id="kity_g_12"><path id="kity_path_13" fill="none" stroke="none" d="M44.5,431.5h0v0h0z"></path></g><g id="kity_g_14"><path id="kity_path_15" fill="none" stroke="none" d="M44.5,431.5h0v0h0z"></path><path id="kity_path_16" fill="none" stroke="none"></path></g><g id="minder_node2"><g id="node_expander4" style="cursor: pointer;"><path id="kity_path_120" fill="white" stroke="gray" d="M279.5,110.5A6,6,0,1,1,267.5,110.5A6,6,0,1,1,279.5,110.5"></path><path id="kity_path_121" fill="none" stroke="gray" d="M269,110.5L278,110.5"></path></g><path id="node_outline5" fill="rgb(238, 243, 246)" stroke="rgb(115, 161, 191)" d="M283.5,97.5h62a3,3,0,0,1,3,3v20a3,3,0,0,1,-3,3h-62a3,3,0,0,1,-3,-3v-20a3,3,0,0,1,3,-3z" stroke-width="1"></path><g id="node_text5" fill="black"><text id="kity_text_69" text-rendering="inherit" font-size="14" dy=".8em" y="101.40000009536743" x="300.5">封装</text></g></g><g id="minder_node3"><g id="node_expander1" display="none" style="cursor: pointer;"><path id="kity_path_111" fill="white" stroke="gray" d="M379.5,31.5A6,6,0,1,1,367.5,31.5A6,6,0,1,1,379.5,31.5"></path><path id="kity_path_112" fill="none" stroke="gray"></path></g><path id="node_outline2" fill="none" stroke="none" d="M384.5,20.5h46a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-46a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text2" fill="black"><text id="kity_text_63" text-rendering="inherit" font-size="12" dy=".8em" y="23.700000047683716" x="389.5">一般类</text></g></g><g id="minder_node4"><g id="node_expander2" display="none" style="cursor: pointer;"><path id="kity_path_114" fill="white" stroke="gray" d="M382.5,95.5A6,6,0,1,1,370.5,95.5A6,6,0,1,1,382.5,95.5"></path><path id="kity_path_115" fill="none" stroke="gray"></path></g><path id="node_outline3" fill="none" stroke="none" d="M387.5,84.5h46a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-46a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text3" fill="black"><text id="kity_text_65" text-rendering="inherit" font-size="12" dy=".8em" y="87.70000004768372" x="392.5">闭包类</text></g></g><g id="minder_node5"><g id="node_expander3" display="none" style="cursor: pointer;"><path id="kity_path_117" fill="white" stroke="gray" d="M387.5,162.5A6,6,0,1,1,375.5,162.5A6,6,0,1,1,387.5,162.5"></path><path id="kity_path_118" fill="none" stroke="gray"></path></g><path id="node_outline4" fill="none" stroke="none" d="M392.5,151.5h46a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-46a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text4" fill="black"><text id="kity_text_67" text-rendering="inherit" font-size="12" dy=".8em" y="154.70000004768372" x="397.5">安全类</text></g></g><g id="minder_node6"><g id="node_expander7" style="cursor: pointer;"><path id="kity_path_129" fill="white" stroke="gray" d="M282.5,735.5A6,6,0,1,1,270.5,735.5A6,6,0,1,1,282.5,735.5"></path><path id="kity_path_130" fill="none" stroke="gray" d="M272,735.5L281,735.5"></path></g><path id="node_outline8" fill="rgb(238, 243, 246)" stroke="rgb(115, 161, 191)" d="M286.5,722.5h62a3,3,0,0,1,3,3v20a3,3,0,0,1,-3,3h-62a3,3,0,0,1,-3,-3v-20a3,3,0,0,1,3,-3z" stroke-width="1"></path><g id="node_text8" fill="black"><text id="kity_text_75" text-rendering="inherit" font-size="14" dy=".8em" y="726.4000000953674" x="303.5">多态</text></g></g><g id="minder_node7"><g id="node_expander5" display="none" style="cursor: pointer;"><path id="kity_path_123" fill="white" stroke="gray" d="M385.5,682.5A6,6,0,1,1,373.5,682.5A6,6,0,1,1,385.5,682.5"></path><path id="kity_path_124" fill="none" stroke="gray"></path></g><path id="node_outline6" fill="none" stroke="none" d="M390.5,671.5h70a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-70a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text6" fill="black"><text id="kity_text_71" text-rendering="inherit" font-size="12" dy=".8em" y="674.7000000476837" x="395.5">编译时多态</text></g></g><g id="minder_node8"><g id="node_expander6" display="none" style="cursor: pointer;"><path id="kity_path_126" fill="white" stroke="gray" d="M391.5,761.5A6,6,0,1,1,379.5,761.5A6,6,0,1,1,391.5,761.5"></path><path id="kity_path_127" fill="none" stroke="gray"></path></g><path id="node_outline7" fill="none" stroke="none" d="M396.5,750.5h70a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-70a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text7" fill="black"><text id="kity_text_73" text-rendering="inherit" font-size="12" dy=".8em" y="753.7000000476837" x="401.5">运行时多态</text></g></g><g id="minder_node9"><g id="node_expander16" style="cursor: pointer;"><path id="kity_path_156" fill="white" stroke="gray" d="M342.5,433.5A6,6,0,1,1,330.5,433.5A6,6,0,1,1,342.5,433.5"></path><path id="kity_path_157" fill="none" stroke="gray" d="M332,433.5L341,433.5"></path></g><path id="node_outline17" fill="rgb(238, 243, 246)" stroke="rgb(115, 161, 191)" d="M264.5,420.5h62a3,3,0,0,1,3,3v20a3,3,0,0,1,-3,3h-62a3,3,0,0,1,-3,-3v-20a3,3,0,0,1,3,-3z" stroke-width="1"></path><g id="node_text17" fill="black"><text id="kity_text_93" text-rendering="inherit" font-size="14" dy=".8em" y="424.40000009536743" x="281.5">继承</text></g></g><g id="minder_node10"><g id="node_expander14" style="cursor: pointer;"><path id="kity_path_150" fill="white" stroke="gray" d="M369.5,342.5A6,6,0,1,1,357.5,342.5A6,6,0,1,1,369.5,342.5"></path><path id="kity_path_151" fill="none" stroke="gray" d="M359,342.5L368,342.5"></path></g><path id="node_outline15" fill="none" stroke="none" d="M374.5,331.5h46a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-46a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text15" fill="black"><text id="kity_text_89" text-rendering="inherit" font-size="12" dy=".8em" y="334.7000000476837" x="379.5">单继承</text></g></g><g id="minder_node11"><g id="node_expander8" display="none" style="cursor: pointer;"><path id="kity_path_132" fill="white" stroke="gray" d="M467.5,192.5A6,6,0,1,1,455.5,192.5A6,6,0,1,1,467.5,192.5"></path><path id="kity_path_133" fill="none" stroke="gray"></path></g><path id="node_outline9" fill="none" stroke="none" d="M472.5,181.5h58a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-58a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text9" fill="black"><text id="kity_text_77" text-rendering="inherit" font-size="12" dy=".8em" y="184.70000004768372" x="477.5">类式继承</text></g></g><g id="minder_node12"><g id="node_expander9" display="none" style="cursor: pointer;"><path id="kity_path_135" fill="white" stroke="gray" d="M466.5,252.5A6,6,0,1,1,454.5,252.5A6,6,0,1,1,466.5,252.5"></path><path id="kity_path_136" fill="none" stroke="gray"></path></g><path id="node_outline10" fill="none" stroke="none" d="M471.5,241.5h94a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-94a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text10" fill="black"><text id="kity_text_79" text-rendering="inherit" font-size="12" dy=".8em" y="244.70000004768372" x="476.5">构造函数式继承</text></g></g><g id="minder_node13"><g id="node_expander10" display="none" style="cursor: pointer;"><path id="kity_path_138" fill="white" stroke="gray" d="M473.5,299.5A6,6,0,1,1,461.5,299.5A6,6,0,1,1,473.5,299.5"></path><path id="kity_path_139" fill="none" stroke="gray"></path></g><path id="node_outline11" fill="none" stroke="none" d="M478.5,288.5h58a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-58a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text11" fill="black"><text id="kity_text_81" text-rendering="inherit" font-size="12" dy=".8em" y="291.7000000476837" x="483.5">组合继承</text></g></g><g id="minder_node14"><g id="node_expander11" display="none" style="cursor: pointer;"><path id="kity_path_141" fill="white" stroke="gray" d="M465.5,368.5A6,6,0,1,1,453.5,368.5A6,6,0,1,1,465.5,368.5"></path><path id="kity_path_142" fill="none" stroke="gray"></path></g><path id="node_outline12" fill="none" stroke="none" d="M470.5,357.5h70a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-70a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text12" fill="black"><text id="kity_text_83" text-rendering="inherit" font-size="12" dy=".8em" y="360.7000000476837" x="475.5">原型式继承</text></g></g><g id="minder_node15"><g id="node_expander12" display="none" style="cursor: pointer;"><path id="kity_path_144" fill="white" stroke="gray" d="M465.5,420.5A6,6,0,1,1,453.5,420.5A6,6,0,1,1,465.5,420.5"></path><path id="kity_path_145" fill="none" stroke="gray"></path></g><path id="node_outline13" fill="none" stroke="none" d="M470.5,409.5h70a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-70a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text13" fill="black"><text id="kity_text_85" text-rendering="inherit" font-size="12" dy=".8em" y="412.7000000476837" x="475.5">寄生式继承</text></g></g><g id="minder_node16"><g id="node_expander13" display="none" style="cursor: pointer;"><path id="kity_path_147" fill="white" stroke="gray" d="M465.5,472.5A6,6,0,1,1,453.5,472.5A6,6,0,1,1,465.5,472.5"></path><path id="kity_path_148" fill="none" stroke="gray"></path></g><path id="node_outline14" fill="none" stroke="none" d="M470.5,461.5h94a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-94a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text14" fill="black"><text id="kity_text_87" text-rendering="inherit" font-size="12" dy=".8em" y="464.7000000476837" x="475.5">寄生组合式继承</text></g></g><g id="minder_node17"><g id="node_expander15" display="none" style="cursor: pointer;"><path id="kity_path_153" fill="white" stroke="gray" d="M369.5,524.5A6,6,0,1,1,357.5,524.5A6,6,0,1,1,369.5,524.5"></path><path id="kity_path_154" fill="none" stroke="gray"></path></g><path id="node_outline16" fill="none" stroke="none" d="M374.5,513.5h46a5,5,0,0,1,5,5v12a5,5,0,0,1,-5,5h-46a5,5,0,0,1,-5,-5v-12a5,5,0,0,1,5,-5z"></path><g id="node_text16" fill="black"><text id="kity_text_91" text-rendering="inherit" font-size="12" dy=".8em" y="516.7000000476837" x="379.5">多继承</text></g></g></g></g></svg>

## 一、面向对象程序设计

- 面向对象：万物皆对象，面向对象是一种更加优秀的程序设计方法，它的基本思想是使用类、对象、继承、封装、消息等概念来进行程序的设计。是从世界客观存在的事物(即对象)出发，并根据这些事物的本质特点，把它们抽象的表示为系统中的类，这样程序可以直接映射客观世界，并保持客观世界中事物及其相关关系的本来面貌。

- 面向对象的三个最基本的特征：`封装(Encapsulation)`、`继承(Inheritance)`、`多态(Polymorphism)`。封装是将对象的实现细节隐藏起来，然后通过一些公用的方法来暴露该对象的功能；继承是指，当子类继承父类后，子类可以直接获得父类的属性和方法；多态指的是子类对对象可以直接赋给父类变量，但运行是依然表现子类的行为特征，这意味着同一个类型的对象在运行时可能表现不同的行为特征。

- 使用面向对象的好处：可以为程序提供更好的`可重用性`、`可扩展性`和`可维护性`。

## 二、javascript 中的封装

隐藏对象的属性和实现细节，仅对外提供公共访问方式

### 1、一般类（构造函数类）

```javascript
var Student = function(name, age){
	var id = 0;//私有属性

	//公有属性
	this.name = "";
	this.age = -1;
	
	//私有方法
	function checkId(id){
		if(parseInt(id) > 0){
			return true;
		}
		throw new Error("Invalid Id!");
		return false;
	}

	//特权方法
	this.setName = function(name){
		this.name = name;
		return true;
	};
	this.setAge = function(age){
		this.age = age;
		return true;
	};
	this.getName = function(){
		return this.name;
	};
	this.getAge = function(){
		return this.age;
	};

	//公有方法
	this.status = function(){
		return "I am Studaying!";
	};

	//构造器
	this.setName(name);
	this.setAge(age);
}

//静态公有属性
Student.hasCourses = true;
//静态公有方法
Student.resetTime = function(){
	return new Date();
}
// 公有属性
Student.prototype.bookCount = 100;
// 公有方法
Student.prototype.show = function(){
	return "show!";
}
```

### 2、闭包类

```javascript
// 闭包类1
var Book = (function(){
	//静态私有变量
	var bookNum = 0;
	//静态私有方法
	function checkBook(name){}

	//返回构造函数
	return function(newId, newName, newPrice){
		//私有变量
		var name, price;
		//私有属性
		function CeeckId(id){}

		//特权方法
		this.getName = function(){}
		this.getPrice = function(){}
		this.setName = function(){}
		this.setPrice = function(){}


		//公有属性
		this.id = newId;

		this.copy = function(){};

		bookNum++;
		if(bookNum > 3){
			throw new Error("我们仅出版100本书！")
		}

		this.setName(name);
		this.setPrice(price);
	}
})();

Book.prototype = {
	//公有属性
	isJSBook: false,
	//公有方法
	display: function(){}
}

// 闭包类2
var Book = (function(){
	//静态私有变量
	var bookNum = 0;
	//静态私有方法
	function checkBook(name){}

	//返回构造函数
	function book(newId, newName, newPrice){
		//私有变量
		var name, price;
		//私有属性
		function CeeckId(id){};

		//特权方法
		this.getName = function(){};
		this.getPrice = function(){};
		this.setName = function(){};
		this.setPrice = function(){};


		//公有属性
		this.id = newId;

		this.copy = function(){};

		bookNum++;
		if(bookNum > 3){
			throw new Error("我们仅出版100本书！")
		}

		this.setName(name);
		this.setPrice(price);
	}

	book.prototype = {
		//公有属性
		isJSBook: false,
		//公有方法
		display: function(){}
	}

	return book;
})();
```

这两种闭包类没有什么实质上的区别。

### 3、安全类

```javascript
var Book = function(title, name, type){
	// 判断执行过程中 this 是否是当前这个对象（如果是说明是用 new 创建的）
	if(this instanceof Book){
		this.title = title;
		this.name = name;
		this.type = type;
	// 否则重新创建这个对象
	}else{
		return new Book(title, name, type);
	}
}
```

避免没有使用`new`创建对象而造成的错误！

以上三种创建类的方法，没有什么实质上的区别，个人觉得结合一般类和安全类就完全可以胜任所有类的创建了！

## 三、javascript 中的继承

### 1、单继承

继承是指这样一种能力：它可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。

#### （1）类式继承

```javascript
//声明父类
function SupperCalss(name){
	this.superValue = true;
	//this.name = name;
}

// 为父类添加公有方法
SupperCalss.prototype.getSupperValue = function(){
	return this.supperValue;
}

// 声明子类
function SubClass(){
	this.SubValue = false;
}

// 继承父类
SubClass.prototype = new SupperCalss();
//SubClass.prototype = new SupperCalss("AidanDai");//传参导致所有SubClass实例化的对象没有区别

//为子类添加公有方法
SubClass.prototype.getSubVale = function(){
	return this.SubValue;
}
```

优点：

- 结构清晰

缺点

- 父类中的引用类型的公共属性影响所有子类，
在一个子类中改变从父类继承来的引用类型的公共属性会影响所用子类（浅克隆）

#### （2）构造函数继承

```javascript
// 声明父类
function SupperClass(id){
	//引用类型共有属性
	this.books = ["Javascript", "html", "css"];
	//基本类型共有属性
	this.id = id;
}

//父类声明原型方法
SupperClass.prototype.showBooks = function(){
	return this.books;
}

//声明子类
function SubClass(id){
	//继承父类（深克隆）
	SupperClass.call(this, id);
}
```

优点：

- 避免了父类中引用类型属性的浅克隆问题

缺点

- 这种继承没有涉及原型 prototype ,所以父类中的原型方法不会被继承；如果为了继承而把方法也放在构造函数中，这样创建出来的每个实例都各自拥有一份而不能公用，这违背了代码复用的原则

#### （3）组合继承

```javascript
// 声明父类
function SupperClass(name){
	//应用类型共有属性
	this.books = ["Javascript", "html", "css"];
	//基本类型共有属性
	this.name = name || "Aidan Dai"; //改进后
}

//父类声明原型方法
SupperClass.prototype.getName = function(){
	return this.name;
}

//声明子类
function SubClass(name, time){
	//构造函数式继承父类（深克隆）
	SupperClass.call(this, name);//未涉及到原型继承
	// 子类中新增共有属性
	this.time = time;
}

// 子类原型类式继承父类
SubClass.prototype = new SupperClass();

// 子类原型方法
SubClass.prototype.getTime = function(){
	return this.time;
}
```

优点：

- 避免了父类中引用类型属性的浅克隆问题；构造函数继承中不涉及原型的缺点

缺点

- 执行了两遍父类的构造函数，导致子类型原型上保持了大量多余属性，也就是父类型构造函数中定义的属性（ps：浪费内存空间）

#### （4）原型式继承

```javascript
function inheritObject(o){
	//声明一个过滤对象
	function F(){}
	//过滤对象的原型继承父对象
	F.prototype = o;
	//返回过渡对象的一个实例，该实例的原型继承了父对象
	return new F();
}

function Person(){
	this.name = "Aidan Dai";
	this.ability = ["html", "css", "javascript", "php"];
}

Person.prototype = {
	showAbility: function(){
		console.log(this.ability);
	}
}

var person1 = new Person();
var person2 = inheritObject(person1);
```

这是**道格拉斯-克罗克德**提出的一种继承方式；这是对类式继承的一个封装，其中过渡类型就相当于类式继承中的子类。

优点：

- 封装了类式继承，但同时对熟悉类式继承的同学来说结构不是很清晰

缺点：

- 类式继承的浅克隆问题

#### （5）寄生式继承

```javascript
function inheritObject(o){
	//声明一个过滤对象
	function F(){}
	//过滤对象的原型继承父对象
	F.prototype = o;
	//返回过渡对象的一个实例，该实例的原型继承了父对象
	return new F();
}

var book = {
	name: "js book",
	alikeBook: ["css book", "html book"]
};

function createBook(obj){
	var o = new inheritObject(obj);

	o.getName = function(){
		return name;
	}

	return o;
}

var obj = createBook(book);
```

这是**道格拉斯-克罗克德**提出的另一种继承方式；这是对原型式继承的增强

优点：

- 封装了类式继承，但同时对熟悉类式继承的同学来说结构不是很清晰

缺点：

- 类式继承的浅克隆问题

#### （6）寄生组合式继承

```javascript
function inheritObject(o){
	//声明一个过滤对象
	function F(){}
	//过滤对象的原型继承父对象
	F.prototype = o;
	//返回过渡对象的一个实例，该实例的原型继承了父对象
	return new F();
}

/**
 * [inheritPrototype description]
 * @param  {[type]} subClass    子类
 * @param  {[type]} supperClass 父类
 * @return {[type]}             [description]
 */
function inheritPrototype(subClass, supperClass){
	//复制一份父类的原型副本保存在变量中
	var p = inheritObject(supperClass.prototype);
	// 修正因为重写子类原型导致子类的constructor属性被修改
	p.constructor = subClass;
	// 设置子类型的原型
	subClass.prototype = p;
}

// 定义父类
function SupperClass(name){
	this.name = name;
	this.colors = ["red", "blue", "green"];
}

// 定义父类原型方法
SupperClass.prototype.getName = function(){
	return this.name;
}

// 定义子类
function SubClass(name, time){
	// 构造函数式继承
	SupperClass.call(this, name);
	// 子类新增属性
	this.time = time;
}

//寄生式继承父类原型
inheritPrototype(SubClass, SupperClass);

// 子类新增原型方法
SubClass.prototype.getTime = function(){
	return this.time;
}
```

优点：

- 结构清晰，保留住了面向对象语言中类式继承的经典结构；同时又将javascript中的原型继承发挥到极致，利用构造函数继承避免了构造函数中引用类型浅克隆问题，同时避免了子类原型引入无用属性（ps：再也不用担心浪费内存了）

缺点：

- 貌似没有什么缺点，我感觉这是javascript中最好的继承机制！当然比面向对象语言中的继承稍微麻烦了点！

原型链图如下：

![javascript 经典继承原型链图](/asset/images/article/javascript-well-extends.jpg)

### 2、多继承

```javascript
// 单继承 属性赋值
var extend = function(target, source){
	// 遍历原对象中的属性
	for(var property in source){
		// 将源对象中的属性复制到目标对象
		target[property] = source[property];
	}
	// 返回原对象
	return target;
};

// 多继承 属性复制
var mix = function(){
	var len = arguments.length,	//从第二个参数起为被继承对象
		target = arguments[0],  //取第一个对象为目标对象
		arg;					//缓存参数对象
	// 遍历被继承对象中的属性 
	for(var i=1; i<len; i++){
		arg = arguments[i];
		// 遍历被继承对象中的属性
		for(var property in arg){
			// 件被继承对象中的属性复制到目标对象中
			target[property] = arg[property];
		}
	}
	// 返回附表对象
	return target;
}
```

说实话这种实现让人觉得有点荒谬？

## 四、javascript 中的多态

### 1、编译时多态

简单工厂方法！一个典型的编译时多态。直接贴代码，就不多说了

```javascript
// 篮球基类
var Basketball = function(){
	this.info = "蓝球盛行于美国";
}

Basketball.prototype = {
	getMember: function(){
		return "每个队伍需要5名队员！";
	},
	getBallSize: function(){
		return "篮球很大！";
	}
}

// 足球基类
var Football = function(){
	this.info = "足球在世界范围很流行！";
}

Football.prototype = {
	getMember: function(){
		return "每个队伍需要11名队员！";
	},
	getBallSize: function(){
		return "足球很大！";
	}
}

// 网球基类
var Tennis = function(){
	this.info = "每年有很多网球系列赛！";
}

Tennis.prototype = {
	getMember: function(){
		return "每个队伍需要1名队员！";
	},
	getBallSize: function(){
		return "篮球很小！";
	}
}

// 运动工厂
var SportFactory = function(name){
	switch(name){
		case "NBA":
			return new Basketball();
			break;
		case "wordCup":
			return new Football();
			break;
		case "FrenchOpen":
			return new Tennis();
			break;
	}
}

var basketbool = new SportFactory("NBA");
```

### 2、运行时多态（重载）

```javascript
function Add(){
	// 无参数算法
	function zero(){
		return 10;
	}
	// 一个参数算法
	function one(num){
		return 10 + num;
	}
	// 两个参数算法
	function two(num1, num2){
		return num1 + num2;
	}
	// 相加共有方法
	this.add = function(){
		var arg = arguments,
			len = arg.length;
		switch(len){
			case 0:
				return zero();
				break;
			case 1:
				return one(arg[0]);
				break;
			case 2:
				return two(arg[0], arg[1]);
				break;
		}
	}
}
```

## 五、结束语

以上就是 javascript 面向对象编程的基础知识，不难发现都是在模仿C++之类的真正面向对象语言来实现的，所以要掌握面向对象设计思想还是要去学习真正的面向对象语言实现思想，然后把它运用在 javascript 编程当中！这样才能真正理解面向对象设计思想的精华所在！