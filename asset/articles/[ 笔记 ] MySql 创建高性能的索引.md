#MySql 创建高性能的索引

注：本文永久更新链接，markdown格式源码  [Github: Aidan Dai](https://github.com/AidanDai/blog)

******************************

[TOC]

##索引及其类别

一、索引：存储引擎用于快速找到记录的一种数据结构

- 索引是存储引擎层而不是服务器层实现

二、 B-Tree 索引

- 每个叶子节点都包含指向下一个叶子节点的指针，从而方便叶子节点的范围遍历

- 对索引列是顺序结构存储的，可以来做**ORDER BY **和 **GROUP BY **

- 存储索引列值，所以部分查询只使用索引就能够完成全部查询，无需扫面数据库


- 索引示例：

![索引示例](http://img.blog.csdn.net/20151228113006992)

对于初学者来说这个看起来相当困惑，于是找了找关于** B-Tree**的知识

**详情请参考：** [B-Trees: Balanced Tree Data Structures](http://bluerwhite.org/btree/)

- **B-Tree ：Balanced Tree Data Structures**


1、Sample  B-Tree

![sample b-tree](http://bluerwhite.org/btree/tree1.png)

2、Searching a B-Tree for Key 21

![Searching a B-Tree for Key 21](http://bluerwhite.org/btree/tree-search.gif)

3、Inserting Key 33 into a B-Tree (w/ Split)

![Inserting Key 33 into a B-Tree (w/ Split)](http://bluerwhite.org/btree/tree-insert2.gif)


继续索引示例：

- 索引对多个值进行排序的依据是**CREATE TABLE**语句中定义索引时列的顺序

- B-Tree索引适用于全键值、键值范围或键前缀查找（只适用于根据最左前缀查找）

1、可以使用 B-Tree 索引的查询类型

![B-Tree 查询类型](http://img.blog.csdn.net/20151228121557845)


2、关于 B-Tree 索引的一些限制

- 如果不是按照索引的最左列开始查找的，则无法使用索引

- 不能跳过索引中的列

- 如果查询中有某个列的范围查询，则其右边的所有列都无法使用索引优化查找

有些限制并不是B-Tree本身导致的，而是MySql优化器和存储引擎使用索引方式导致的。

三、哈希索引

- 基于哈希表实现，只有精确匹配所有索引列的查询才有效

- MySql 中，只有 Memory 引擎显示支持哈希索引，这也是 Memory 引擎表的默认索引类型， Memory 引擎同时支持 B-Tree 索引。值得一提的是， Memory 引擎是支持非唯一哈希索引的；如果多个列的哈希值相同。索引会已链表的方式存放多个记录指针到同一个哈希条目中。

![哈希索引](http://img.blog.csdn.net/20151228123659244)

- 哈希索引的限制

1、哈希索引只包含哈希值和行指针，而不存储字段值，索引不能使用索引中的值来避免读取行。

2、无法排序，因为哈希索引并不是按照索引值顺序存储的
3、哈希索引也并不支持部分索引列匹配查找，因为始终是使用索引列的全部内容来计算哈希值的。

4、哈希索引只支持等值比较，包括 = ， IN() , <=> （注意<> 和 <=> 是不同的操作）；也不支持任何范围查询。

5、访问哈希索引的数据非常快，除非有很多哈希冲突（不同的索引列值确有相同的哈希值）。

4、如果哈希冲突很多的话，一些索引维护操作的代价也很高。

- 使用哈希索引

1、哈希值的生成（整数组成）
<pre>
	CRC32() 会出现大量哈希冲突
	SHA1() 和 MD5() 是强加密函数，浪费存储空间，降低性能，比较哈希值时间变长
	截取 MD5() 中部分值，方便快捷，也不影响性能
	FNV64() 速度快，冲突比 CRC32() 少很多
</pre>
2、使用触发器维护哈希索引
3、使用 **where = "常量" **来解决哈希冲突

![解决哈希冲突](http://img.blog.csdn.net/20151228134925785)

四、R-Tree（空间数据索引）

MySql 中很少使用

五、全文索引


##索引的优点

- 索引大大减少了服务器需要扫描的数据量
- 索引可以帮助服务器避免排序和临时表
- 索引可以将随机**I/O**变为顺序**I/O**

##高性能的索引策略

1、独立的列：索引列不能是表达式的一部分，也不能是函数的参数

常见错误
```mysql
SELECT actor_id FROM salila.actor WHERE actor_id + 1 = 5;
SELECT ... WHERE TO_DAYS(CURRENT_DAY) - TO_DAYS(date_col) <= 10;
```
2、前缀索引和索引选择性

索引的选择性：不重复的索引值（也称为基数，cardinality）和数据表的记录总数（#T）的比值，范围从 1/#T 到  1 之间。

- 计算完整列的选择性
```mysql
SELECT CONUT(DISTINCT city)/COUNT(*) FROM sakila.city_demo;
```

- 测试不同前缀的选择性
```mysql
SELECT
	COUNT (DISTINT LEFT(city, 4))/COUNT(*) AS sel4，
	COUNT (DISTINT LEFT(city, 5))/COUNT(*) AS sel5，
	COUNT (DISTINT LEFT(city, 6))/COUNT(*) AS sel6，
	COUNT (DISTINT LEFT(city, 7))/COUNT(*) AS sel7，
FROM sakila.city_demo;
```

- 创建前缀索引
```mysql
ALTER TABLE sakila.city_demo ADD KEY (city(7));
```

- 前缀索引的优缺点
优点：能使索引更小、更快的有效办法
缺点：MySql 无法使用前缀索引做 **ORDER BY** 和 **GROUP BY** ，也无法使用前缀索引做覆盖扫描

3、多列索引

待续
