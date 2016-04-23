---
layout: post
title:  "【总结】深入理解 JavaScript 中的事件处理"
date: 2016-04-20
categories: javascript
tags: javascript
keyword: "DOM, event, HTML DOM 事件,【笔记】深入理解 JavaScript 中的事件处理"
author: Aidan Dai
---

与浏览器进行交互的时候浏览器就会触发各种事件。比如当我们打开某一个网页的时候，浏览器加载完成了这个网页，就会触发一个 load 事件；当我们点击页面中的某一个“地方”，浏览器就会在那个“地方”触发一个 click 事件。这样，我们就可以编写 JavaScript，通过监听某一个事件，来实现某些功能扩展。例如监听 load 事件，显示欢迎信息，那么当浏览器加载完一个网页之后，就会显示欢迎信息。下面就来介绍一下事件。

## 前言、HTML DOM 知识结构

自己挑选重要的知识点做个总(PS：嵌入看不清)

[HTML DOM 事件](http://naotu.baidu.com/file/2cf7f66968da0beb8a7c55fb0c8c15c7?token=e1e22c116e42a4f6)

## 一、什么是事件？

事件(Event)是JavaScript应用跳动的心脏 ，也是把所有东西粘在一起的胶水。当我们与浏览器中 Web 页面进行某些类型的交互时，事件就发生了。事件可能是用户在某些内容上的点击、鼠标经过某个特定元素或按下键盘上的某些按键。事件还可能是 Web 浏览器中发生的事情，比如说某个 Web 页面加载完成，或者是用户滚动窗口或改变窗口大小。

通过使用 JavaScript ，你可以监听特定事件的发生，并规定让某些事件发生以对这些事件做出响应。

## 二、今天的事件

在漫长的演变史，我们已经告别了内嵌式的事件处理方式（直接将事件处理器放在 HTML 元素之内来使用）。今天的事件，它已是DOM的重要组成部分，遗憾的是， IE继续保留它最早在IE4.0中实现的事件模型，以后的IE版本中也没有做太大的改变，这也就是说IE还是使用的是一种专有的事件模型（冒泡型）,而其它的主流浏览器直到DOM 级别 3 规定定案后，才陆陆续续支持DOM标准的事件处理模型 — 捕获型与冒泡型。

**历史原因是：**W3C 规范 在DOM 级别 1中并没有定义任何的事件，直到发布于 2000 年 11 月 的DOM 级别 2 才定义了一小部分子集，DOM 级别 2中已经提供了提供了一种更详细的更细致的方式以控制 Web 页面中的事件，最后，完整的事件是在2004年 DOM 级别 3的规定中才最终定案。因为IE4是1995推出的并已实现了自己的事件模型（冒泡型），当时根本就没有DOM标准，不过在以后的DOM标准规范过程中已经把IE的事件模型吸收到了其中。

目前除IE浏览器外，其它主流的Firefox, Opera,Safari都支持标准的DOM事件处理模型。IE仍然使用自己专有的事件模型，即冒泡型，它事件模型的一部份被DOM标准采用，这点对于开发者来说也是有好处的，只有使用DOM标准，IE都共有的事件处理方式才能有效的跨浏览器。

## 三、DOM事件流

DOM(文档对象模型)结构是一个树型结构，当一个HTML元素产生一个事件时，该事件会在元素结点与根节点之间按特定的顺序传播，路径所经过的节点都会收到该事件，这个传播过程可称为DOM事件流。

事件顺序有两种类型：`事件捕捉`和`事件冒泡`。

### 1、冒泡型事件(Event Bubbling)

这是IE浏览器对事件模型的实现，也是最容易理解的，至少笔者觉得比较符合实际的。冒泡，顾名思义，事件像个水中的气泡一样一直往上冒，直到顶端。从DOM树型结构上理解，就是事件由叶子节点沿祖先结点一直向上传递直到根节点；从浏览器界面视图HTML元素排列层次上理解就是事件由具有从属关系的最确定的目标元素一直传递到最不确定的目标元素`冒泡技术`冒泡型事件的基本思想,事件按照从特定的事件目标开始到最不确定的事件目标.

### 捕获型事件(Event Capturing)

Netscape 的实现，它与冒泡型刚好相反，由DOM树最顶层元素一直到最精确的元素，这个事件模型对于开发者来说（至少是我..）有点费解，因为直观上的理解应该如同冒泡型，事件传递应该由最确定的元素，即事件产生元素开始。

![event-flow](/asset/images/article/event-flow.jpg)

### 2、DOM标准的事件模型

我们已经对上面两个不同的事件模型进行了解释和对比。DOM标准同时支持两种事件模型，即`捕获型事件`与`冒泡型事件`，但是，捕获型事件先发生。两种事件流都会触发DOM中的所有对象，从document对象开始，也在document对象结束（大部分兼容标准的浏览器会继续将事件是捕捉/冒泡延续到window对象）。

![dom-event-flow](/asset/images/article/dom-event-flow.jpg)

如图：首先是捕获式传递事件，接着是冒泡式传递，所以，如果一个处理函数既注册了捕获型事件的监听，又注册冒泡型事件监听，那么在DOM事件模型中它就会被调用两次。

DOM标准的事件模型最独特的性质是,`文本节点也会触发事件(在IE不会)`。

![dom-event-flow-example1](/asset/images/article/dom-event-flow-example1.png)


### 3、事件传送

为了更好的说明DOM标准中的事件流原理，我们把它放在“事件传送”小结里来更具体的解释。

显然，如果为一个超链接添加了click事件监听器，那么当该链接被点击时该事件监听器就会被执行。但如果把该事件监听器指派给了包含该链接的p元素或者位于DOM树顶端的document节点，那么点击该链接也同样会触发该事件监听器。这是因为事件不仅仅对触发的目标元素产生影响，它们还会对沿着DOM结构的所有元素产生影响。这就是大家所熟悉的`事件转送`。

W3C事件模型中明确地指出了事件转送的原理。事件传送可以分为3个阶段。

![dom-event-tranmit](/asset/images/article/dom-event-tranmit.png)

如图：标准的事件转送模式

- 在事件捕捉（Capturing）阶段，事件将沿着DOM树向下转送，目标节点的每一个祖先节点，直至目标节点。例如，若用户单击了一个超链接，则该单击事件将从document节点转送到html元素，body元素以及包含该链接的p元素。

在此过程中，浏览器都会检测针对该事件的捕捉事件监听器，并且运行这件事件监听器。

- 在目标（target）阶段，浏览器在查找到已经指定给目标事件的事件监听器之后，就会运行 该事件监听器。目标节点就是触发事件的DOM节点。例如，如果用户单击一个超链接，那么该链接就是目标节点（此时的目标节点实际上是超链接内的文本节点）。

- 在冒泡（Bubbling）阶段，事件将沿着DOM树向上转送，再次逐个访问目标元素的祖先节点到document节点。该过程中的每一步。浏览器都将检测那些不是捕捉事件监听器的事件监听器，并执行它们。

`并非所有的事件都会经过冒泡阶段的`

`所有的事件都要经过捕捉阶段和目标阶段，但是有些事件会跳过冒泡阶段。例如，让元素获得输入焦点的focus事件以及失去输入焦点的blur事件就都不会冒泡。`

这里有个非常经典的事件流的 [请用chrome打开：event-flow-demo](/asset/demo/event-flow.html)

## 四、事件句柄和事件接听器

### 1、事件句柄

事件句柄（又称事件处理函数，DOM称之为事件监听函数），用于响应某个事件而调用的函数称为事件处理函数。每一个事件均对应一个事件句柄，在程序执行时，将相应的函数或语句指定给事件句柄，则在该事件发生时，浏览器便执行指定的函数或语句，从而实现网页内容与用户操作的交互。当浏览器检测到某事件发生时，便查找该事件对应的事件句柄有没有被赋值，如果有，则执行该事件句柄。

我们认为响应点击事件的函数是onclick事件处理函数。以前，事件处理函数有两种分配方式：在`JavaScript`中或者在`HTML`中。

如果在JavaScript 中分配事件处理函数， 则需要首先获得要处理的对象的一引用，然后将函数赋值给对应的事件处理函数属性，请看一个简单的例子：

```javascript
var link=document.getElementById("mylink");

link.onclick=function(){
	alert("I was clicked !");
}; 
```
从我们看到的例子中，我们发现使用事件句柄很容易，不过事件处理函数名称必须是小写的，还有就是只有`在元素载入完成之后才能将事件句柄赋给元素`，不然会有异常。

关于文档载入技术，请看[《window.onload加载的多种解决方案》](http://blog.moocss.com/tutorials/javascript-tutorials/526.html) 的文章。

如果在HTML中分配事件句柄的话，则直接通过HTML属性来设置事件处理函数就行了，并在其中包含合适的脚本作为特性值就可以了，例如：

```html
<a href="/" onclick="JavaScript code here">......</a>
```

这种JavaScript代码和通过HTML的style属性直接将CSS属性赋给元素类似。这样会代码看起来一团糟，也违背了将实现动态行为的代码与显示文档静态内容的代码相分离的原则。从1998年开始，这种写法就过时了。

**这种传统的事件绑定技术，优缺点是显然的：**

- 简单方便，在HTML中直接书写处理函数的代码块，在JS中给元素对应事件属性赋值即可。

- IE与DOM标准都支持的一种方法，它在IE与DOM标准中都是在事件冒泡过程中被调用的。

- 可以在处理函数块内直接用this引用注册事件的元素，this引用的是当前元素。

- 要给元素注册多个监听器，就不能用这方法了。

### 2、事件监听器

除了前面已经介绍的简单事件句柄之外，现在大多数浏览器都内置了一些更高级的事件处理方式，即，事件监听器，这种处理方式就不受一个元素只能绑定一个事件句柄的限制。

我们已经知道了事件句柄与事件监听器的最大不同之处是使用事件句柄时一次只能插接一个事件句柄，但对于事件监听器，一次可以插接多个。

#### （1）IE下的事件监听器

IE提供的却是一种自有的，完全不同的甚至存在BUG的事件监听器，因此如果要让脚本在本浏览器中正常运行的话，就必须使用IE所支持的事件监听器。另外，Safari 浏览器中的事件监听器有时也存在一点不同。

在IE中，每个元素和window对象都有两个方法：attachEvent方法和detachEvent方法。 

```javascript
element.attachEvent("onevent",eventListener);
```

此方法的意思是在IE中要想给一个元素的事件附加事件处理函数，必须调用attachEvent方法才能创建一个事件监听器。attachEvent方法允许外界注册该元素多个事件监听器。

attachEvent接受两个参数。第一个参数是事件类型名，第二个参数eventListener是回调处理函数。这里得说明一下，有个经常会出错的地方，`IE下利用attachEvent注册的处理函数调用时this指向不再是先前注册事件的元素，这时的this为window对象。`还有一点是此方法的事件类型名称必须加上一个”on”的前缀（如onclick）。 

```javascript
element.attachEvent("onclcik",eventListener);
```

要想移除先前元素注册的事件监听器,可以使用detachEvent方法进行删除，参数相同。

#### （2）DOM标准下的事件监听器

在支持W3C标准事件监听器的浏览器中，对每个支持事件的对象都可以使用addEventListener方法。该方法既支持注册冒泡型事件处理，又支持捕获型事件处理。所以与IE浏览器中注册元素事件监听器方式有所不同的。

```javascript
//标准语法 
element.addEventListener('event', eventListener, useCapture);

//默认
element.addEventListener('event', eventListener, false);
```

addEventListener方法接受三个参数。第一个参数是事件类型名，值得注意的是，这里事件类型名称与IE的不同，事件类型名是没’on’开头的;第二个参数eventListener是回调处理函数（即监听器函数）;第三个参数注明该处理回调函数是在事件传递过程中的捕获阶段被调用还是冒泡阶段被调用 ，通常此参数通常会设置为false（为false时是冒泡）,那么，如果将其值设置为true，那就创建一个捕捉事件监听器。

移除已注册的事件监听器调用element的removeEventListener方法即可，参数相同。

```javascript
//标准语法 
element.removeEventListener('event', eventListener, useCapture);

//默认
element.removeEventListener('event', eventListener, false);
```

`通过addEventListener方法添加的事件处理函数，必须使用removeEventListener方法才能删除，而且要求参数与添加事件处理函数时addEventListener方法的参数完全一致（包括useCapture参数），否则将不能成功删除事件处理函数。`

### 3、跨浏览器的注册与移除元素事件监听器方案

我们现在已经知道，对于支持addEventListener方法的浏览器，只要需要事件监听器脚本就都需要调用addEventListener方法；而对于不支持该方法的IE浏览器，使用事件监听器时则需要调用attachEvent方法。要确保浏览器使用正确的方法其实并不困难，只需要通过一个if-else语句来检测当前浏览器中是否存在addEventListener方法或attachEvent方法即可。

这样的方式就可以实现一个跨浏览器的注册与移除元素事件监听器方案:

```javascript
/**
 * 添加事件处理程序
 * @param 	{Element} 	element 	添加事件元素
 * @param 	{string} 	type    	添加事件类型
 * @param 	{function} 	handler 	处理事件函数指针
 */
addHandler: function(element, type, handler){
	if(element.addEventListener){
		element.addEventListener(type, handler, false);
	}else if(elemtn.attachEvent){
		elemtn.attachEvent("on" + type, handler);
	}else{
		element["on" + type] = handler;
	}
}

/**
 * 移除事件处理程序
 * @param 	{Element} 	element 	移除事件元素
 * @param 	{string} 	type    	移除事件类型
 * @param 	{function} 	handler 	处理事件函数指针
 */
removeHandler: function(element, type, handler){
	if(element.removeEventListener){
		element.removeEventListener(type, handler, false);
	}else if(elemtn.detachEvent){
		elemtn.detachEvent("on" + type, handler);
	}else{
		element["on" + type] = null;
	}
}

/**
 * 获得事件
 * @param  	{object} 	event 	事件
 * @return 	{object}       		事件
 */
getEvent: function(event){
	return event ? event : window.event;
}
```

### 4、事件对象引用

为了更好的处理事件，你可以根据所发生的事件的特定属性来采取不同的操作。

如事件模型一样，IE 和其他浏览器处理方法不同：IE 使用一个叫做 event 的全局事件对象来处理对象（它可以在全局变量window.event中找到），而其它所有浏览器采用的 W3C 推荐的方式，则使用独立的包含事件对象的参数传递。

跨浏览器实现这样的功能时，最常见的问题就是获取事件本身的引用及获取该事件的目标元素的引用。

下面这段代码就为你解决了这个问题：

```javascript
/**
 * 获得事件
 * @param  	{object} 	event 	事件
 * @return 	{object}       		事件
 */
getEvent: function(event){
	return event ? event : window.event;
}

/**
 * 获得事件目标
 * @param  	{object} 	event 	事件
 * @return 	{Element}       	事件目标(例如：click 的元素)
 */
getTarget: function(event){
	return event.target || event.scrElement;
}
```

### 5、停止事件冒泡和阻止事件的默认行为

“停止事件冒泡“和”阻止浏览器的默认行为“，这两个概念非常重要，它们对复杂的应用程序处理非常有用。

#### （1）停止事件冒泡

停止事件冒泡是指，停止冒泡型事件的进一步传递（取消事件传递,不只是停止IE和DOM标准共有的冒泡型事件，我们还可以停止支持DOM标准浏览器的捕捉型事件，用stopPropagation()方法）。例如上图中的冒泡型事件传递中，在body处理停止事件传递后，位于上层的document的事件监听器就不再收到通知，不再被处理。

#### （2）阻止事件的默认行为

停止事件的默认行为是指，通常浏览器在事件传递并处理完后会执行与该事件关联的默认动作（如果存在这样的动作）。例如，如果表单中input type 属性是 “submit”，点击后在事件传播完浏览器就自动提交表单。又例如，input 元素的 keydown 事件发生并处理后，浏览器默认会将用户键入的字符自动追加到 input 元素的值中。

停止事件冒泡的处理方法：

在IE下,通过设置event对象的cancelBubble为true即可。

```javascript
function someHandle() {
   	window.event.cancelBubble = true;
}
```
DOM标准通过调用event对象的stopPropagation()方法即可。

```javascript
function someHandle(event) {
   	event.stopPropagation();
}
```

因些，跨浏览器的停止事件传递的方法是:

```javascript
/**
 * 阻止默认事件
 * @param  	{[object]} 	event 	事件
 */
preventDefault: function(event){
	if(event.preventDefault){
		event.preventDefault();
	}else{
		event.returnValue = false;
	}
}
```
 
阻止事件的默认行为的处理方法

就像事件模型和事件对象差异一样，在IE和其它所有浏览器中阻止事件的默认行为的方法也不同。

在IE下,通过设置event对象的returnValue为false即可。

```javascript
function someHandle() {
  	window.event.returnValue = false;
}
```
DOM标准通过调用event对象的preventDefault()方法即可。

```javascript
function someHandle(event) {
  	event.preventDefault();
}
````

因些，跨浏览器的取消事件传递后的默认处理方法是：

```javascript
/**
 * 停止派发事件(阻止捕获和冒泡)
 * @param  	{[object]} 	event 	事件
 */
stopPropagation: function(event){
	if(event.stopPropagation){
		event.stopPropagation();
	}else{
		event.cancelBubble = true;
	}
}
```

`完整的事件处理兼容性函数`

```javascript
/**
 * 跨浏览器简单事件处理工具对象
 * @type {Object}
 */
var eventUtil = {
	/**
	 * 添加事件处理程序
	 * @param 	{Element} 	element 	添加事件元素
	 * @param 	{string} 	type    	添加事件类型
	 * @param 	{function} 	handler 	处理事件函数指针
	 */
	addHandler: function(element, type, handler){
		if(element.addEventListener){
			element.addEventListener(type, handler, false);
		}else if(elemtn.attachEvent){
			elemtn.attachEvent("on" + type, handler);
		}else{
			element["on" + type] = handler;
		}
	},
	/**
	 * 获得事件
	 * @param  	{object} 	event 	事件
	 * @return 	{object}       		事件
	 */
	getEvent: function(event){
		return event ? event : window.event;
	},
	/**
	 * 获得事件目标
	 * @param  	{object} 	event 	事件
	 * @return 	{Element}       	事件目标(例如：click 的元素)
	 */
	getTarget: function(event){
		return event.target || event.scrElement;
	},
	/**
	 * 阻止默认事件
	 * @param  	{[object]} 	event 	事件
	 */
	preventDefault: function(event){
		if(event.preventDefault){
			event.preventDefault();
		}else{
			event.returnValue = false;
		}
	},
	/**
	 * 移除事件处理程序
	 * @param 	{Element} 	element 	移除事件元素
	 * @param 	{string} 	type    	移除事件类型
	 * @param 	{function} 	handler 	处理事件函数指针
	 */
	removeHandler: function(element, type, handler){
		if(element.removeEventListener){
			element.removeEventListener(type, handler, false);
		}else if(elemtn.detachEvent){
			elemtn.detachEvent("on" + type, handler);
		}else{
			element["on" + type] = null;
		}
	},
	/**
	 * 停止派发事件(阻止捕获和冒泡)
	 * @param  	{[object]} 	event 	事件
	 */
	stopPropagation: function(event){
		if(event.stopPropagation){
			event.stopPropagation();
		}else{
			event.cancelBubble = true;
		}
	},
	mouse: {
		/**
		 * 获得事件相关元素
		 * (针对 mouseover 和 mouseout;鼠标指针从一个元素移入另一个元素)
		 * @param  	{object} 	event 	事件
		 * @return 	{Element}       	相关元素(失去光标或获得光标元素)
		 */
		getRelateTarget: function(event){
			if(event.relateTarget){
				return event.relateTarget;
			}else if(event.toElement){
				return event.toElement;
			}else if(event.formElement){
				return event.formElement;
			}else{
				return null;
			}
		},
		wheel: {
			/**
			 * 获取鼠标事件有关所有属性
			 * @param  	{[object]} 	event 	事件
			 */
			getWheelDelta: function(event){
				if(event.wheelDelta){
					return (client.engine.opera && cilent.engine.opera < 9.5 ?
						-event.wheelDelta : event.wheelDelta);
				}else{
					return -event.detail * 40;
				}
			}
		}
	},
	keyboard: {
		/**
		 * onkeypress事件触发键值的字母代码
		 * @param  	{[object]} 	event 	事件
		 */
		getCharCode: function(event){
			if(typeof event.charCode == "Number"){
				return event.charCode;
			}else{
				return event.keyCode;
			}
		}
	}
}
```

注：[eventUtil.js长期更新](https://github.com/AidanDai/code-favorites/blob/master/javascript/eventUtil.js)


## 五、捕获型事件模型与冒泡型事件模型的应用场合

标准事件模型为我们提供了两种方案，可能很多朋友分不清这两种不同模型有啥好处，为什么不只采取一种模型。

这里抛开IE浏览器讨论（IE只有一种，没法选择）什么情况下适合哪种事件模型。

### 1、捕获型应用场合

捕获型事件传递由最不精确的祖先元素一直到最精确的事件源元素，传递方式与操作系统中的全局快捷键与应用程序快捷键相似。当一个系统组合键发生时，如果注册了系统全局快捷键监听器，该事件就先被操作系统层捕获，全局监听器就先于应用程序快捷键监听器得到通知，也就是全局的先获得控制权，它有权阻止事件的进一步传递。所以捕获型事件模型适用于作全局范围内的监听，这里的全局是相对的全局，相对于某个顶层结点与该结点所有子孙结点形成的集合范围。

例如你想作全局的点击事件监听，相对于document结点与document下所有的子结点，在某个条件下要求所有的子结点点击无效，这种情况下冒泡模型就解决不了了，而捕获型却非常适合，可以在最顶层结点添加捕获型事件监听器，伪码如下:

```javascript
function globalClickListener(event) {
   	if(canEventPass == false) {
     	//取消事件进一步向子结点传递和冒泡传递
     	event.stopPropagation();

     	//取消浏览器事件后的默认执行
     	event.preventDefault();
   	}
}
```

这样一来，当canEventPass条件为假时，document下所有的子结点click注册事件都不会被浏览器处理。

### 2、冒泡型的应用场合

可以说我们平时用的都是冒泡事件模型，因为IE只支持这模型。这里还是说说，在恰当利用该模型可以提高脚本性能。在元素一些频繁触发的事件中，如
onmousemove,onmouseover,onmouseout,如果明确事件处理后没必要进一步传递，那么就可以大胆的取消它。此外，对于子结点事件监听器的处理会对父
层监听器处理造成负面影响的，也应该在子结点监听器中禁止事件进一步向上传递以消除影响。

### 3、综合案例分析

最后结合下面HTML代码作分析:

```html
<!DOCTYPE html>
<html>
<head>
	<title></title>
</head>
<body onclick="alert('current is body');">
   	<div id="div0" onclick="alert('current is '+this.id)">
	    <div id="div1" onclick="alert('current is '+this.id)">
	       	<div id="div2" onclick="alert('current is '+this.id)">
	         	<div id="event_source" onclick="alert('current is '+this.id)" style="height:200px;width:200px;background-color:red;"></div>
	       	</div>
	    </div>
   	</div>
</body>
</html>
```

HTML运行后点击红色区域,这是最里层的DIV,根据上面说明,无论是DOM标准还是IE,直接写在html里的监听处理函数是事件冒泡传递时调用的,由最里层一直往上传递,所以会先后出现

```
current is event_source
current is div2
current is div1
current is div0
current is body
```

添加以下片段:

```javascript
var div2 = document.getElementById('div2');

EventUtil.addHandler(div2, 'click', function(event){
   	event = EventUtil.getEvent(event);
   	EventUtil.stopPropagation(event);
}, false);
```

HTML运行后点击红色区域,这是最里层的DIV,根据上面说明,无论是DOM标准还是IE,直接写在html里的监听处理函数是事件冒泡传递时调用的,由最里层一直往上传递。当点击红色区域后,根据上面说明,在泡冒泡处理期间,事件传递到div2后被停止传递了,所以div2上层的元素收不到通知,所以会先后出现:

```
current is event_source
current is div2
```

在支持DOM标准的浏览器中,添加以下代码:

```javascript
document.body.addEventListener('click', function(event){
   	event.stopPropagation();
}, true);
```

以上代码中的监听函数由于是捕获型传递时被调用的,所以点击红色区域后,虽然事件源是ID为event_source的元素,但捕获型选传递,从最顶层开始,body结点监听函数先被调用,并且取消了事件进一步向下传递,所以只会出现：

```
current is body
```

## 六、使用事件代理（Event Delegate）

### 1、什么是事件代理？

在javasript中delegate这个词经常出现，看字面的意思，代理、委托。那么它究竟在什么样的情况下使用？它的原理又是什么？在各种框架中，也经常能看到delegate相关的接口。这些接口又有什么特殊的用法呢？这篇文章就主要介绍一下javascript delegate的用法和原理，以及Dojo，jQuery等框架中delegate的接口。

首先介绍一下JavaScript的事件代理。事件代理在JS世界中一个非常有用也很有趣的功能。当我们需要对很多元素添加事件的时候，可以通过将事件添加到它们的父节点而将事件委托给父节点来触发处理函数。这主要得益于浏览器的事件冒泡机制，后面会详细介绍。下面我们具体举个例子来解释如何使用这个特性。这个例子主要取自[David Walsh](https://davidwalsh.name/)的相关文章[（How JavaScript Event Delegation Works）](http://davidwalsh.name/event-delegate)。

假设有一个 UL 的父节点，包含了很多个 Li 的子节点：

```html
<ul id="parent-list">
  <li id="post-1">Item 1</li>
  <li id="post-2">Item 2</li>
  <li id="post-3">Item 3</li>
  <li id="post-4">Item 4</li>
  <li id="post-5">Item 5</li>
  <li id="post-6">Item 6</li>
</ul>
```

当我们的鼠标移到Li上的时候，需要获取此Li的相关信息并飘出悬浮窗以显示详细信息，或者当某个Li被点击的时候需要触发相应的处理事件。我们通常的写法，是为每个Li都添加一些类似onMouseOver或者onClick之类的事件监听。

```javascript
function addListeners4Li(liNode){
    liNode.onclick = function clickHandler(){
    	//...
    };
    liNode.onmouseover = function mouseOverHandler(){
    	//...
    }
}

window.onload = function(){
    var ulNode = document.getElementById("parent-list");
    var liNodes = ulNode.getElementByTagName("Li");
    for(var i=0, l = liNodes.length; i < l; i++){
        addListeners4Li(liNodes[i]);
    }   
}
```

如果这个UL中的Li子元素会频繁地添加或者删除，我们就需要在每次添加Li的时候都调用这个addListeners4Li方法来为每个Li节点添加事件处理函数。这就添加的复杂度和出错的可能性。

更简单的方法是使用事件代理机制，当事件被抛到更上层的父节点的时候，我们通过检查事件的目标对象（target）来判断并获取事件源Li。下面的代码可以完成我们想要的效果：

```javascript
// 获取父节点，并为它添加一个click事件
document.getElementById("parent-list").addEventListener("click",function(e) {
  // 检查事件源e.targe是否为Li
  if(e.target && e.target.nodeName.toUpperCase == "LI") {
    // 真正的处理过程在这里
    console.log("List item ",e.target.id.replace("post-")," was clicked!");
  }
}, false);
```

为父节点添加一个click事件，当子节点被点击的时候，click事件会从子节点开始向上冒泡。父节点捕获到事件之后，通过判断e.target.nodeName来判断是否为我们需要处理的节点。并且通过e.target拿到了被点击的Li节点。从而可以获取到相应的信息，并作处理。

### 2、事件代理的优点

通过上面的介绍，大家应该能够体会到使用事件委托对于web应用程序带来的几个优点：

- 管理的函数变少了。不需要为每个元素都添加监听函数。对于同一个父节点下面类似的子元素，可以通过委托给父元素的监听函数来处理事件。

- 可以方便地动态添加和修改元素，不需要因为元素的改动而修改事件绑定。

- JavaScript和DOM节点之间的关联变少了，这样也就减少了因循环引用而带来的内存泄漏发生的概率。

### 3、使用事件代理

#### （1）DOM事件代理

[请看demo](/asset/demo/event-delegate.html)

#### （2）扩展：JavaScript事件代理

```javascript
var delegate = function(client, clientMethod) {
    return function() {
        console.log(arguments);
        return clientMethod.apply(client, arguments);
    }
}
var ClassA = function() {
    var _color = "red";
    return {
        getColor: function() {
            console.log("Color: " + _color);
        },
        setColor: function(color) {
            _color = color;
        }
    };
};

var a = new ClassA();

a.getColor();

a.setColor("green");
a.getColor();
console.log("执行代理！");

var d = delegate(a, a.setColor);
d("blue");
console.log("执行完毕！");

a.getColor();
```

上面的例子中，通过调用delegate()函数创建的代理函数d来操作对a的修改。这种方式尽管是使用了apply（call也可以）来实现了调用对象的转移，但是从编程模式上实现了对某些对象的隐藏，可以保护这些对象不被随便访问和修改。

## 七、事件回调函数的作用域问题

与事件绑定在一起的回调函数作用域会有问题，我们来看个例子：

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset=utf-8 />
  <title>Events in JavaScript: Removing event listeners</title>
</head>
<body>
  	<button id="element">Click Me</button>
  	<script type="text/javascript">
    	var element = document.getElementById('element');

		var user = {
		   	firstname: 'Bob',
		   	greeting: function(){
		     	alert('My name is ' + this.firstname);
		   	}
		};

  		// Attach user.greeting as a callback
  		element.addEventListener('click', user.greeting);
  	</script>
</body>
</html>
```

回调函数调用的 user.greeting 函数作用域应该是在 user 下的，本期望输出 My name is Bob 结果却输出了 My name is undefined。这是因为事件绑定函数时，该函数会以当前元素为作用域执行。为了证明这一点，我们可以为当前 element 添加属性：

```javascript
element.firstname = 'jiangshui';
```

再次点击，可以正确弹出 My name is jiangshui。那么我们来解决一下这个问题。

### 1、使用匿名函数

我们为回调函数包裹一层匿名函数。

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset=utf-8 />
  <title>Events in JavaScript: Removing event listeners</title>
</head>
<body>
  	<button id="element">Click Me</button>
  	<script type="text/javascript">
    	var element = document.getElementById('element');

		var user = {
		   	firstname: 'Bob',
		   	greeting: function(){
		     	alert('My name is ' + this.firstname);
		   	}
		};

  		// Call the method with the correct
		// context inside an anonymouse function
		element.addEventListener('click', function() {
		  	user.greeting();
		});
  	</script>
</body>
</html>
```

包裹之后，虽然匿名函数的作用域被指向事件触发元素，但执行的内容就像直接调用一样，不会影响其作用域。

### 2、使用 bind 方法

使用匿名函数是有缺陷的，每次调用都包裹进匿名函数里面，增加了冗余代码等，此外如果想使用 removeEventListener 解除绑定，还需要再创建一个函数引用。Function 类型提供了 bind 方法，可以为函数绑定作用域，无论函数在哪里调用，都不会改变它的作用域。通过如下语句绑定作用域：

```javascript
user.greeting = user.greeting.bind(user);
```

这样我们就可以直接使用：

内置的事件也可以被 JavaScript 模拟触发，比如下面函数模拟触发单击事件：

```javascript
element.addEventListener('click', user.greeting);
```

## 八、用 JavaScript 模拟触发内置事件

```javascript
function simulateClick() {
  var event = new MouseEvent('click', {
    'view': window,
    'bubbles': true,
    'cancelable': true
  });
  var cb = document.getElementById('checkbox'); 
  var canceled = !cb.dispatchEvent(event);
  if (canceled) {
    // A handler called preventDefault.
    alert("canceled");
  } else {
    // None of the handlers called preventDefault.
    alert("not canceled");
  }
}
```

可以看这个 [Demo](https://developer.mozilla.org/samples/domref/dispatchEvent.html) 来了解更多。

## 九、自定义事件

我们可以自定义事件来实现更灵活的开发，事件用好了可以是一件很强大的工具，基于事件的开发有很多优势（后面介绍）。

### 1、javascript 自定义事件

与自定义事件的函数有 Event、CustomEvent 和 dispatchEvent。

直接自定义事件，使用 Event 构造函数：

```javascript
var event = new Event('build');

// Listen for the event.
elem.addEventListener('build', function (e) { ... }, false);

// Dispatch the event.
elem.dispatchEvent(event);
```

CustomEvent 可以创建一个更高度自定义事件，还可以附带一些数据，具体用法如下：

```javascript
var myEvent = new CustomEvent(eventname, options);
```
其中 options 可以是：

```javascript
{
    detail: {
        //...
    },
    bubbles: true,
    cancelable: false
}
```

其中 detail 可以存放一些初始化的信息，可以在触发的时候调用。其他属性就是定义该事件是否具有冒泡等等功能。

内置的事件会由浏览器根据某些操作进行触发，自定义的事件就需要人工触发。dispatchEvent 函数就是用来触发某个事件：

```javascript
element.dispatchEvent(customEvent);
```

上面代码表示，在 element 上面触发 customEvent 这个事件。结合起来用就是：

```javascript
// add an appropriate event listener
obj.addEventListener("cat", function(e) { process(e.detail) });

// create and dispatch the event
var event = new CustomEvent("cat", {"detail":{"hazcheeseburger":true}});

obj.dispatchEvent(event);
```

`使用自定义事件需要注意兼容性问题`，而使用 jQuery 就简单多了：

### 2、jQuery 自定义事件

```javascript
// 绑定自定义事件
$(element).on('myCustomEvent', function(){});

// 触发事件
$(element).trigger('myCustomEvent');
```

此外，你还可以在触发自定义事件时传递更多参数信息：

```javascript
$( "p" ).on( "myCustomEvent", function( event, myName ) {
  $( this ).text( myName + ", hi there!" );
});
$( "button" ).click(function () {
  $( "p" ).trigger( "myCustomEvent", [ "John" ] );
});
```

更详细的用法请看 [Introducing Custom Events](http://learn.jquery.com/events/introduction-to-custom-events/)，这里不再赘述。

## 十、在开发中应用事件

当我们操作某一个 DOM，发出一个事件，我们可以在另一个地方写代码捕获这个事件执行处理逻辑。触发操作和捕获处理操作是分开的。我们可以根据这个特性来对程序解耦。

### 1、用事件解耦

我们可以将一个整个的功能，分割成独立的小功能，每个小功能绑定一个事件，由一个“控制器”负责根据条件触发某个事件。这样，在外面触发这个事件，也可以调用对应功能，使其更加灵活。

在《基于 MVC 的 JavaScript Web 富应用开发》一书中，有更加具体的实例，有兴趣的朋友可以买本看看。（ps：打算看了）

### 2、发布（Publish）和订阅（Subscribe）模式

针对上面这种用法，继续抽象一下，就是发布和订阅开发模式。正如其名，这种模式有两个角色：发布者和订阅者，此外有一条信道，发布者被触发往这个信道里面发信，订阅者从这个信道里面收信，如果收到特定信件则执行某个对应的逻辑。这样，发布者和订阅者之间是完全解耦的，只有一条信道连接。这样就非常容易扩展，也不会引入额外的依赖。

这样如果需要添加新功能，只需要添加一个新的订阅者（及其执行逻辑），监听信道中某一类新的信件。再在应用中通过发布者发送一类新的信件即可。

具体实现，这里推荐 cowboy 开发的 [Tiny Pub Sub](https://github.com/cowboy/jquery-tiny-pubsub)，通过 jQuery 实现，非常简洁直观，jQuery 太赞。代码就这几行：

```javascript
(function($) {

  var o = $({});

  $.subscribe = function() {
    o.on.apply(o, arguments);
  };

  $.unsubscribe = function() {
    o.off.apply(o, arguments);
  };

  $.publish = function() {
    o.trigger.apply(o, arguments);
  };

}(jQuery));
```

定义一个对象作为信道，然后提供了三个方法，订阅者、取消订阅、发布者。


## 十一、参考资料

[Biny：理解JavaScript中的事件处理](http://www.cnblogs.com/binyong/articles/1750263.html)

[最详细的JavaScript和事件解读](http://www.igooda.cn/jzjl/20150322837.html)

[大饼卷馒头蘸米饭： JavaScript事件代理和委托（Delegation）](http://blog.csdn.net/majian_1987/article/details/8591385)

[司徒正美：JavaScript事件代理](http://www.cnblogs.com/rubylouvre/archive/2009/08/09/1542174.html)