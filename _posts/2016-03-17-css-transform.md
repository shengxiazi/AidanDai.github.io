---
layout: post
title:  "css之transform"
date:   2016-03-17
categories: css
tags: css
keyword: "transform, css"
author: Aidan Dai
---

在参加2016-spring百度前端技术学院做团队静态主页时我们大胆尝试使用了大量的css3的高级属性，自我感觉还是不错的！欢迎去看看[baidu-ife: SHSF团队]( http://ife-SHSF.github.io)。不过当时理解的还是比较浅薄的，现在回过头来在研究研究！更进一步吧！

## 语法及取值等

请参考[杜瑶: CSS参考手册](http://css.doyoe.com/)

## 变换类型

<pre>
transform-style：flat | preserve-3d

flat: 指定子元素位于此元素所在平面内(默认值)

preserve-3d: 指定子元素定位在三维空间内
</pre>



## 平面变换

指定元素平面变换基准点，默认在元素中心

### 旋转rotate、移动translate、缩放scale

![rotate](/asset/images/article/rotate.png)

![translate-x-y](/asset/images/article/translate-x-y.png)

![scale-x-y](/asset/images/article/scale-x-y.png)

注: 旋转、移动、缩放比较直观，我们再此就不讨论具体的坐标运算了

- 旋转值为正数表示顺时针旋转，如果设置的值为负数，则表示逆时针旋转

- 移动值为正数表示向浏览器平面坐标系正方向移动，如果设置的值为负数，则表示向浏览器平面坐标系负方向移动

- 旋转、移动、缩放都可指定是单方向的

### 扭曲skew、矩阵matrix

扭曲也可指单方向的,当然基点也是元素中心（默认），其实可以把基点想象成做图形变换的坐标原点，然后依次在该坐标系对元素的个顶点进行矩阵变换，以求得新的坐标位置，变换过程遵循下图的公式（来自[W3C:  transformation matrices ](https://www.w3.org/TR/SVG/coords.html#TransformMatrixDefined)），只是参与运算的矩阵不相同而已；有兴趣的可以去研究一下！

![MatrixMultiply](/asset/images/article/MatrixMultiply.png)

#### X单方向扭曲

运算矩阵：

![MatrixSkewX](/asset/images/article/MatrixSkewX.png)

#### Y单方向扭曲

运算矩阵：

![MatrixSkewY](/asset/images/article/MatrixSkewY.png)

#### 矩阵matrix

运算矩阵：

![Matrix](/asset/images/article/Matrix.png)

#### 平面综合变换示例

![NestedCalcs](/asset/images/article/NestedCalcs.png)

具体计算：

1、将参数带入各个矩阵，算出CMT

2、计算出CMT的逆

3、CMT的逆乘以原坐标矩阵，求得新坐标矩阵

## 三维空间变换

指定元素三维空间变换基准点，默认在元素中心

### 浏览器三维坐标系：(屏幕左上角为坐标原点)

![3d_axes](/asset/images/article/3d_axes.png)

下面来看几个实际的例子：

- 邹凯的体操单杠运动是rotateX

![邹凯的体操单杠运动是rotateX](/asset/images/article/rotatex-example.jpg)

- 蔡依林姐姐的钢管舞是rotateY

![蔡依林姐姐的钢管舞是rotateY](/asset/images/article/rotatey-example.jpg)

- 旋转飞刀的特技表演是rotateZ

![旋转飞刀的特技表演是rotateZ](/asset/images/article/rotatez-example.jpg)

### 必不可少的perspective属性

```html
<!DOCTYPE html>
<html>
<head>
	<title>perspective</title>
</head>
<body>
	<div class="stage">
		<div calss="box"></div>
	</div>
</body>
</html>
```

1、给舞台元素加个视距

```css
.stage {
    perspective: 600px;
}
```

2、给当前动画元加个视距

```css
#stage .box{
    transform: perspective(600px) rotateY(45deg);
}
```

注意两者的区别！

## 参考资料

[W3cplus: CSS3 Transform](http://www.w3cplus.com/content/css3-transform)

[W3C：TransformMatrixDefined](https://www.w3.org/TR/SVG/coords.html#TransformMatrixDefined)

[张鑫旭：好吧，CSS3 3D transform变换，不过如此！](http://www.zhangxinxu.com/wordpress/2012/09/css3-3d-transform-perspective-animate-transition/)
