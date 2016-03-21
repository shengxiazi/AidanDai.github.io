---
layout: post
title:  "参加2016-spring百度前端技术学院中遇到的问题"
date:   2016-03-19
categories: web
tags: html css
---

先说说这次参加2016-spring百度前端技术学院的计划，认真的复习基础知识；虽然学习前端快两年了，大大小小也许做过些项目，有些是学长带着做，也有些是自己和学弟们一块做，感触最深的就是基础不好真的不行，还有一个就是一定要有自己解决问题的思路。这次趁着这个机会好好把基础补下，真样才能走的更远；什么框架那只是API而已，学框架只是好用，做项目快，就像**真阿当**所说的那个样，好好的把你的基础学好，别乱搞框架，以为会了框架就是会前端了，NO! NO! NO！最后再说一点这次也要好好养成代码习惯。。。

# 突兀的p元素

在参加2016-spring百度前端技术学院做任务七的时候，遇到了如下问题：

![突兀的p](/asset/images/article/float-relative.jpg)

## 简单问题描述：

这种情况是什么原因造成的？

[在线预览：任务七（实现常见的技术产品官网的页面架构及样式布局）](http://ife-shsf.github.io/work.html)

[代码：task1_07.html](https://github.com/ife-SHSF/ife-SHSF.github.io/tree/master/assets/work/AidanDai)

## 在一些研究基础上描述问题

先说说我写这个的思路，方便大神解决问题！

```html
	<section class="part-four">
		<div class="layer"></div>
		<h1>查找新世界城市活动信息</h1>
		<hr/>
		<p>每个城市都有不同的活动信息，请自主查询您所需要了解的城市！</p>
		<div class="search">
			<select>
				<option>中国</option>
			</select>
			<select>
				<option class="test">省份</option>
				<option class="test">北京市</option>
				<option class="test">天津市</option>
				<option class="test">河北省</option>
				<option class="test">陕西省</option>
				<option class="test">内蒙古自治区</option>
				<option class="test">辽宁省</option>
			</select>
			<select>
				<option>城市</option>
			</select>
			<button>搜&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;索</button>
		</div>
	</section>
	<section class="part-five">
		<ul>
			<li>
				<div class="bg bg01"></div>
				<p class="title">北京活动</p>
				<p>新社区大联盟</p>
			</li>
			<li>
				<div class="bg bg02"></div>
				<p class="title">上海活动</p>
				<p>新上海新夜景探索</p>
			</li>
			<li>
				<div class="bg bg03"></div>
				<p class="title">深圳活动</p>
				<p>全新海岸线观察点</p>
			</li>
			<li>
				<div class="bg bg04"></div>
				<p class="title">香港活动</p>
				<p>奢侈消费大派送</p>
			</li>
		</ul>
	</section>
```

## part four的实现：

1、&lt;section class="part-four&gt;定高定宽，常规文档流

2、由于 ![css3 model box](http://img.blog.csdn.net/20151121133821143)

所以为了实现透明度0.2，使用&lt;div class="layer&gt;遮罩层，和 section 等高等宽

3、由于遮罩层实现的影响，所以part four 的其他元素便使用相对定位，使其达到图示效果

4、继续往下写，发现 part five 部分中的一个 p 元素十分异常，看上去有了高度（实际上什么样式都没有）

经过我们小组讨论，得出以下结论,但还是不能理解float和relative情况下造成的布局混乱，实在想不明白，网上查询大都只是单独讲解float和position的知识点；而没有找到这两者对浏览器渲染造成的影响

虽然 relative 定位不影响其他元素，就是不脱离文档流；换句话说也就是仍然占他原本占的位子，只是z-index变大，相对原始位置漂浮到指定地方

可能有人会问那 part five 所有的元素都应该往下，而不应该在现在图中的位置；个人觉得relative改变只是只是把我们所理解的三维盒子移动了,而他原本的渲染时占位用的东西任然还在原地，暂且说成BFC吧，而absolute把元素连带BFC都拿走了，而part four中元素的BFC都在section盒子中，layer遮罩层只是把其他元素的三维盒模型赶了出来，现在通过relative 我又把这些元素的三维盒模型给赶了回去，所以就形成了part five中部分元素紧跟part four.

可悲催是search 部分的子元素是浮动的，形成了新的BFC,不在受原来BFC的约束！我们通过relative只是强行把这些元素的三维盒子赶回了part five的section中！于是乎在成了现在这种局面？他死死的占据了那块位置，当然这只是猜想！还是想请大牛们解释下到底是为啥？

另一个猜想：从结果来看，浏览器貌似是先渲染了search部分子元素的flaot,创建新的BFC,解除默认形成的section中BFC的约束；然后在对search整体相对定位，我们只是把这个元素三维盒子移到我们想要的位置，float创建BFC的位置它是继续站着吗？

实在是不太明白，请大牛们帮忙解答下疑惑？

当然仅仅想解决目前的bug的人就别回答了吧！解决bug还是会的！只想知道这是什么原因造成的？


## 诡异的 float

## css 之继承

## 元素居中问题（Normal flow、BEF、IFC、Out of normal flaow）

## 图片优化问题
