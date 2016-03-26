---
layout: post
title:  "大行其道-JavaScript模块化开发(一)"
date:   2016-03-25
categories: javascript
tags: javascript module
---

对于那些正在构建大型应用程序，而对JavaScript不甚了解的开发者而言，他们最初必须要面对的挑战之一就是如何着手组织代码。起初只要在&lt;script&gt;标记之间嵌入几百行代码就能跑起来，不过很快代码就会变得一塌糊涂。而问题是，JavaScript没有为组织代码提供任何明显帮助。从字面上看，C++有#include，php也有include、require——而JavaScript一无所有。这迫使JavaScript编写者试验不同的约定，并使用现有的语言创建了一些切实可行的方法来组织大型JavaScript应用程序。从而实现团队协作、模块复用、单元测试等等一系列复杂的需求。

>各种模式（patterns）、工具（tools）及惯例（practices）会形成现代JavaScript的基础，它们必将来自于语言本身实现之外。

>——Rebecca Murphy

## 模块模式（The Module Pattern）

首先我们来看看Module模式的基本特征：

- 模块化，可重用
- 封装了变量和function，和全局的namaspace不接触，松耦合
- 只暴露可用public的方法，其它私有方法全部隐藏

关于Module模式，最早是由YUI的成员Eric Miraglia在4年前提出了这个概念。模块模式（Module Pattern）也是被广泛应用去组织代码、团队协作、模块复用、单元测试的复杂的需求，当然这是前辈们总结的经验，现在我们需要把它执行下去！除了代码的模块化，当然功能也许要模块化！

## 天生丽质的 Javascript

Javascript不是一种模块化编程语言，它不支持"类"（class），更遑论"模块"（module）了。（正在制定中的[ECMAScript 标准第六版](http://en.wikipedia.org/wiki/ECMAScript)，将正式支持"类"和"模块"，但还需要很长时间才能投入实用。）这是被大家诟病最多的了，但是Javascript 天生丽质，它那些丰富的特性让人们模仿其他语言的模块开发十分简单，更有超凡脱粗的一面，下面我们来讨论下 javascript 的那些特性！

### 匿名闭包

这是一切成为可能的基础，也是JavaScript最好的特性。我们将简单的创建匿名函数，并立即执行。所有函数内部代码都在闭包(closure)内。它提供了整个应用生命周期的私有和状态。

```javascript
(function(){}(
    // ... all vars and functions are in this scope only
    // still maintains access to all globals
    // 在这里想干什么就干什么
));
```

### 引入全局变量的闭包

JavaScript有一个特性叫做隐式全局变量，不管一个变量有没有用过，JavaScript解释器反向遍历作用域链来查找整个变量的var声明，如果没有找到var，解释器则假定该变量是全局变量，如果该变量用于了赋值操作的话，之前如果不存在的话，解释器则会自动创建它，这就是说在匿名闭包里使用或创建全局变量非常容易，不过比较困难的是，代码比较难管理，尤其是阅读代码的人看着很多区分哪些变量是全局的，哪些是局部的。

不过，好在在匿名函数里我们可以提供一个比较简单的替代方案，我们可以将全局变量当成一个参数传入到匿名函数然后使用，相比隐式全局变量，它又清晰又快，我们来看一个例子：

(function ($, YAHOO) {
    // 这里，我们的代码就可以使用全局的jQuery对象了，YAHOO也是一样
} (jQuery, YAHOO));

### 创建模块的闭包

有时你不只想用全局变量，但你需要先声明他们（模块的全局调用）。我们用匿名函数的返回值，很容易输出他们。这样做就完成了基本的模块模式。以下是一个完整例子：

```javascript
var MODULE = (function () {
	var my = {},
		privateVariable = 1;

	function privateMethod() {
		// ...
	}

	my.moduleProperty = 1;
	my.moduleMethod = function () {
		// ...
	};

	return my;
}());
```

注意，我们声明了一个全局模块MODULE，有两个公开属性：方法MODULE.moduleMethod和属性MODULE.moduleProperty。而且，匿名函数的闭包还维持了私有内部状态。同时学会之上的内容，我们就很容易引入需要的全局变量，和输出到全局变量。

**有了闭包，模块开发不是是不是很简单呀！**

## 实现模块模式的一些策略

对许多用户而言以上的还不足，我们可以采用以下的模式创造强大的，可扩展的结构。让我们使用MODULE模块，一个一个继续。

### 扩展

Module模式的一个限制就是所有的代码都要写在一个文件，但是在一些大型项目里，将一个功能分离成多个文件是非常重要的，因为可以多人合作易于开发。再回头看看上面的引入全局变量的例子，我们能否把MODULE自身传进去呢？答案是肯定的，我们先将MODULE自身传进去，添加一个函数属性，然后再返回就达到了我们所说的目的，上代码：

```javascript
var MODULE = (function (my) {
	my.anotherMethod = function () {
		// added method...
	};

	return my;
}(MODULE));
```

这段代码，看起来是不是有C#里扩展方法的感觉？有点类似，但本质不一样哦。同时尽管var不是必须的，但为了确保一致，我们再次使用了它，代码执行以后，MODULE下的anotherMethod就可以使用了，同时匿名函数内部的代码也依然保证了私密性和内部状态。

### 松耦合扩展

上面的例子需要我们首先创建模块，然后扩充它，这并不总是必要的。提升JavaScript应用性能最好的操作就是异步加载脚本。因而我们可以创建灵活多部分的模块，可以将他们无顺序加载，以**松耦合扩展模块**。每个文件应有如下的结构：（这得益于javascript 变量声明的提升）

```javascript
var MODULE = (function (my) {
	// add capabilities...

	return my;
}(MODULE || {}));
```

通过这样的代码，每个单独分离的文件都保证这个结构，那么我们就可以实现任意顺序的加载，所以，这个时候的var就是必须要声明的，因为不声明，其它文件读取不到哦。

### 紧耦合扩展

虽然松耦合很不错，但模块上也有些限制。最重要的，你不能安全的覆写模块属性（因为没有加载顺序）。初始化时也无法使用其他文件定义的模块属性（但你可以在初始化后运行）。紧耦合扩充意味着一组加载顺序，但是允许覆写。下面是一个例子（扩充最初定义的MODULE）：

```javascript
var MODULE = (function (my) {
	var old_moduleMethod = my.moduleMethod;

	my.moduleMethod = function () {
		// method override, has access to old through old_moduleMethod...
	};

	return my;
}(MODULE));
```

我们覆写的MODULE.moduleMethod，但依旧保持着模块其他的内部状态。
通过这种方式，我们达到了重载的目的，当然如果你想在继续在内部使用原有的属性，你可以调用old_moduleMethod来用。

### 克隆和继承

```javascript
var MODULE_TWO = (function (old) {
	var my = {},
		key;

	for (key in old) {
		if (old.hasOwnProperty(key)) {
			my[key] = old[key];
		}
	}

	var super_moduleMethod = old.moduleMethod;
	my.moduleMethod = function () {
		// override method on the clone, access to super through super_moduleMethod
	};

	return my;
}(MODULE));
```

这种方式灵活是灵活，但是也需要花费灵活的代价，其实该对象的属性对象或function根本没有被复制，只是对同一个对象多了一种引用而已，所以如果老对象去改变它，那克隆以后的对象所拥有的属性或function函数也会被改变，解决这个问题，我们就得是用递归，但递归对function函数的赋值也不好用，所以我们在递归的时候eval相应的function。不管怎么样，我还是把这一个方式放在这个帖子里了，大家使用的时候注意一下就行了。

### 跨文件共享私有对象

通过上面的例子，我们知道，如果一个MODULE分割到多个文件的话，每个文件需要保证一样的结构，也就是说每个文件匿名函数里的私有对象都不能交叉访问，那如果我们非要使用，那怎么办呢？ 我们先看一段代码：

```javascript
var MODULE = (function (my) {
	var _private = my._private = my._private || {},
		_seal = my._seal = my._seal || function () {
			delete my._private;
			delete my._seal;
			delete my._unseal;
		},
		_unseal = my._unseal = my._unseal || function () {
			my._private = _private;
			my._seal = _seal;
			my._unseal = _unseal;
		};

	// permanent access to _private, _seal, and _unseal

	return my;
}(MODULE || {}));
```

任何文件都可以对他们的局部变量_private设属性，并且设置对其他的文件也立即生效。一旦这个模块加载结束，应用会调用 MODULE\.\_seal()"上锁"，这会阻止外部接入内部的_private。如果这个模块需要再次增生，应用的生命周期内，任何文件都可以调用_unseal() ”开锁”，然后再加载新文件。加载后再次调用 \_seal()”上锁”。

### 子模块

最后一个也是最简单的使用方式，那就是创建子模块

```javascript
MODULE.sub = (function () {
	var my = {};
	// ...

	return my;
}());
```

尽管非常简单，我还是把它放进来了，因为我想说明的是子模块也具有一般实现模块的所有策略，也就是说你可以对任意子模块再次使用上面的一些策略。

## 总结

大多数策略可以互相组合成更多有用的模式。如果要我提出一个复杂应用的设计模式，我会组合**松耦合**、**私有状态**和**子模块**。

这里我还没有涉及性能，不过我有个小建议：模块模式是**性能增益**的。他简化了许多，加快代码下载。**松耦合**可以无阻塞并行下载，等价于提高下载速度。可能初始化比别的方法慢一点，但值得权衡。只要全局正确的引入，运行性能不会有任何损失，可能还因为局部变量和更少的引用，加快子模块的加载。

最后，一个例子动态加载子模块到父模块（动态创建）中。这里就不用私有状态了，其实加上也很简单。这段代码允许整个复杂分成的代码核心及其子模块等平行加载完全。

```javascript
var UTIL = (function (parent, $) {
	var my = parent.ajax = parent.ajax || {};// 引用赋值

	my.get = function (url, params, callback) {
		// ok, so I'm cheating a bit :)
		return $.getJSON(url, params, callback);
	};

	// etc...

	return parent;
}(UTIL || {}, jQuery));
```

参考文章：

[Ben Cherry：深入理解JavaScript 模块模式](http://www.oschina.net/translate/javascript-module-pattern-in-depth)

[汤姆大叔：全面解析Module模式](http://www.cnblogs.com/TomXu/archive/2011/12/30/2288372.html)

[David Padbury：JavaScript模块化开发一瞥](http://www.ituring.com.cn/article/1091)

[阮一峰：Javascript模块化编程（一）：模块的写法](http://www.ruanyifeng.com/blog/2012/10/javascript_module.html)
