---
layout: post
title:  "大行其道-JavaScript模块化开发(requirejs)"
date:   2016-03-26
categories: javascript
tags: javascript module requirejs
---

![requirejs](/asset/images/article/requirejs.png)

RequireJS是一个非常小巧的JavaScript模块载入框架，是AMD规范最好的实现者之一。最新版本的RequireJS压缩后只有14K，堪称非常轻量。它还同时可以和其他的框架协同工作，使用RequireJS必将使您的前端代码质量得以提升。

## requirejs能带来什么好处?

简言之使用requirejs有以下有点：

### 防止js加载阻塞页面渲染（异步加载）

先看下面这个例子：

- html

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
    <head>
        <title>javascript 模块化开发</title>
        <meta charset="utf8">
        <script type="text/javascript" src="a.js"></script>
    </head>
    <body>
        <h1>hello requirejs!</h1>
    </body>
</html>
```

- javascript

```JavaScript
// index.js
(function(){
	function echo(){
		alert("hello requirejs!");
	}
	echo();
}())
```

把这段小demo复制到你的编辑器运行下看下吧！（看看第一次打开是什么情况）

### 使用程序调用的方式加载js，防出现如下丑陋的场景

最早的时候，所有Javascript代码都写在一个文件里面，只要加载这一个文件就够了。后来，代码越来越多，一个文件不够了，必须分成多个文件，依次加载。下面的网页代码，相信很多人都见过。

```JavaScript
<script type="text/javascript" src="a.js"></script>
<script type="text/javascript" src="b.js"></script>
<script type="text/javascript" src="c.js"></script>
<script type="text/javascript" src="d.js"></script>
<script type="text/javascript" src="e.js"></script>
<script type="text/javascript" src="f.js"></script>
<script type="text/javascript" src="g.js"></script>
<script type="text/javascript" src="h.js"></script>
<script type="text/javascript" src="i.js"></script>
<script type="text/javascript" src="j.js"></script>
```
这段代码依次加载多个js文件。

这样的写法有很大的缺点。首先，加载的时候，浏览器会停止网页渲染，加载文件越多，网页失去响应的时间就会越长；其次，由于js文件之间存在依赖关系，因此必须严格保证加载顺序（比如上例的1.js要在2.js的前面），依赖性最大的模块一定要放到最后加载，当依赖关系很复杂的时候，代码的编写和维护都会变得困难。

require.js的诞生，就是为了解决这两个问题：

![requirejs](/asset/images/article/requirejs.png)

（1）实现js文件的异步加载，避免网页失去响应；

（2）管理模块之间的依赖性，便于代码的编写和维护。

### 团队协作、模块复用、单元测试等

这个大家在平时自己体会吧！

## 开始使用requirejs

### 加载requirejs

**注**：这里所说的路径都是指定到文件，但是不需要js后缀；路径都是相对requirejs的真时路径

```Javascript
<script type="text/javascript" src="path of requirejs"></script>
```

有人可能会想到，加载这个文件，也可能造成网页失去响应。解决办法有两个，一个是把它放在网页底部加载，另一个是写成下面这样

```Javascript
<script type="text/javascript" src="path of requirejs" defer async="true" ></script>
```

async属性表明这个文件需要异步加载，避免网页失去响应。IE不支持这个属性，只支持defer，所以把defer也写上。

### 加载我们自己的代码

```Javascript
<script type="text/javascript" src="path of requirejs" data-main="path of myself javascript"></script>
```

data\-main属性的作用是，指定网页程序的主模块。在上例中，就是js目录下面的main.js，这个文件会第一个被require.js加载。由于require.js默认的文件后缀名是js，所以可以把main.js简写成main。

### 写个简单、完整的主模块

假定主模块依赖jquery、underscore和backbone这三个模块，main.js就可以这样写：

#### 加载模块

```Javascript
//main.js
require(['jquery', 'underscore', 'backbone'], function ($, _, Backbone){
　　　　// some code here
});
```

require.js会先加载jQuery、underscore和backbone，然后再运行回调函数。主模块的代码就写在回调函数中。

#### 加载模块配置

使用require.config()方法，我们可以对模块的加载行为进行自定义。require.config()就写在主模块（main.js）的头部。参数就是一个对象，这个对象的paths属性指定各个模块的加载路径。

```Javascript
//main.js
require.config({
　　paths: {
　　　　"jquery": "path of jQuery",
　　　　"underscore": "path of underscore",
　　　　"backbone": "path of backone"
　　}
});

// 另一种则是直接改变基目录（baseUrl）
// require.config({
//     baseUrl: "path"
// 　　paths: {
// 　　　　"jquery": "path of jQuery",
// 　　　　"underscore": "path of underscore",
// 　　　　"backbone": "path of backone"
// 　　}
// });

// 指定绝对路径与多路径
// require.config({
//     paths : {
//         "jquery" :["http://libs.baidu.com/jquery/2.0.3/jquery", "path of jQuery"]
//     }
// })

require(['jquery', 'underscore', 'backbone'], function ($, _, Backbone){
　　　　// some code here
});
```

require.js要求，每个模块是一个单独的js文件。这样的话，如果加载多个模块，就会发出多次HTTP请求，会影响网页的加载速度。因此，require.js提供了一个[优化工具](http://requirejs.org/docs/optimization.html)，当模块部署完毕以后，可以用这个工具将多个模块合并在一个文件中，减少HTTP请求数。

## AMD模块的写法

require.js加载的模块，采用AMD规范。也就是说，模块必须按照AMD的规定来写。

具体来说，就是模块必须采用特定的define()函数来定义。如果一个模块不依赖其他模块，那么可以直接定义在define()函数之中。

假定现在有一个math.js文件，它定义了一个math模块。那么，math.js就要这样写：

```Javascript
// math.js
define(function (){
　　var add = function (x,y){
　　　　return x+y;
　　};
　　return {
　　　　add: add
　　};
});
```

加载方法如下：

```Javascript
// main.js
require(['math'], function (math){
　　alert(math.add(1,1));
});
```

如果这个模块还依赖其他模块，那么define()函数的第一个参数，必须是一个数组，指明该模块的依赖性。

```Javascript
define(['myLib'], function(myLib){
	function foo(){
		myLib.doSomething();
	}
	return {
		foo : foo
	};
});
```

当require()函数加载上面这个模块的时候，就会先加载myLib.js文件。

## 加载非规范的模块

理论上，require.js加载的模块，必须是按照AMD规范、用define()函数定义的模块。但是实际上，虽然已经有一部分流行的函数库（比如jQuery）符合AMD规范，更多的库并不符合。那么，require.js是否能够加载非规范的模块呢？

回答是可以的。

这样的模块在用require()加载之前，要先用require.config()方法，定义它们的一些特征。

举例来说，underscore和backbone这两个库，都没有采用AMD规范编写。如果要加载它们的话，必须先定义它们的特征。

```Javascript
require.config({
　　shim: {
        'underscore':{
　　　　　　      exports: '_'
　　      },
    　　 'backbone': {
    　　　　    deps: ['underscore', 'jquery'],
    　　　　    exports: 'Backbone'
    　　 }
　   }
});
```

require.config()接受一个配置对象，这个对象除了有前面说过的paths属性之外，还有一个shim属性，专门用来配置不兼容的模块。具体来说，每个模块要定义（1）exports值（输出的变量名），表明这个模块外部调用时的名称；（2）deps数组，表明该模块的依赖性。

比如，jQuery的插件可以这样定义：

```javascript
require.config({
    shim: {
        'jquery.scroll': {
            deps: ['jquery'],
            exports: 'jQuery.fn.scroll'
        }
    }
});
```

## require.js插件

require.js还提供一系列[插件](https://github.com/requirejs/requirejs/wiki/Plugins)，实现一些特定的功能。

domready插件，可以让回调函数在页面DOM结构加载完成后再运行。

```Javascript
require(['domready!'], function (doc){
　　// called once the DOM is ready
});
```

text和image插件，则是允许require.js加载文本和图片文件。

```Javascript
    define([
　　　　'text!review.txt',
　　　　'image!cat.jpg'
　　　　],

　　　　function(review,cat){
　　　　　　console.log(review);
　　　　　　document.body.appendChild(cat);
　　　　}
　　);
```

类似的插件还有json和mdown，用于加载json文件和markdown文件。

参考文章：

[阮一峰：Javascript模块化编程（三）：require.js的用法](http://www.ruanyifeng.com/blog/2012/11/require_js.html)

[菜鸟教程：JS模块化工具requirejs教程(一)：初识requirejs](http://www.runoob.com/w3cnote/requirejs-tutorial-1.html)

[菜鸟教程：JS模块化工具requirejs教程(二)：基本知识](http://www.runoob.com/w3cnote/requirejs-tutorial-2.html)

注：本文绝大部分摘自[阮一峰：Javascript模块化编程（三）：require.js的用法](http://www.ruanyifeng.com/blog/2012/11/require_js.html)
