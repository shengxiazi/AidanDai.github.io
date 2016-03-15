#centos 7.0 搭建node.js环境

注：本文永久更新链接，markdown格式源码  [Github: Aidan Dai](https://github.com/AidanDai/blog)

******************************

[TOC]

前言：由于自己也是刚刚接触linux，如果文章有错误还请大家指出。

[Nodejs Downloads](https://nodejs.org/en/download/)

![Nodejs Downloads](http://img.blog.csdn.net/20151227235830398)

##Linux源码编译安装Nodejs

```shell
wget https://nodejs.org/dist/v4.2.4/node-v4.2.4.tar.gz   #下载源文件

tar -xvf node-v4.2.4.tar.gz   #解压源文件

cd node-v4.2.4

./configure   --prefix=/usr/local/bin/node  # 执行configure文件

make #编译程序

make install #安装程序

cp /usr/local/bin/node /usr/sbin/
```

##源码编译安装程序流程详解


####Linux下程序大都是由以下几部分组成：
*********
1、二进制文件：也就是可以运行的程序文件

2、库文件：就是通常我们见到的lib目录下的文件

3、配置文件：这个不必多说，都知道

4、帮助文档：通常是我们在Linux下用man命令查看的命令的文档
*****************

####Linux程序的存放目录大致有四个地方：

*****************
<pre>/etc
/bin		->			/usr/bin
/sbin		->			/usr/sbin
/lib		->			/usr/lib
</pre>

详情请参考：[CentOS目录结构详解](http://www.linuxidc.com/Linux/2015-03/115440.htm)

************

####编译安装源程序
************

- 查看当前是否安装了gcc编译器，没有可以先用yum安装gcc
```shell
gcc --version  #查看是否安装gcc
```

- 解压源码包，例如：
```shell
tar -xvf nginx-1.7.7.tar.gz  #解压源码包
```
- 进入解压好的源码包：
```shell
cd nginx-1.7.7    #进入源码包
```

-  执行configure文件，此文件有两个功能：

	-  1、让用户选定编译特性；
	- 2、检查编译环境。configure执行后将生成MakeFile文件。例如：
```shell
./configure --prefix=/usr/local/nginx --conf-path=/etc/nginx/nginx.conf
```

其中我们通过**--prefix**制定了安装路径，通过**--conf-path**制定了配置文件的具体位置。注意：不是所有的程序的configure参数都是一样的；可以使用<pre> ./configure --help</pre>查看详细参数说明。如果该程序所依赖的库在当前系统中没有安装，则会导致configure最后报错，遇到这种情况就需要你先安装依赖库。

- 执行make命令，编译程序
```shell
make
```

- 编译成功后就可以安装了，执行如下命令
```shell
make install
```

####关于卸载

- 进入编译后的软件目录，即安装时的目录
- 执行反安装命令：
```shell
make uninstall
```

参考文章：

[linux社区：Linux下通过源码编译安装程序](http://www.linuxidc.com/Linux/2015-03/114689.htm)

[Linux软件安装常用方](http://soft.zdnet.com.cn/software_zone/2008/0513/858582.shtml)
