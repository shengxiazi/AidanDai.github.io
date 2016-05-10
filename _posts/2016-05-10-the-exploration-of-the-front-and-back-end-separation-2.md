---
layout: post
title:  "【总结】前后端分离的探索（二）"
date: 2016-05-10
categories: web
tags: web
keyword: "RPC, web service，Hessian，RESTful API，前后端分离的探索"
author: Aidan Dai
---

简单介绍下 `Web Service` `SOAP` `WSDL` `UDDI` `PRC` `SOA` `REST`！

## 二、Web Service简介

### 1、Web Service的定义

目前对Web Service没有统一的定义。

定义一：Web Service是自包含的、模块化的应用程序，它可以在Web中被描述、发布、查找以及调用。

定义二：Web Service是基于网络的、分布式的模块化组件，它执行特定的任务，遵守具体的技术规范，这些规范使得Web Service能与其他兼任的组件进行操作。

定义三：所谓Web Service是指由企业发布的完成其特别商务需求的在线应用服务，其他公司或应用软件通过Internet来访问并使用这项应用服务。

定义四：**Web Service是一种`面向服务的架构技术`，`通过标准的Web协议提供服务`，目的是`保证不同平台的应用服务可以互操作`。**（[维基百科：Web Service](https://en.wikipedia.org/wiki/Web_service)）

考虑到并没某个独立文档包含一切相关内容，可采用模块化的方式给出对WEB服务的描述，但不能给出一个“绝对全面和准确”的定义。受外部环境和实现技术影响，各方给出的核心定义可能稍有出入，但通常包括：

- SOAP（Simple Object Access Protocol）

它是一种标准化的通讯规范，主要用于Web服务（web service）中。这里之所以说是简单，是因为它是基于已经广泛使用的两个协议：HTTP和XML，所以业界把这种技术称为“它是第一个没有发明任何新技术的技术"。[详情参见](http://baike.sogou.com/v641208.htm?fromTitle=SOAP)

- WSDL（Web Services Description Language）

它是一个用来描述Web服务和说明如何与Web服务通信的XML（标准通用标记语言的子集）语言。[详情参见](http://baike.sogou.com/v54555.htm?fromTitle=WSDL)

- UDDI（Universal Description, Discovery, and Integration）

它是一个基于XML的跨平台的描述规范，可以使世界范围内的企业在互联网上发布自己所提供的服务。

### 2、使用Web Service的方式

-  RPC（Remote Procedure Call Protocol）

Web Service提供一个分布式函数或方法接口供用户调用，这是一种比较传统的方式。通常，在WSDL中对RPC接口进行定义。尽管最初的Web Service广泛采用RPC这种方式部署，但是针对其过于紧密之耦合性的批评声也络绎不绝。原因是RPC式的Web Service服务是利用一个简单的映射，把用户请求直接转换为一个特定语言编写的函数或方法。[详情参见](http://baike.sogou.com/v35613.htm?fromTitle=RPC)

- SOA（Service-Oriented Architecture）

相比RPC式的Web Service，面向服务架构(SOA)得到了大部分主要软件供应商以及业界专家的支持和肯定。因为SOA方式更加关注如何去连接服务而不是去特地某个实现的细节。[详情参见](http://baike.sogou.com/v64569976.htm?fromTitle=SOA&ch=ch.bk.amb)

- REST（Representational state transfer）

表达性状态转移(Representational state transfer，REST)类似于HTTP协议，REST把接口限定在一组HTTP操作中，比如GTP、PUT、DELETE等以供调用，此种服务可以通过WSDL来描述SOAP消息内容，通过HTTP限定动作接口；或者完全在SOAP中对动作进行抽象。[详情参见](http://baike.sogou.com/v650504.htm?fromTitle=REST)


### 3、下篇

[【总结】前后端分离的探索（三）](/archives/the-exploration-of-the-front-and-back-end-separation-3)