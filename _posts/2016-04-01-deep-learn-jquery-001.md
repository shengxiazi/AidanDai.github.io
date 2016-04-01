---
layout: post
title:  "jQuery 源码研究 - 自执行匿名函数"
date:   2016-03-30
categories: jQuery
tags: javascript jQuery
keyword: "javascript, jQuery, jQuery 源码研究, javascript 自执行匿名函数"
author: Aidan Dai
---

今天在读《jQuery技术内幕：深入解析jQuery架构设计与实现原理》发现了作者：高云提出了个十分有意思的话题，javascript 中的自执行匿名函数有哪几种写法？自己觉得十分有意思，便研究了下！结果记录在本篇博客，如有不对还望大家指正！

## 三种实现自执行匿名函数的方法

《jQuery技术内幕：深入解析jQuery架构设计与实现原理》一书中作者总结了三种自执行匿名函数的写法，如下

```javascript
// 写法1（常见写法，也是 jQuery 所采用的）
(function() {
    //...
})();

//写法2
(function(){
    //...
}() );

//写法3
!function() {
    //...
}();
```

看过之后不禁问了个问什么？还有没有其他写法？归纳下，都有哪几种？

在自己写了个小例子，测试了下之后发现以下方法都可以实现自执行匿名函数：（chrome、sublime+node.js）

```javascript
(function(){
	console.log("()");
})();
(function(){
	console.log("()");
}());
[function() {
	console.log("[]");
}()];

// ++、-- 期望的操作数类型及运算符的结果类型（lval -> number）;而恰好
// function(){
// 	console.log("-");
// }()
// 并不是一个左值，所以++、--不能实现自执行函数（这只是个人理解）

// ++function(){
// 	console.log("-");
// }();
// --function(){
// 	console.log("-");
// }();

-function(){
	console.log("-");
}();
+function(){
	console.log("+");
}();
~function(){
	console.log("~");
}();
!function() {
	console.log("!");
}();
delete function() {
	console.log("delete");
}();
typeof function() {
	console.log("typeof");
}();
void function() {
	console.log("void");
}();

new function() {
	console.log("new");
}();
```

在查阅了一些资料后，大概总结了以下几种可以自执行的匿名函数：

- 利用 () 和 [] 特性
- 利用 new
- 利用一元运算符

代码块简单的分析了下原因，如果有错还望大家指正！关于运算符大家可以参考：《Javascript 权威指南（第六版）》p67;我也是在重读了这段内容才豁然开朗。。。

## 验证

这里只是举个简单例子，其它的大家可以自行验证

```javascript
var test = -function(){
	console.log("-");
    return 1;
}();
console.log(test); //-1
```

看着这个结果你是不是明白点什么了呢！
