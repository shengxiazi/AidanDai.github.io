---
layout: post
title:  "margin collapsing 和 BFC"
date:   2016-03-17
categories: css
tags: css
---

BFC 已经是一个耳听熟闻的词语了，网上有许多关于 BFC 的文章，介绍了如何触发 BFC 以及 BFC 的一些用处（如清浮动，防止 margin 重叠等）。虽然我知道如何利用 BFC 解决这些问题，但当别人问我 BFC 是什么，我还是不能很有底气地解释清楚。于是这两天仔细阅读了许多文章并查阅W3C标准来全面地理解BFC。

## 前言：谈外margin collapsing

### 1、margin collapsing现象

BFC（block formatting context）中相邻的两个block-level的盒，上一个box的下边距会跟下一个box的上边距发生叠加，即两者取最大的，而不是相加：

example:

```html
<!doctype HTML>
<html>
<head>
<style type="text/css">

    #green {
        margin:10px 10px 10px 10px
    }
    #blue {
        margin:10px 10px 10px 10px
    }
    #red {
        margin:10px 10px 10px 10px
    }

</style>
</head>
<body>

<div id="green" style="background:lightgreen;height:100px;width:100px;"></div>
<div id="blue" style="background:lightblue;height:100px;width:100px;"></div>
<div id="red" style="background:pink;height:100px;width:100px;"></div>

</body>
</html>
```

### 2、margin collapsing与body的writing-mode息息相关

example:

```html
<!doctype HTML>
<html>
<head>
<style type="text/css">

    #green {
        margin:10px 10px 10px 10px
    }
    #blue {
        margin:10px 10px 10px 10px
    }
    #red {
        margin:10px 10px 10px 10px
    }
    body {
        writing-mode:tb-rl;
    }

</style>
</head>
<body>

<div id="green" style="background:lightgreen;height:100px;width:100px;"></div>
<div id="blue" style="background:lightblue;height:100px;width:100px;"></div>
<div id="red" style="background:pink;height:100px;width:100px;"></div>

</body>
</html>
```

### 3、margin collapsing仅仅发生在BFC

当把元素的display属性设为inline-block，它们都位于IFC当中，所以不论水平还是竖直方向，都不会发生叠加：

example:

```html
<!doctype HTML>
<html>
<head>
<style type="text/css">

    #green {
        margin:10px 10px 10px 10px;
        display:inline-block;
    }
    #blue {
        margin:10px 10px 10px 10px;
        display:inline-block; 
    }
    #red {
        margin:10px 10px 10px 10px;
        display:inline-block;
    }
  
</style>
</head>
<body>
<div id="green" style="background:lightgreen;height:100px;width:100px;"></div>
<div id="blue" style="background:lightblue;height:100px;width:100px;"></div>
<br/>
<div id="red" style="background:pink;height:100px;width:100px;"></div>

</body>
</html>
```

### 4、margin collapsing不发生在floats

当把元素的float属性设为left，它们都不在正常流中，更不在BFC中了，同样不论水平还是竖直方向，都不会发生叠加：

```html 
<!doctype HTML>
<html>
<head>
<style type="text/css">

    #green {
        margin:10px 10px 10px 10px;
        float:left;
    }
    #blue {
        margin:10px 10px 10px 10px;
        float:left; 
    }
    #red {
        margin:10px 10px 10px 10px;
        clear:left;
        float:left;
    }
  
</style>
</head>
<body>

<div id="green" style="background:lightgreen;height:100px;width:100px;"></div>
<div id="blue" style="background:lightblue;height:100px;width:100px;"></div>

<div id="red" style="background:pink;height:100px;width:100px;"></div>

</body>
</html>
```

### 5、margin collapsing可能跨元素

```html
<!doctype HTML>
<html>
<head>
<style type="text/css">

    #green {
        margin:10px 10px 10px 10px;
    }
    #blue {
        margin:5px 10px 10px 10px;
    }
    #red {
        margin:10px 10px 10px 10px;
    }
  
</style>
</head>
<body>

<div id="green" style="background:lightgreen;height:100px;width:100px;">
</div>
<div id="blue" style="background:lightblue;height:100px;width:100px;">
    <div id="red" style="background:pink;height:100px;width:100px;"></div>
</div>

</body>
</html>
```

### 6、margin collapsing不能跟padding(内边距)折叠

有padding(内边距)的时候，上一节的情况不发生：

```html
<!doctype HTML>
<html>
<head>
<style type="text/css">

    #green {
        margin:10px 10px 10px 10px;
    }
    #blue {
        margin:10px 10px 10px 10px;
        padding:10px 10px 10px 10px;
    }
    #red {
        margin:10px 10px 10px 10px;
    }
  
</style>
</head>
<body>

<div id="green" style="background:lightgreen;height:100px;width:100px;">
</div>
<div id="blue" style="background:lightblue;height:100px;width:100px;">
    <div id="red" style="background:pink;height:100px;width:100px;"></div>
</div>

</body>
</html>
```

### 7、margin collapsing不能跟跨越BFC

当一个容器在其内部创建新的BFC时，跨元素折叠的情况也不会发生。

```html
<!doctype HTML>
<html>
<head>
<style type="text/css">

    #green {
        margin:10px 10px 10px 10px;
    }
    #blue {
        margin:10px 10px 10px 10px;
        overflow:hidden;
    }
    #red {
        margin:10px 10px 10px 10px;
    }
  
</style>
</head>
<body>

<div id="green" style="background:lightgreen;height:100px;width:100px;">
</div>
<div id="blue" style="background:lightblue;height:100px;width:100px;">
    <div id="red" style="background:pink;height:100px;width:100px;"></div>
</div>

</body>
</html>
```

## 一、BFC是什么？

### BFC（Block Formatting Context）-块级格式化范围。

是 W3C CSS 2.1 规范中的一个概念，它决定了元素如何对其内容进行定位，以及与其他元素的关系和相互作用。当涉及到可视化布局的时候，Block Formatting Context提供了一个环境，HTML元素在这个环境中按照一定规则进行布局。一个环境中的元素不会影响到其它环境中的布局。比如浮动元素会形成BFC，浮动元素内部子元素的主要受该浮动元素影响，两个浮动元素之间是互不影响的。这里有点类似一个BFC就是一个独立的行政单位的意思。也可以说BFC就是一个作用范围。可以把它理解成是一个独立的容器，并且这个容器的里box的布局，与这个容器外的毫不相干。

另一个通俗点的解释是：在普通流中的 Box(框) 属于一种 formatting context(格式化上下文) ，类型可以是 block ，或者是 inline ，但不能同时属于这两者。并且， Block boxes(块框) 在 block formatting context(块格式化上下文) 里格式化， Inline boxes(块内框) 则在 inline formatting context(行内格式化上下文) 里格式化。任何被渲染的元素都属于一个 box ，并且不是 block ，就是 inline 。即使是未被任何元素包裹的文本，根据不同的情况，也会属于匿名的 block boxes 或者 inline boxes。所以上面的描述，即是把所有的元素划分到对应的 formatting context 里。

### 一般表现规则

1、在创建了 Block Formatting Context 的元素中，其子元素按文档流一个接一个地放置。垂直方向上他们的起点是一个包含块的顶部，两个相邻的元素之间的垂直距离取决于 ‘margin’ 特性。

- 根据 [CSS 2.1 8.3.1 Collapsing margins](http://www.w3.org/TR/CSS21/box.html#collapsing-margins) 第一条，两个相邻的普通流中的块框在垂直位置的空白边会发生折叠现象。也就是处于同一个BFC中的两个垂直窗口的margin会重叠。

- 根据 [CSS 2.1 8.3.1 Collapsing margins](http://www.w3.org/TR/CSS21/box.html#collapsing-margins)第三条，生成 block formatting context 的元素不会和在流中的子元素发生空白边折叠。所以解决这种问题的办法是要为两个容器添加具有BFC的包裹容器。(后面会讨论怎么产生BFC)

example:

```html
<!DOCTYPE html>
<html>
<head>
	<title>BFC</title>
</head>
	<style type="text/css">
		div{
			width: 200px;
			height: 200px;
			margin: 40px;
			padding: 40px;
		}
		span{
			display: block;
			width: 80px;
			height: 80px;
			margin: 10px;
		}
		div span:nth-child(1){
			background-color: red;
		}
		div span:nth-child(2){
			background-color: blue;
		}
	</style>
<body>
	<div>
		<span></span>
		<span></span>
	</div>
</body>
</html>
```

2、在 Block Formatting Context 中，每一个元素左外边与包含块的左边相接触（对于从右到左的格式化，右外边接触右边）， 即使存在浮动也是如此（尽管一个元素的内容区域会由于浮动而压缩），除非这个元素也创建了一个新的 Block Formatting Context 。

3、Block Formatting Context就是页面上的一个隔离的独立容器，容器里面的子元素不会在**布局上**影响到外面的元素，反之也是如此。

4、根据 CSS 2.1 9.5 Floats 中的描述，创建了 Block Formatting Context 的元素不能与浮动元素重叠。

- 表格的 border-box、块级的替换元素、或是在普通流中创建了新的 block formatting context（如元素的 'overflow' 特性不为 'visible' 时）的元素不可以与位于相同的 block formatting context 中的浮动元素相重叠。

5 、当容器有足够的剩余空间容纳 BFC 的宽度时，所有浏览器都会将 BFC 放置在浮动元素所在行的剩余空间内。

6、 在 IE6 IE7 IE8 Chrome Opera 中，当 BFC 的宽度介于 "容器剩余宽度" 与 "容器宽度" 之间时，BFC 会显示在浮动元素的下一行；在 Safari 中，BFC 则仍然保持显示在浮动元素所在行，并且 BFC 溢出容器；在 Firefox 中，当容器本身也创建了 BFC 或者容器的 'padding-top'、'border-top-width' 这些特性不都为 0 时表现与 IE8(S)、Chrome 类似，否则表现与 Safari 类似。 

**经验证，最新版本的浏览中只有firefox会在同一行显示，其它浏览器均换行。** 

**Aidan注：没明白？**

7、 在 IE6 IE7 IE8 Opera 中，当 BFC 的宽度大于 "容器宽度" 时，BFC 会显示在浮动元素的下一行；在 Chrome Safari 中，BFC 则仍然保持显示在浮动元素所在行，并且 BFC 溢出容器；在 Firefox 中，当容器本身也创建了 BFC 或者容器的 'padding- top'、'border-top-width' 这些特性不都为 0 时表现与 IE8(S) 类似，否则表现与 Chrome 类似。

**经验证，最新版本的浏览中只有firefox会在同一行显示，其它浏览器均换行。**

8、根据CSS2.1 规范第10.6.7部分的高度计算规则，在计算生成了 block formatting context 的元素的高度时，其浮动子元素应该参与计算。

下面先看一个比较典型的例子：

```html
<!DOCTYPE HTML>
	<html>
	<head>
		<meta charset="utf8">
		<title>BFC</title>
		<style>
			* { padding:0; margin:0; }
			#red, 
			#yellow, 
			#orange, 
			#green { width:100px; height:100px; float:left; }
			#red { background-color:red; }
			#yellow { background-color:yellow; }
			#orange { background-color:orange; }
			#green { background-color:green; }
		</style>
	</head>
	<body>
		<div id="c1">
		    <div id="red"></div>
		    <div id="yellow"></div>
		</div>
		<div id="c2">
		    <div id="orange"></div>
		    <div id="green"></div>
		</div>
		<p>Here is the text!</p>
	</body>
</html>
```

![bfc-example1](/asset/images/article/bfc-example1.jpg)

该段代码本意要形成两行两列的布局，但是由于#red，#yellow，#orange，#green四个div在同一个布局环境BFC中，因此虽然它们位于两个不同的div（#c1和#c2）中，但仍然不会换行，而是一行四列的排列。

若要使之形成两行两列的布局，就要创建两个不同的布局环境，也可以说要创建两个BFC。那到底怎么创建BFC呢？

## 二、如何产生BFC

当一个HTML元素满足下面条件的任何一点，都可以产生Block Formatting Context：

- float的值不为none。

- overflow的值不为visible。

- display的值为table-cell, table-caption, inline-block中的任何一个。

- position的值不为relative和static。

上面的例子，我再加两行代码，创建两个BFC：

```css
#c1{overflow:hidden;}
#c2{overflow:hidden;}
```
效果如下：

![bfc-example2](/asset/images/article/bfc-example2.jpg)

上面创建了两个布局环境BFC。内部子元素的左浮动不会影响到外部元素。所以#c1和#c2没有受浮动的影响，仍然各自占据一行！

### 1、产生BCF的差异

先看下面这个例子：

```html
<!DOCTYPE html>
<html>  
<head> 
	<meta charset="utf-8">
	<title>常用产生BCF方法的差异</title>
	<style> 
		html, body { 
			height: 100%; 
			width: 100%; 
			margin: 0; 
			padding: 0; 
		}
		section{
			margin: 50px 20px;
			float: left;
			height: 330px;
			background-color: red;
		}
		.first{
		  	margin: 40px;
		  	background-color: lightgreen;
		  	width: 100px;
		  	height: 100px;
		}
		ul{
		    margin: 20px 40px;
		    background-color: lightblue;
		}
		li{
		  	margin: 30px;
		  	width: 100px;
		  	height: 20px;
		}
		section:nth-child(2) ul{
			overflow: hidden;
		}
		section:nth-child(3) ul{
			float: left;
		}
		section:nth-child(4) ul{
			display:inline-block;
		}
		section:nth-child(5) ul{
			position: absolute;
		}
	</style> 
</head> 
	<body>
		<section>
			<div class="first"></div>
			<ul>
				<li>1</li>
				<li>2</li>
				<li>3</li>
			</ul>
		</section>
		<section>
			<div class="first"></div>
			<ul>
				<li>1</li>
				<li>2</li>
				<li>3</li>
			</ul>
		</section>
		<section>
			<div class="first"></div>
			<ul>
				<li>1</li>
				<li>2</li>
				<li>3</li>
			</ul>
		</section>
		<section>
			<div class="first"></div>
			<ul>
				<li>1</li>
				<li>2</li>
				<li>3</li>
			</ul>
		</section>
		<section>
			<div class="first"></div>
			<ul>
				<li>1</li>
				<li>2</li>
				<li>3</li>
			</ul>
		</section>
	</body> 
</html>
```

效果图如下：

![create-bfc-different](/asset/images/article/create-bfc-different.jpg)


从图中可以看出，overflow方法与position方法产生的BCF与其他两种方法明显不同，下面逐个分析：

1、overflow方法：

- 从例子来看overflow方法**只会影响子元素的布局，而不会影响本身元素的布局**，可能就是只会使浏览器重新渲染局部布局，而不是整个页面全部重新渲染

2、position方法：

- 使元素脱离文档流，会造成布局的改变；不建议使用

3、float方法：

- 从例子来看float方法**不仅会影响子元素的布局，而且会影响本身元素的布局**，可能是整个页面全部重新渲染

4、display方法：

从例子来看float方法**不仅会影响子元素的布局，而且会影响本身元素的布局**，可能是整个页面全部重新渲染


## 三、BFC能用来做什么？

### 1、不和浮动元素重叠

如果一个浮动元素后面跟着一个非浮动的元素，那么就会产生一个覆盖的现象，很多自适应的两栏布局就是这么做的。

看下面一个例子：

```html
<!DOCTYPE HTML>
<html>
	<head>
		<meta charset="utf8">
		<title>BFC</title>
		<style>
			html,body {height:100%; }
			*{ 
				padding:0; margin:0; 
				color:#fff; 
				text-decoration:none; 
				list-style:none; 
				font-family:"微软雅黑" 
			}
			.aside{background:#f00;width:170px;float:left;height:300px;}
			.main{background:#090;height:100%;}
		</style>
	</head>
	<body>
		<div class="aside"></div>
		<div class="main"></div>
	</body>
</html>
```

效果图如下：

![bfc-example3](/asset/images/article/bfc-example3.jpg)

很明显，.aside和.mian重叠了。试分析一下，由于两个box都处在同一个BFC中，都是以BFC边界为起点，如果两个box本身都具备BFC的话，会按顺序一个一个排列布局，现在.main并不具备BFC，按照规则2，内部元素都会从左边界开始，除非它本身具备BFC，按上面规则4拥有BFC的元素是不可以跟浮动元素重叠的，所以只要为.mian再创建一个BFC,就可以解决这个重叠的问题。上面已经说过创建BFC的方法，可以根据具体情况选用不同的方法，这里我选用的是加overflow:hidden。

**由于ie的原因需要再加一个解发haslayout的zoom:1，有关haslayout后面会讲到。**

### 2、清除元素内部浮动

只要把父元素设为BFC就可以清理子元素的浮动了，最常见的用法就是在父元素上设置overflow: hidden样式，对于IE6加上zoom:1就可以了(IE Haslayout)。

看下面例子：

```html
<!DOCTYPE HTML>
<html>
<head>
	<meta http-equiv="Content-Type" content="text/html; charset=utf8">
	<title>BFC</title>
	<style>
		html,body {height:100%; }
		* { 
			padding:10px; 
			margin:0; 
			color:#000; 
			text-decoration:none; 
			list-style:none; 
			font-family:"微软雅黑" 
		}
		.outer{width:300px;border:1px solid #666;padding:10px;}
		.innerLeft{height:100px;width:100px;float:left;background:#f00;}
		.innerRight{height:100px;width:100px;float:right;background:#090;}
	</style>
</head>

<body>
	<div class="outer">
		<div class="innerLeft"></div>
		<div class="innerRight"></div>
	</div>
</body>
</html>
```

效果图如下：

![bfc-example4](/asset/images/article/bfc-example4.jpg)

根据 [CSS2.1 规范第 10.6.3](http://www.w3.org/TR/CSS21/visudet.html#normal-block) 部分的高度计算规则，在进行普通流中的块级非替换元素的高度计算时，浮动子元素不参与计算。

同时 [CSS2.1 规范第10.6.7](http://www.w3.org/TR/CSS2/visudet.html#root-height)部分的高度计算规则，在计算生成了 block formatting context 的元素的高度时，其浮动子元素应该参与计算。

所以，触发外部容器BFC，高度将重新计算。比如给outer加上属性overflow:hidden触发其BFC。

### 3、解决上下相邻两个元素重叠

 看下面例子：

```html
<!DOCTYPE HTML>
<html>
<head>
	<meta http-equiv="Content-Type" content="text/html; charset=utf8">
	<title>无标题文档</title>
	<style>
		html,body {height:100%; }
		* { 
			padding:0; 
			margin:0; 
			color:#fff; 
			text-decoration:none; 
			list-style:none; 
			font-family:"微软雅黑" 
		}
		.rowone{
			background:#f00;
			height:100px;
			margin-bottom:20px;
			overflow:hidden;
		}
		.rowtow{
			background:#090;
			height:100px;
			margin-top:20px;
			position:relative
		}
	</style>
</head>

<body>
	<div class="rowone">
	</div>
	<div class="rowtow">
	</div>
</body>
</html>
```

效果如下：

![bfc-example5](/asset/images/article/bfc-example5.jpg)

根据 [CSS 2.1 8.3.1 Collapsing margins](http://www.w3.org/TR/CSS21/box.html#collapsing-margins) 第一条，两个相邻的普通流中的块框在垂直位置的空白边会发生折叠现象。也就是处于同一个BFC中的两个垂直窗口的margin会重叠。

根据 [CSS 2.1 8.3.1 Collapsing margins](http://www.w3.org/TR/CSS21/box.html#collapsing-margins) 第三条，生成 block formatting context 的元素不会和在流中的子元素发生空白边折叠。所以解决这种问题的办法是要为两个容器添加具有BFC的包裹容器。

所以解这个问题的办法就是,把两个容器分别放在两个据有BFC的包裹容器中，IE里就是触发layout的两个包裹容器中！

```html
<!DOCTYPE HTML>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf8">
	<title>无标题文档</title>
	<style>
		html, body { height:100%; }
		* { 
			padding:0; 
			margin:0; 
			color:#fff; 
			text-decoration:none; 
			list-style:none; 
			font-family:"微软雅黑" 
		}
		.mg {overflow:hidden; }
		.rowone { background:#f00; height:100px; margin-bottom:20px; }
		.rowtow { background:#090; height:100px; margin-top:20px; }
	</style>
</head>

<body>
	<div class="mg">
		<div class="rowone">
		</div>
	</div>
	<div class="mg">
		<div class="rowtow">
		</div>
	</div>
</body>
</html>
```

效果如下：

![bfc-example6](/asset/images/article/bfc-example6.jpg)

## 四、什么是IE的haslayout

上面的例子中我们用到了IE的zoom:1;实际上是触发了IE的layout。Layout 是 IE 浏览器渲染引擎的一个内部组成部分。在 IE 浏览器中，一个元素要么自己对自身的内容进行组织和计算大小， 要么依赖于包含块来计算尺寸和组织内容。为了协调这两种方式的矛盾，渲染引擎采用了 ‘hasLayout’ 属性，属性值可以为 true 或 false。 当一个元素的 ‘hasLayout’ 属性值为 true 时，我们说这个元素有一个布局（layout），或拥有布局。可以通过 hasLayout 属性来判断一个元素是否拥有 layout ，

如 object.currentStyle.hasLayout 。

hasLayout 与 BFC 有很多相似之处，但 hasLayout 的概念会更容易理解。在 Internet Explorer 中，元素使用“布局”概念来控制尺寸和定位，分为拥有布局和没有布局两种情况，拥有布局的元素由它控制本身及其子元素的尺寸和定位，而没有布局的元素则通过父元素（最近的拥有布局的祖先元素）来控制尺寸和定位，而一个元素是否拥有布局则由 hasLayout 属性告知浏览器，它是个布尔型变量，true 代表元素拥有布局，false 代表元素没有布局。简而言之，hasLayout 只是一个 IE 下专有的属性，hasLayout 为 true 的元素浏览器会赋予它一系列的效果。

**特别注意的是，hasLayout 在 IE 8 及之后的 IE 版本中已经被抛弃，所以在实际开发中只需针对 IE 8 以下的浏览器为某些元素触发 hasLayout。**

## 五、怎样触发layout

一个元素触发 hasLayout 会影响一个元素的尺寸和定位，这样会消耗更多的系统资源，因此 IE 设计者默认只为一部分的元素触发 hasLayout （即默认有部分元素会触发 hasLayout ，这与 BFC 基本完全由开发者通过特定 CSS 触发并不一样），这部分元素如下：

<pre>
&lt;html&gt;, &lt;body&gt;
&lt;table&gt;, &lt;tr&gt;, &lt;th&gt;, &lt;td&gt;
&lt;img&gt;
&lt;hr&gt;
&lt;input&gt;, &lt;button&gt;, &lt;select&gt;, &lt;textarea&gt;, &lt;fieldset&gt;, &lt;legend&gt;
&lt;iframe&gt;, &lt;embed&gt;, &lt;object&gt;, &lt;applet&gt;
&lt;marquee&gt;
</pre>


除了 IE 默认会触发 hasLayout 的元素外，Web 开发者还可以使用特定的 CSS 触发元素的 hasLayout 。

通过为元素设置以下任一 CSS ，可以触发 hasLayout （即把元素的 hasLayout 属性设置为 true）。

```css
display: inline-block
height: (除 auto 外任何值)
width: (除 auto 外任何值)
float: (left 或 right)
position: absolute | fixed
writing-mode: tb-rl
zoom: (除 normal 外任意值)
min-height: (任意值)
min-width: (任意值)
max-height: (除 none 外任意值)
max-width: (除 none 外任意值)
overflow: (除 visible 外任意值，仅用于块级元素)
overflow-x: (除 visible 外任意值，仅用于块级元素)
overflow-y: (除 visible 外任意值，仅用于块级元素)
position: 
```

对于内联元素(可以是默认被浏览器认为是内联元素的 span 元素，也可以是设置了 display: inline 的元素)，width 和 height 只在 IE5.x 下和 IE6 或更新版本的 quirks 模式下能触发元素的 hasLayout ，但是对于 IE6，如果浏览器运行于标准兼容模式下，内联元素会忽略 width 或 height 属性，所以设置 width 或 height 不能在此种情况下令该元素触发 hasLayout 。但 zoom 除了在 IE 5.0 中外，总是能触发 hasLayout 。zoom 用于设置或检索元素的缩放比例，为元素设置 zoom: 1 既可以触发元素的 hasLayout 同时不会对元素造成多余的影响。**因此综合考虑浏览器之间的兼容和对元素的影响， 建议使用 zoom: 1 来触发元素的 hasLayout **。

## 六、能解决的问题

hasLayout表现出来的特性跟BFC很相似，所以可以认为是IE中的BFC。上面的规则几乎都遵循，所以上面的问题在IE里都可以通过触发hasLayout来解决。

虽然 hasLayout 也会像 BFC 那样影响着元素的尺寸和定位，但它却又不是一套完整的标准，并且由于它默认只为某些元素触发，这导致了 IE 下很多前端开发的 bugs ，触发 hasLayout 更大的意义在于解决一些 IE 下的 bugs ，而不是利用它的一些“副作用”来达到某些效果。另外由于触发 hasLayout 的元素会出现一些跟触发 BFC 的元素相似的效果，因此为了统一元素在 IE 与支持 BFC 的浏览器下的表现，Kayo 建议为触发了 BFC 的元素同时触发 hasLayout ，当然还需要考虑实际的情况，也有可能只需触发其中一个就可以达到表现统一，下面会举例介绍。

这里首先列出触发 hasLayout 元素的一些效果：

### 1、阻止外边距折叠(lt < IE6)

```html
<!DOCTYPE HTML>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf8">
	<title> hasLayout(IE)</title>
	<style>
		html, body { height:100%; }
		* { 
			padding:0; 
			margin:0; 
			color:#fff; 
			text-decoration:none; 
			list-style:none; 
			font-family:"微软雅黑" 
		}
		.mg {zoom:1}
		.rowone { background:#f00; height:100px; margin-bottom:20px; }
		.rowtow { background:#090; height:100px; margin-top:20px; }
	</style>
</head>

<body>
	<div class="mg">
		<div class="rowone">
		</div>
	</div>
	<div class="mg">
		<div class="rowtow">
		</div>
	</div>
</body>
</html>
```

## 补充：到底什么是BFC、IFC、GFC和FFC

CSS2.1中只有BFC和IFC, CSS3中才有GFC和FFC。

### What's FC？

一定不是KFC，FC的全称是：Formatting Contexts，是W3C CSS2.1规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。

### BFC

BFC(Block Formatting Contexts)直译为"块级格式化上下文"。Block Formatting Contexts就是页面上的一个隔离的渲染区域，容器里面的子元素不会在布局上影响到外面的元素，反之也是如此。

### IFC

IFC(Inline Formatting Contexts)直译为"内联格式化上下文"，IFC的line box（线框）高度由其包含行内元素中最高的实际高度计算而来（不受到竖直方向的padding/margin影响)

IFC中的line box一般左右都贴紧整个IFC，但是会因为float元素而扰乱。float元素会位于IFC与与line box之间，使得line box宽度缩短。 同个ifc下的多个line box高度会不同。 IFC中时不可能有块级元素的，当插入块级元素时（如p中插入div）会产生两个匿名块与div分隔开，即产生两个IFC，每个IFC对外表现为块级元素，与div垂直排列。

那么IFC一般有什么用呢？

**水平居中**：当一个块要在环境中水平居中时，设置其为inline-block则会在外层产生IFC，通过text-align则可以使其水平居中。

**垂直居中**：创建一个IFC，用其中一个元素撑开父元素的高度，然后设置其vertical-align:middle，其他行内元素则可以在此父元素下垂直居中。

### GFC

GFC(GridLayout Formatting Contexts)直译为"网格布局格式化上下文"，当为一个元素设置display值为grid的时候，此元素将会获得一个独立的渲染区域，我们可以通过在网格容器（grid container）上定义网格定义行（grid definition rows）和网格定义列（grid definition columns）属性各在网格项目（grid item）上定义网格行（grid row）和网格列（grid columns）为每一个网格项目（grid item）定义位置和空间。 

那么GFC有什么用呢，和table又有什么区别呢？首先同样是一个二维的表格，但GridLayout会有更加丰富的属性来控制行列，控制对齐以及更为精细的渲染语义和控制。

### FFC

FFC(Flex Formatting Contexts)直译为"自适应格式化上下文"，display值为flex或者inline-flex的元素将会生成自适应容器（flex container），可惜这个牛逼的属性只有谷歌和火狐支持，不过在移动端也足够了，至少safari和chrome还是OK的，毕竟这俩在移动端才是王道。

Flex Box 由伸缩容器和伸缩项目组成。通过设置元素的 display 属性为 flex 或 inline-flex 可以得到一个伸缩容器。设置为 flex 的容器被渲染为一个块级元素，而设置为 inline-flex 的容器则渲染为一个行内元素。

伸缩容器中的每一个子元素都是一个伸缩项目。伸缩项目可以是任意数量的。伸缩容器外和伸缩项目内的一切元素都不受影响。简单地说，Flexbox 定义了伸缩容器内伸缩项目该如何布局。

**参考博文：**

**[穆乙: 关于Block Formatting Context－－BFC和IE的hasLayout](http://www.cnblogs.com/pigtail/archive/2013/01/23/2871627.html)**

**[小科普：到底什么是BFC、IFC、GFC和FFC，次奥？](http://www.xiaomeiti.com/note/3608)**

**[谈外margin collapsing(外边距叠加)](http://www.cnblogs.com/winter-cn/archive/2012/11/16/2772562.html)**

**[我对BFC的理解](http://www.cnblogs.com/dojo-lzz/p/3999013.html)**