---
layout: post
title:  "排序算法的原理分析与javascript实现"
date:   2016-03-19
categories: algorithm
tags: algorithm sort
---

排序有内部排序和外部排序，内部排序是数据记录在内存中进行排序，而外部排序是因排序的数据很大，一次不能容纳全部的排序记录，在排序过程中需要访问外存。

## 选择排序

### 简单选则排序

- 排序思想：

>在未排序序列中选出最小的元素和序列的首位元素交换，接下来在剩下的未排序的序列中在选出最小元素与序列的第二元素交换，以此类推，最后形成从小到大的一排序列

假设用选择排序来给有 N 个值的数组排序，换句话说，也就是做 N-1 次遍历，把第 i（从零开始） 小或大的元素放到第 i 的位置，从而形成有序序列

- 原理图：

![chooseSort](/asset/images/article/simpleSelectionSort.png)

如图所示，每趟排序中，**将当前第 i 小或大的元素放在位置 i 上**。

- 实现：

```javascript
function simpleSelectionSort(arr){
	var len = arr.length;
	for(var i=0; i<len-1; i++){ //当要排序 N 个数，已经经过 N-1 次遍历后，已经是有序数列
		//var min = i; //保存最小值索引
		var max = i; //保存最大值索引
		for(var j=i+1; j<len; j++){
			// if(arr[j] < arr[min]){ //寻找第i个小的数值
			// 	min = j;
			// }
			if(arr[j] > arr[max]){
				max = j;
			}
		}
		//swap(arr,i,min); //将找到的第i个小的数值放在第i个位置上
		swap(arr,i,max); //将找到的第i个大的数值放在第i个位置上
	}
	return arr;
}```
- 算法分析：

1、时间复杂度：

（1）比较次数：

不难得出第0个元素比较了 N-1 次，第1个元素比较了 N-1 次，以此类推总的比较次数：N-1 + N-2 + ... + 1 = N*(N-1)/2，这也就是内层循环的循环次数

（2）交换元素步骤：

只有当当前元素是第 i 小或大元素是不用交换元素，也就是说在最好情况下0次（待排序元素的序列已经有序），最坏的情况下为 (N-1) 交换元素，只有最后一个元素不用交换位置

那么时间复杂度显然就是 O(n*n)

空间复杂度：

简单选择排序只需要在交换数值时占用一个临时空间。

下面是几种比较极端的情况，可以感受一下：

![simpleSelectionSort-effect](/asset/images/article/simpleSelectionSort-effect.gif)

### 堆排序

- 排序思想
- 原理图
- 实现
- 算法分析

## 插入排序

### 简单插入排序

- 排序思想

>将待排序的一组序列分为已排好的和未排好的两个部分；初始状态时，已排序序列仅包含第一个元素，未排序序列中的元素为除第一个元素以外的 N-1 个元素；此后将未排序序列中的元素逐一插入到已排序的序列中

通俗点讲就是把未排序序列的第一个元素插入到已排序序列中的合适位置

- 原理图

![simpleInsertSort](/asset/images/article/simpleInsertSort.gif)

- 实现

```javascript
function simpleInsertSort(arr){
	var len = arr.length;
	for(var i=0; i<len; i++){
		var temp = arr[i];
		for(var j=i+1; j>0; j--){
			if(temp < arr[j-1]){
				arr[j] = arr[j-1];
			}else{
				break;
			}
		}
		arr[j] = temp;
	}
	return arr;
}
```

- 算法分析

### 希尔排序

- 排序思想
- 原理图
- 实现
- 算法分析

## 交换排序

### 冒泡排序

- 排序思想
- 原理图
- 实现
- 算法分析

### 快速排序

- 排序思想
- 原理图
- 实现
- 算法分析

## 归并排序
## 基数排序
## 外部排序

**************************************

注：

1、交换数组元素

```javascript
function swap(arr,index1,index2){//数组数据交换
    var temp = arr[index1];
    arr[index1] = arr[index2];
    arr[index2] = temp;
    return null;
}
```

2、参考书籍：数据结构（陈越版）

3、参考书籍：[Victor Zhang：程序员的内功——数据结构和算法系列](http://www.cnblogs.com/jingmoxukong/p/4329079.html)
