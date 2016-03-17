---
layout: post
title:  "CSS实现垂直居中的常用方法"
date:   2016-03-17
categories: css
tags: css
---

在前端开发过程中，盒子居中是常常用到的。其中 ，居中又可以分为水平居中和垂直居中。水平居中是比较容易的，直接设置元素的margin: 0 auto就可以实现。但是垂直居中相对来说是比较复杂一些的。下面我们一起来讨论一下实现垂直居中的方法。


## margin and absolute

example:

```html
<!DOCTYPE html>
<html lang="zh-cmn-hans">
	<head>
	    <meta charset="UTF-8">
	    <title>vertical center 1</title>
	    <style>
		html,body{
			margin: 0;
			padding: 0;
			width: 100%;
			height: 100%;
		}
		.content{
			width: 400px;
			height: 200px;
			background-color: #ccc;
			position: absolute;
			top: 50%;
			left: 50%;
			margin: -100px 0 0 -200px;
		}
	    </style>
	</head>
	<body>
	    <div class="content"></div>
	</body>
</html>
```

## margin and relative

example:

```html
<!DOCTYPE html>
<html lang="zh-cmn-hans">
	<head>
	    <meta charset="UTF-8">
	    <title>vertical center 2</title>
	    <style>
		html,body{
			margin: 0;
			padding: 0;
			width: 100%;
			height: 100%;
		}
		.content{
			width: 400px;
			height: 200px;
			background-color: #ccc;
			position: relative;
			top: 50%;
			margin: -100px auto 0;
		}
	    </style>
	</head>
	<body>
	    <div class="content"></div>
	</body>
</html>
```

## margin and tranform

example:

```html
<!DOCTYPE html>
<html lang="zh-cmn-hans">
	<head>
	    <meta charset="UTF-8">
	    <title>vertical center 3</title>
	    <style>
		html,body{
			margin: 0;
			padding: 0;
			width: 100%;
			height: 100%;
		}
		.content{
			width: 400px;
			height: 200px;
			background-color: #ccc;
			position: relative;
			top: 50%;
			margin: 0 auto;
			transform: translateY(-50%);
		}
	    </style>
	</head>
	<body>
	    <div class="content"></div>
	</body>
</html>
```

## flex

example:

```html
<!DOCTYPE html>
<html lang="zh-cmn-hans">
	<head>
	    <meta charset="UTF-8">
	    <title>vertical center 4</title>
	    <style>
		html,body {
			width: 100%;
			height: 100%;
			margin: 0;
			padding: 0;
		}
		body {
			display: flex;
			align-items: center; /*定义body的元素垂直居中*/
			justify-content: center; /*定义body的里的元素水平居中*/
		}
		.content {
			width: 400px;
			height: 200px;
			background-color: #ccc;        
		}
	    </style>
	</head>
	<body>
	    <div class="content"></div>
	</body>
</html>
```

**参考博客：**

[渔歌：CSS实现垂直居中的常用方法](http://www.cnblogs.com/yugege/p/5246652.html)