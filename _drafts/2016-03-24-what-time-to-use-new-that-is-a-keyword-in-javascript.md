---
layout: post
title:  "javascript 中，什么时候该去 new ?"
date:   2016-03-19
categories: javascript
tags: javascript 面向对象编程 函数式编程
---

昨晚和学弟聊天聊到半夜，一直在聊怎么去规划人生的发展，以及怎么去学习？以前觉得自己学的也是比较杂的，但是昨晚的聊天更让人觉得自己学的太杂了，没有根基；也想明白了以前很多错误的想法，甚至是借口，以前觉得自己的前端已经到了发展的瓶颈期，可是那是自己错了，以为自己的深度已经可以了，然后不断去拓展其他知识！其实那是自己对面对困难的软弱以及自己手高眼低罢了，没有发现真正的大牛是怎样炼成？从今天起我们要开起 javascript 模式了，让自己离大牛更近一点！找个目标，学习的伙伴，当然也可以把他当做对手，就是要你争我赶！说的有点多，还是继续写博客吧！

## 到底是先有鸡（Function），还是先有蛋（Object）？

先有鸡还是先有蛋，这是个货真价实的千古难题——至少讨论了两千年。即使现在科学这么发达仍然没有结果！[番外篇：到底是先有鸡，还是先有蛋？](http://www.guokr.com/article/440611/)

现实世界中没有上帝，不可以凭空创造鸡，也不可以凭空创造蛋！不能违背自然！但在程序世界里程序猿就是上帝，随时可以创造一切东西！

>如果曾有一个最初的人，那他必定是无父无母而降生——这是违背自然的。蛋能孵出鸟，然而鸟类不可能诞生自一枚最初的蛋，不然一定还得有一只最初的鸟去生下这枚蛋。

是不是这样呢？

在 javascript 的王国里 [Brendan Eich](https://en.wikipedia.org/wiki/Brendan_Eich) 就是上帝，他创造了一个母鸡（Function），母鸡又下了一个蛋（Object）；于是乎就开始造物了Array、String、Math、Date...

先来瞻仰一下javascript 之父！

![javascript 之父：Brendan Eich](/asset/images/article/Brendan-Eich.jpg)

先别着急，下面我们来慢慢分析下吧！

### 先来回顾下 javascript 的原型链吧！

![javascript-objetc-layout](/asset/images/article/javascript-object-layout.jpg)

先看上5分钟吧！把这每条线都看明白！

#### 创造母鸡

```
function function(){// 这是Brendan Eich创造的第一只母鸡

}
```

#### 母鸡生下了第一个鸡蛋！好高兴呀！

```javascript
var Function = function(){};
Function.prototype = {// 这是Brendan Eich把他的母鸡打扮了一番。。。
	...
}
```

Brendan Eich 再创造了第一只母鸡之后又制定了许多规则，这回可是好好过了把上帝的瘾！

- 凡是通过 new

#### 母鸡生下了第一个鸡蛋！好高兴呀！

```javascript
var Object = new Function();
```

## new 一个对象

```
Function.prototype.addMethod1 = function(name, fn){
	this[name] = fn;
}

var methods1 = function(){};//这行代码真的很让人费解，书中说是函数式编程，但要怎么去理解呢？
var methods2 = Function(); //这段代码可以理解为正常函数调用，返回一个对象函数
var methods3 = new Function(); //以面向对象的观点来解释，实例化 Function 类的对象

methods1.addMethod1("checkName", function(){
	log("checkName1");
});
```

注：[阮一峰：函数式编程初探](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)

- 对象是怎么来的

上面的代码作如下调试,大家可以先猜猜结果

```javascript
console.log(methods1);
console.log(methods2);
console.log(methods3);
console.log(methods1.prototype);
console.log(methods2.prototype);
console.log(methods3.prototype);
console.log(methods1.__proto__);
console.log(methods2.__proto__);
console.log(methods3.__proto__);

// output
// { [Function] checkName: [Function] }
// [Function]
// [Function]
// {}
// {}
// {}
// { [Function: Empty] addMethod1: [Function] }
// { [Function: Empty] addMethod1: [Function] }
// { [Function: Empty] addMethod1: [Function] }
```

从结果来看说明了什么呢？

三种方式结果完全相同，说明了 javascript 在解释执行这些代码表现一致或者 javascript 底层三者的实现完全一样
