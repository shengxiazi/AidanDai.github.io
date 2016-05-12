---
layout: post
title:  "【总结】前后端分离的探索（五）"
date: 2016-05-10
categories: web
tags: web
keyword: "RPC, web service，Hessian，RESTful API，前后端分离的探索"
author: Aidan Dai
---

目前互联网上充斥着大量的关于 RESTful API ，如何设计的文章，然而却没有一个”万能“的设计标准：如何鉴权？ RESTful API 格式如何？你的 RESTful API 是否应该加入版本信息？当你开始写一个 app 的时候，特别是后端模型部分已经写完的时候，你不得不殚精竭虑的设计和实现自己 app 的 public RESTful API 部分。因为一旦发布，对外发布的 RESTful API 将会很难改变。

## 五、RESTful API 设计指南

### 1、接口路径设计

#### （1） 接口设计原则

- URI指向的是唯一的资源对象

示例: 指向ID为aidan的users对象

```
GET http://www.example.com/users/aidan
```

- URI可以隐式指向唯一的集合列表

示例: 隐式地指向trades list 集合

```
GET http://www.example.com/trades/(list)
等同于
GET http://www.example.com/trades
```

- 聚合资源必须通过父级资源操作

示例: Profile是User的聚合资源，User有一个唯一且私有的Profile资源，只能通过User操作Profile。

```
更新user_id为123456的Profile资源
PUT http://www.example.com/users/123456/profiles
Request Body:
{
    "full_name": "yanbo.ai",
    "state": "Shanghai",
    "title": "Senior software engineer"
}
```

- 组合资源要避免资源路径嵌套

示例: 一个系统里面包含多个 applications，一个 application 又包含多个 users。那获取 user 资源的路径应该是怎样的？

看一个路径嵌套的例子：

```
GET http://www.example.com/systems/:systemId/applications/:applicationId/users/:userId
```

这样做是不合理的，它会让你的接口变得越来越混乱和缺少灵活性。正确的做法是：

```
GET http://www.example.com/systems/:systemId
GET http://www.example.com/applications/:applicationId 
GET http://www.example.com/users/:userId/
```

### 2、常用 Http Methods

|HTTP Operation|  Description|
|:----|:----|
|GET     |获取，查找|
|POST    |新增创建|
|PUT     |更新|
|PATCH   |部分更新|
|DELETE  |删除|

### 3、URL组成

（1）网络协议(HTTP, HTTPS)

（2）服务器地址

（3）接口名称

（4）?参数列表

（ps：强烈反对URL加入版本号，违背了REST的基本原则；至于向下兼容没必要吧，api更换意味着你服务器上的资源发生变化，所以不能向下兼容的）

### 4、URL定义限制

（1）不使用大写字母

（2）使用中线`-`代替下划线`_`

（3）参数列表应该被encode过

```
GET https://github.com/trades?uid=1
```

### 5、接口分类

（1）资源对象的CURD操作

```
GET http://www.example.com/trades            获取trades列表
GET http://www.example.com/trades/:id        根据id获取单个trade
POST http://www.example.com/trades           创建trade
PUT http://www.example.com/trades/:id        根据id更新trade
PATCH http://www.example.com/trades/:id      根据id部分更新trade
DELETE http://www.example.com/trades/:id     根据id删除trade
```

（2）服务型接口

```
搜索
GET http://www.example.com/services/search?key=filter&category=file
```

（3）系统设置

```
更改界面语言环境
PUT http://www.example.com/settings/gui/lang
{
    "lang": "zh-CN"
}
```

### 6、Oauth2 安全验证

RESTful 服务使用 Oauth2 的方式进行调用授权，使用 http 请求头 Authorization 设置授权码; 必须使用 User-Agent 设置客户端信息, 无 User-Agent 请求头的请求应该被拒绝访问。

Oauth2 的参与者为：客户端，资源所有者，授权服务器，资源服务器。**客户端先从资源所有者得到授权码之后使用授权码从授权服务器得到token，再使用token调用资源服务器获取经过资源所有者授权使用的资源。**这种授权方式的特点有：

（1）资源所有者可以随时撤销授权许可

（2）可以通过撤销token拒绝客户端的调用 

（3）资源服务器可以拒绝客户端的调用 

通过这三种方式可以做到对资源的严格保护。资源的访问权限也把握在资源所有者的手中，而不是资源服务器。 

当然，Oauth2授权框架也允许受信任的客户端直接使用token调用资源服务器获取资源。这种灵活性完全取决于客户端类型和对资源的保护程度。

- 为什么授权码要放在Http Header中?

WEB服务器对访问做记录已经成为了行业的一个标准，访问记录不仅可以用来做访问量统计还能用来做访问特征分析。互联网广告平台就是利用访问记录来做精准营销的。如果token(授权码)包含在URL中就有很大的安全风险。

包含在URL中的token串可能被进行重定向传递。通过这两种方式入侵者可以不通过授权而使用泄漏的授权码访问那些受保护的数据，会造成数据泄漏的风险。

### 7、容错机制

简单处理，采取 http 状态码 [http 状态码完全列表](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)

### 8、返回结果

- JSON

- Hypermedia API

RESTful API最好做到Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。

比如，当用户向www.example.com的根目录发出请求，会得到这样一个文档。

```
{"link": {
  "rel":   "collection https://www.example.com/zoos",
  "href":  "https://api.example.com/zoos",
  "title": "List of zoos",
  "type":  "application/vnd.yourformat+json"
}}
```

上面代码表示，文档中有一个link属性，用户读取这个属性就知道下一步该调用什么API了。rel表示这个API与当前网址的关系（collection关系，并给出该collection的网址），href表示API的路径，title表示API的标题，type表示返回类型。

## 六、参考资料

[玉伯：Web 研发模式演变](https://github.com/lifesinger/blog/issues/184)

[黄国华：Web Service入门](http://blog.jobbole.com/29195/)

[TaobaoUED：前后端分离](http://ued.taobao.org/blog/tag/%E5%89%8D%E5%90%8E%E7%AB%AF%E5%88%86%E7%A6%BB/)

[蒋金楠：我所理解的RESTful Web API [Web标准篇]](http://www.cnblogs.com/artech/p/restful-web-api-01.html)

[蒋金楠：我所理解的RESTful Web API [设计篇]](http://www.cnblogs.com/artech/p/restful-web-api-02.html)

[阮一峰：理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful)

[Gevin：RESTful 架构风格概述](http://blog.igevin.info/posts/restful-architecture-in-general/)

[周梦康：REST的五个关键词](https://mengkang.net/623.html)

[周梦康：深入理解 RESTful Api 架构](https://mengkang.net/620.html)

[NinetyH：RESTful Best Practices](https://zybuluo.com/yanbo-ai/note/17890)

[阮一峰：RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)