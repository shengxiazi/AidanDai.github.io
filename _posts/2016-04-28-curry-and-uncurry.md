---
layout: post
title:  "【笔记】javascript 中的科里化和反科里化"
date: 2016-04-26
categories: javascript
tags: javascript
keyword: "科里化，反科里化，javascript 中的科里化和反科里化"
author: Aidan Dai
---

在计算机科学中，柯里化（英语：Currying），又译为卡瑞化或加里化，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。这个技术由 Christopher Strachey 以逻辑学家哈斯凯尔·加里命名的，尽管它是 Moses Schönfinkel 和 Gottlob Frege 发明的。

## 一、科里化

看了上面来自维基百科的`科里化`的解释。顾名思义，**柯里化其实本质是固定一个可以预期的参数，并返回一个特定的函数，处理批特定的需求。**这增加了函数的适用性，但同时也降低了函数的适用范围。

通用实现

```javascript
function currying(fn) {
    var slice = Array.prototype.slice,
	    // 利用闭包保留作用域
	    __args = slice.call(arguments, 1);

    return function () {
        var __inargs = slice.call(arguments);
        
        return fn.apply(null, __args.concat(__inargs));
    };
}
```

## 二、柯里化的实用性

### 1、提高适用性

【通用函数】解决了兼容性问题，但同时也会再来，使用的不便利性，不同的应用场景往，要传递很多参数，以达到解决特定问题的目的。有时候应用中，同一种规则可能会反复使用，这就可能会造成代码的重复性。

看下面一个例子：

```javascript
function square(i) {
    return i * i;
}

function dubble(i) {
    return i *= 2;
}

function map(handeler, list) {
    return list.map(handeler);
}

// 数组的每一项平方
map(square, [1, 2, 3, 4, 5]);
map(square, [6, 7, 8, 9, 10]);
map(square, [10, 20, 30, 40, 50]);
// ......

// 数组的每一项加倍
map(dubble, [1, 2, 3, 4, 5]);
map(dubble, [6, 7, 8, 9, 10]);
map(dubble, [10, 20, 30, 40, 50]);
```

例子中，创建了一个map通用函数，用于适应不同的应用场景。显然，通用性不用怀疑。同时，例子中重复传入了相同的处理函数：square和dubble。

应用中这种可能会更多。当然，通用性的增强必然带来适用性的减弱。但是，我们依然可以在中间找到一种平衡。

看下面，我们利用柯里化改造一下：

```javascript
function square(i) {
    return i * i;
}

function dubble(i) {
    return i *= 2;
}

function map(handeler, list) {
    return list.map(handeler);
}

var mapSQ = currying(map, square);
mapSQ([1, 2, 3, 4, 5]);
mapSQ([6, 7, 8, 9, 10]);
mapSQ([10, 20, 30, 40, 50]);
// ......

var mapDB = currying(map, dubble);
mapDB([1, 2, 3, 4, 5]);
mapDB([6, 7, 8, 9, 10]);
mapDB([10, 20, 30, 40, 50]);
// ......
```

我们缩小了函数的适用范围，但同时提高函数的适性。当然，也有扩展函数适用范围的方法--反柯里化，留到下一篇再讨论。

由此，可知柯里化不仅仅是提高了代码的合理性，更重的它突出一种思想---降低适用范围，提高适用性。

下面再看一个例子，一个应用范围更广泛更熟悉的例子：

```javascript
function Ajax() {
    this.xhr = new XMLHttpRequest();
}

Ajax.prototype.open = function(type, url, data, callback) {
    this.onload = function() {
        callback(this.xhr.responseText, this.xhr.status, this.xhr);
    }

    this.xhr.open(type, url, data.async);
    this.xhr.send(data.paras);
}

// 亮瞎眼，有没有！枉自己还用了这么久的Ajax
'get post'.split(' ').forEach(function(mt) {
    Ajax.prototype[mt] = currying(Ajax.prototype.open, mt);
});

var xhr = new Ajax();
xhr.get('/articles/list.php', {},
function(datas) {
    // done(datas)    
});

var xhr1 = new Ajax();
xhr1.post('/articles/add.php', {},
function(datas) {
    // done(datas)    
});
```

### 2、延迟执行

柯里化的另一个应用场景是延迟执行。不断的柯里化，累积传入的参数，最后执行。

看下面：

```javascript
var add = function() {
    var _this = this,
    _args = arguments

    function test() {
        if (!arguments.length) {
            var sum = 0,
            	len = _args.length;
            for (var i=0; i<len;i++){
            	sum += _args[i];
            }
            return sum
        } else {
            Array.prototype.push.apply(_args, arguments);
            return test;
        }
    }

    return test;
}
add(1)(2)(3)(4)();//10
```

通用的写法：

```javascript
var curry = function(fn) {
    var _args = []
    return function cb() {
        if (arguments.length == 0) {
            return fn.apply(this, _args)
        }
        Array.prototype.push.apply(_args, arguments);
        return cb;
    }
}
```

上面累加的例子，可以实验一下怎么写？

```javascript
function add(){
	var i = 0,
		sum = 0;

	while(arguments[i]){
		sum += arguments[i++];
	}
	return sum;
}

var curry = function(fn) {
    var _args = []
    return function cb() {
        if (arguments.length == 0) {
            return fn.apply(this, _args)
        }
        Array.prototype.push.apply(_args, arguments);
        return cb;
    }
}

var test = curry(add);
test(1)(2)(3)(4)(5)();// 15
```

### 3、固定易变因素

柯里化特性决定了它这应用场景。提前把易变因素，传参固定下来，生成一个更明确的应用函数。最典型的代表应用，是bind函数用以固定this这个易变对象。

```javascript
Function.prototype.bind = function(context) {
    var _this = this,
    _args = Array.prototype.slice.call(arguments, 1);

    return function() {
        return _this.apply(context, _args.concat(Array.prototype.slice.call(arguments)))
    }
}
```

## 三、反科里化

函数柯里化，是固定部分参数，返回一个接受剩余参数的函数，也称为`部分计算函数`，目的是为了缩小适用范围，创建一个针对性更强的函数。

那么反柯里化函数，从字面讲，意义和用法跟函数柯里化相比正好相反，扩大适用范围，创建一个应用范围更广的函数。使本来只有特定对象才适用的方法，扩展到更多的对象。

通用函数：

```javascript
Function.prototype.unCurrying = function() {
    var that = this;
    return function() {
        return Function.prototype.call.apply(that, arguments);
    }
}
```

短小精悍，科学上讲，浓缩的都是精品，但越精品的往往越难以理解。分解一下：

1 为Function原型添加unCurrying方法，这样所有的function都可以被借用；

2 返回一个借用其它方法的函数，这是目的；

3 借用call方法实现，但call方法参数传入呢？借用apply，至此完毕。

回头看看，好像也不难！

还有其它的实现方式：

```javascript
Function.prototype.unCurrying = function () {
    var f = this;
    return function () {
        var a = arguments;
        return f.apply(a[0], [].slice.call(a, 1));
    };
};
```

```javascript
Function.prototype.unCurrying = function () {
    return this.call.bind(this);
};
```

原理都相同，最终是把this.method转化成 method(this,arg1,arg2....)以实现方法借用和this的泛化。

鸭式辩型：如果一个对象可以像鸭子一样走路,游泳,并且嘎嘎叫,就认为这个对象是鸭子,哪怕它并不是从鸭子对象继承过来的。

在javascript里面，很多函数都不做对象的类型检测，而是只关心这些对象能做什么。

Array构造器和String构造器的prototype上的方法就被特意设计成了鸭子类型。这些方法不对this的数据类型做任何校验。这也就是为什么arguments能冒充array调用push方法.
看下v8引擎里面Array.prototype.push的代码：

```javascript
function ArrayPush() {
    var n = TO_UINT32(this.length);
    var m = %_ArgumentsLength();
    for (var i = 0; i < m; i++) {
        this[i + n] = %_Arguments(i); //属性拷贝
        this.length = n + m; //修正length
        return this.length;
    }
}
```

这就给对象冒充创造了条件，也就我们讨论的函数柯反里化unCurrying。反柯里化其实反映的是一种思想，扩大方法的适用范围！

看下面例了，让一个普通对象具备push方法：

```javascript
var push = Array.prototype.push.unCurrying(),
	obj = {};

push(obj, 'first', 'two');

console.log(obj);
/*obj {
0: "first",
1: "two"
}*/
```

obj被push了两个元素：first 和two，同时还具备了一个length属性，其实我们创建了一类数组对象。

再看一个例子：

```javascript
var toUpperCase = String.prototype.toUpperCase.unCurrying();

console.log(toUpperCase('avd')); // AVD

function AryUpper(ary) {
    return ary.map(toUpperCase);
}

console.log(AryUpper(['a', 'b', 'c'])); // ["A", "B", "C"]
```

只是方法都可以借用。包括call方法。

```javascript
var call = Function.prototype.call.unCurrying();

function $(id) {
    return this.getElementById(id);
}

var demo = call($, document, 'demo');

console.log(demo);
```

这似乎看起来相对于前两个例子，比较难理解，其实一句话就可以解释：document借用了$方法，并替换了其中的this。

在函数柯里化例子中bind的例子中，柯里化的目的是为了固定可变参数this。而反柯里化，把原来拥有方法的this泛化了，泛化到所有对象都可以借用，也就是替代当前拥有方法的this。

这里，希望不要混淆，这里的this并不是指上例中的this,上例中的this只是因为借用的方法是call。

更有趣的是，unCurrying本身也是方法，它是否可以被借用呢？答案是肯定的。这就是js的奇妙之处，反柯里化的奇妙之处。

看下面：

```java
var unCurrying = Function.prototype.unCurrying.unCurrying();

var map = unCurrying(Array.prototype.map);
var sq = map(	
				[1, 2, 3],
				function(n) {
				    return n * n;
				}
			);

console.log(sq); // [1,4,9]
```

## 四、原文

[穆乙：前端开发者进阶之函数柯里化Currying](http://www.cnblogs.com/pigtail/p/3447660.html)

[穆乙：前端开发者进阶之函数反柯里化unCurrying](http://www.cnblogs.com/pigtail/p/3450852.html)
