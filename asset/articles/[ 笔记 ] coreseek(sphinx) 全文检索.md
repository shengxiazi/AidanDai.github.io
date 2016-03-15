# coreseek(sphinx) 全文检索

注：本文永久更新链接，markdown格式源码  [Github: Aidan Dai](https://github.com/AidanDai/blog)

******************************

[TOC]

## Coreseek/sphinx全文检索的了解

### 概述

>全文检索是一种将文件中所有文本与检索项匹配的文字资料检索方法，全文检索是将存储于数据库中整本书、整篇文章中的任意内容信息查找出来的检索。它可以根据需要获得全文中有关章、节、段、句、词等信息，也可以进行各种统计和分析。

### 全文检索

>全文检索易龙天网的设计师认为可以把它划分为二部分：

>- 全文

>全文顾名思义：就是全文检索的对象，它可能是一段话，也可能是一片文章，它可能是一个文件比如：word,txt也能是任意一种扩展名结尾的文件

>- 检索

>描文章中的每一个词，对每一个词建立一个索引，指明该词在文章中出现的次数和位置，当用户查询时根据建立的索引查找，类似于通过字典的检索字表查字的过程。

### sphinx

>- sphinx定义

>Sphinx是一个基于SQL的全文检索引擎，可以结合MySQL,PostgreSQL做全文搜索，它可以提供比数据库本身更专业的搜索功能，使得应用程序更容易实现专业化的全文检索。Sphinx特别为一些脚本语言设计搜索API接口，如PHP,Python,Perl,Ruby等，同时为MySQL也设计了一个存储引擎插件。

>- sphinx特点

>Sphinx 单一索引最大可包含1亿条记录，在1千万条记录情况下的查询速度为0.x秒(毫秒级)。Sphinx创建索引的速度为：创建100万条记录的索引只需 3～4分钟，创建1000万条记录的索引可以在50分钟内完成，而只包含最新10万条记录的增量索引，重建一次只需几十秒

### sphinx搜索引擎架构图

![](http://img.blog.csdn.net/20140703094118640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2Vic2l0ZXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### sphinx分布式索引原理

当searchd收到一个对分布式索引的查询时，它做如下操作：

1. 连接到远程代理
2. 执行查询
3. （在远程代理执行搜索的同时）对本地索引进行查询
4. 接收来自远程代理的搜索结果
5. 将所有结果合并，删除重复项
6. 将合并后的结果返回给客户端

在应用程序看来，普通索引和分布式索引完全没有区别。任一个searchd实例可以同时做为主控端（master，对搜索结果做聚合）和从属端（只做本地搜索）。这有如下几点好处：

1. 集群中的每台机器都可以做为主控端来搜索整个集群，搜索请求可以在主控端之间获得负载平衡，相当于实现了一种HA（high availability，高可用性），可以应对某个节点失效的情况。
2. 如果在单台多CPU或多核机器上使用，一个做为代理对本机进行搜索的searchd实例就可以利用到全部的CPU或者核。更好的HA支持已在计划之中，到时将允许指定哪些代理之间互相备份、有效性检查、跟踪运行中的代理、对检索请求进行负载均衡，等等。

#### sphinx分布式索引配置

```conf
index dist1
{
    # local index to be searched
    # there can be many local indexes configured
    local               = test1
    local               = test1stemmed
    # remote agent
    # multiple remote agents may be specified
    # syntax for TCP connections is 'hostname:port:index1,[index2[,...]]'
    # syntax for local UNIX connections is '/path/to/socket:index1,[index2[,...]]'
    agent               = localhost:9313:remote1
    agent               = localhost:9314:remote2,remote3
    # agent             = /var/run/searchd.sock:remote4
    # blackhole remote agent, for debugging/testing
    # network errors and search results will be ignored
    # agent_blackhole       = testbox:9312:testindex1,testindex2
    # remote agent connection timeout, milliseconds
    # optional, default is 1000 ms, ie. 1 sec
    agent_connect_timeout   = 1000
    # remote agent query timeout, milliseconds
    # optional, default is 3000 ms, ie. 3 sec
    agent_query_timeout     = 3000
}
```

原理和配置还是比较简单的，对于索引文件过大的情况，分布式索引非常有用。但是碰到搜索量非常大的时候，分布式索引就显得力不从心了。这个时候就需要做索引复制了。索引复制同步的问题一般采用的方案有两种。

- 第一种方案是主从同步。有一个主搜索服务器和多个从搜索服务器。在主搜索服务器上生成的增量索引会同步从索引服务器。从而达到主从同步。为了避免网络或者其他情况导致的增量索引不同步的情况，需要定期在一个阶段把主从服务器的主索引同步一次。新增加搜索服务器的话需要在增量索引生成的间隔数据去拷贝其他搜索服务器的主索引。

- 第二种方案是每个搜索服务器根据增量数据去生成增量索引。通过表来记录不同的搜索服务器是否生成增量索引。来达到同步的目的。新增加搜索服务器的话，就需要去比历史上所有的增量数据全部生成一遍。

这两种策略都是可行的。增量数据在一定时间段量很大的话建议用第一种方案，否则用第二种方案。

### coreseek

>Coreseek为应用提供全文检索功能，目前的版本(2.x 3.x)基于Sphinx ，支持使用Python定义数据源，支持中文分词。这说明coreseek集合了sphinx的功能，支持更多的数据源，在字典，建立索引，分词更好的支持中文。可以这样理解，coreseek就是支持中文的sphinx全文检索

## [ 教程 ] centos 7 下安装并测试 coreseek

服务器的环境
<pre>
CentOS Linux release 7.2.1511
Coreseek： 3.2.14 稳定版（Sphinx 0.9.9）
httpd：httpd-2.4.6-40.el7.centos.x86_64
mariadb：mariadb-5.5.44-2.el7.centos.x86_64
php：php-5.4.16-36.el7_1.x86_64
</pre>

### 环境准备

```shell
[root@localhost ~] yum install make gcc gcc++ gcc-c++ libtool autoconf automake imake mysql-devel libxml2-devel expat-devel
```
### 安装mmseg

先说下linux上安装软件我习惯把源码包下载在
<pre>/usr/local/src/</pre>
软件安装在
<pre>/usr/local/software_name</pre>
即在安装过程中指定安装目录
<pre>./configure --prefix=/usr/local/mmseg3</pre>

```shell
[root@localhost src] wget http://www.coreseek.cn/uploads/csft/3.2/coreseek-3.2.14.tar.gz

...

[root@localhost src] tar xzvf coreseek-3.2.14.tar.gz

...

[root@localhost src] cd coreseek-3.2.14/mmseg-3.2.14

[root@localhost mmseg-3.2.14] ./bootstrap	#输出的warning信息可以忽略，如果出现error则需要解决

...

[root@localhost mmseg-3.2.14] ./configure --prefix=/usr/local/mmseg3

...

[root@localhost mmseg-3.2.14] make && make install

...

```

注：安装mmseg一般不会出错，测试如下

```shell
[root@localhost bin]# ./mmseg -d /usr/local/mmseg3/etc /usr/local/src/coreseek-3.2.14/mmseg-3.2.14/src/t1.txt
中文/x 分/x 词/x 测试/x 
中国人/x 上海市/x 

Word Splite took: 0 ms.

```

### 安装coreseek

```shell
[root@localhost src] cd coreseek-3.2.14/csft-3.2.14
[root@localhost csft-3.2.14] sh buildconf.sh    #输出的warning信息可以忽略，如果出现error则需要解决

...

[root@localhost csft-3.2.14] ./configure --prefix=/usr/local/coreseek  ./configure --prefix=/usr/local/coreseek  --without-unixodbc --with-mmseg --with-mmseg-includes=/usr/local/mmseg3/include/mmseg/ --with-mmseg-libs=/usr/local/mmseg3/lib/ --with-mysql --with-mysql-includes=/usr/include/mysql --with-mysql-libs=/usr/lib64/mysql     ##如果提示mysql问题，可以查看MySQL数据源安装说明(http://www.coreseek.cn/product_install/install_on_bsd_linux/#mysql)

注：yum install
...

make && make install

...

注：yum install 安装的数据库，一般情况下，需要这样指定安装文件所依赖的库文件

<pre>
    --with-mysql-includes=/usr/include/mysql 
    --with-mysql-libs=/usr/lib64/mysql
</pre>

注1：错误1（官方提供的 Coreseek 4.1 测试版 缺少 Makefile.in 文件），如下图

注2：错误2 （ Coreseek 在 GCC 4.7 上编译不通过的问题 (http://vifix.cn/blog/coreseek-cant-make-in-gcc-4-7-enviroment.html) ）,解决办法如下
```

![注1：错误1](http://img.blog.csdn.net/20160107000024237)

注2：[Coreseek 在 GCC 4.7 上编译不通过的问题 解决方案](http://www.nikest.com/web/jswd/2015/0211/117086.html)

基本配置文件：
<pre>

source src1
{
		type					    = mysql
		sql_host				= localhost
		sql_user				= database_user
		sql_pass				= database_pwd
		sql_db					= database_name
		sql_port				= 3306

	sql_query_pre			= SET NAMES utf8
	sql_query_pre			= SET SESSION query_cache_type=OFF
	sql_query				= 
	sql_attr_uint			= 
	sql_query_info			=

}

index test1
{
	source					    = src1
	path					        = /usr/local/coreseek/var/data/test1
	docinfo					= extern
	charset_type		    = zh_cn.utf-8
	charset_dictpath		= /usr/local/mmseg3/etc/
	html_strip				= 1
}

indexer
{
	mem_limit			= 32M
}

searchd
{
	port					            = 9312
	log						        = /usr/local/coreseek/var/log/searchd.log
	query_log				        = /usr/local/coreseek/var/log/query.log
	read_timeout			    = 5
	max_children			    = 30
	pid_file				            = /usr/local/coreseek/var/log/searchd.pid
	max_matches				= 1000
	seamless_rotate			= 1
	preopen_indexes			= 0
	unlink_old				   = 1
}

</pre>

具体测试如下：（前提是导入测试数据，修改配置文件）

```shell
[root@localhost ~] /usr/local/coreseek/bin/indexer --all
[root@localhost ~] /usr/local/coreseek/bin/search one
```

### 安装 php 的扩展模块 sphinx


```shell
[root@localhost src] yum install php-devel   #phpize
[root@localhost src] wget http://pecl.php.net/get/sphinx-1.3.3.tgz
[root@localhost src] tar -zxvf sphinx-1.3.3.tgz
[root@localhost src] cd /usr/local/src/coreseek-3.2.14/testpack/api/libsphinxclient
[root@localhost libsphinxclient] ./configure 			#注3( sphinxclient.c 错误，请参考（http://sphinxsearch.com/forum/view.html?id=4801）)
[root@localhost libsphinxclient] make && make install
[root@localhost libsphinxclient] cd /usr/local/sphinx-1.3.3
[root@localhost libsphinxclient] phpize
[root@localhost libsphinxclient] ./configure --with--php-config=/usr/binphp-config --with-sphinx
[root@localhost libsphinxclient] make && make install
[root@localhost libsphinxclient] cd /etc/php.d
[root@localhost php.d] cp gd.ini sphinx.ini

...#修改sphinx.ini,重启apache,phpinfo查看sphinx扩展模块是否安装成功

```

<pre>
sphinxclient.c:1216: error: static declaration of ‘sock_close’ follows non-static declaration
sphinxclient.c:280: note: previous declaration of ‘sock_close’ was here
</pre>

### 启动后台服务

```shell
[root@localhost ~] /usr/local/coreseek/bin/searchd
````

### 重建索引

```shell
    /usr/local/sphinx/bin/searchd --stop   #重建索引一定要先关闭后台服务
    /usr/local/sphinx/bin/indexer --all
    /usr/local/sphinx/bin/searchd
```

客户端测试代码：

```php
	$s = new SphinxClient;  
    $s->setServer("127.0.0.1", 9312);  
  
    $s->setMatchMode(SPH_MATCH_PHRASE);  
    $s->setMaxQueryTime(30);  
    $res = $s->query("宝马",'main'); #[宝马]关键字，[main]数据源source  
    $err = $s->GetLastError();  
    var_dump(array_keys($res['matches']));  
    echo "<br>"."通过获取的ID来读取数据库中的值即可。"."<br>";  
      
    echo '<pre>';  
    var_dump($res);  
    var_dump($err);  
    echo '</pre>';  
```
[详情参考](http://php.net/manual/zh/install.pecl.php)


## [ 教程 ] windows 7 下安装并测试 coreseek(搭建开发环境)

[官方安装指南](http://www.coreseek.cn/products-install/install_on_windows/)

基本配置文件：
<pre>

source src1
{
	type					    = mysql
	sql_host				= localhost
	sql_user				= database_user
	sql_pass				= database_pwd
	sql_db					= database_name
	sql_port				= 3306
	sql_sock				= \tmp\mysql.sock
	sql_query_pre		= SET NAMES utf8
	sql_query				= 
	sql_attr_uint			= 
    sql_query_info		= 

}

index test1
{
	source				= searchBlogSrc
	path				= C:\usr\local\coreseek-3.2.14-win32\var\data\test1
	docinfo				= extern
	mlock				= 0
	charset_type		= zh_cn.utf-8
	charset_dictpath	= C:\usr\local\coreseek-3.2.14-win32\etc
	html_strip				= 1
}

indexer
{
	mem_limit			= 32M
}

searchd
{
	port					= 9312
	log					= C:\usr\local\coreseek-3.2.14-win32\var\log\searchd.log

	query_log			= C:\usr\local\coreseek-3.2.14-win32\var\log\query.log

	read_timeout		= 5

	client_timeout		= 300

	max_children		= 30

	pid_file			= C:\usr\local\coreseek-3.2.14-win32\var\log\searchd.pid

	max_matches			= 1000

	seamless_rotate		= 1

	preopen_indexes		= 0

	unlink_old			= 1
}

</pre>

**windows客户端开发环境一直没能成功使用全文检索**

## Sphinx增量索引实例

## coreseek 参考

[Sphinx中文入门指南](http://www.sphinxsearch.org/sphinx-tutorial)

[轩脉刃de刀光剑影：Coreseek + Sphinx + Mysql + PHP构建中文检索引擎](http://www.cnblogs.com/yjf512/p/3581869.html)

[Sphinx 0.9.9/Coreseek 3.2 参考手册](http://www.coreseek.cn/docs/coreseek_3.2-sphinx_0.9.9.html)


## 参考博文

[牛儿吃草的博客：搭建coreseek(sphinx+mmseg3)详细安装配置+php之sphinx扩展安装+php调用示例 ](http://blog.csdn.net/e421083458/article/details/21529969)

[学习笔记：sphinx分布式索引简介](http://blog.haohtml.com/tag/sphinx)

[websites：Coreseek/sphinx全文检索的了解 ](http://blog.csdn.net/websites/article/details/36626573)


结束语：应该这样学习一个新的技能，先让浮躁的心静下来，用好搜索引擎（最好google）；然后从原理（多来自博客）->安装指南（官方）->例子（多来自博客）->参考手册（官方）一路走下去，别让浮躁打击了自己的信心，不是什么东西一看就会的。。。致自己