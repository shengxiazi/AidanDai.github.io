---
layout: post
title:  "【总结】前后端分离的探索（四）"
date: 2016-05-10
categories: web
tags: web
keyword: "RPC, web service，Hessian，RESTful API，前后端分离的探索"
author: Aidan Dai
---

越来越多的人开始意识到，**网站即软件**，而且是一种新型的软件。这种"互联网软件"采用客户端/服务器模式，建立在分布式体系上，通过互联网通信，具有高延时（high latency）、高并发等特点。网站开发，完全可以采用软件开发的模式。但是传统上，软件和网络是两个不同的领域，很少有交集；软件开发主要针对单机环境，网络则主要研究系统之间的通信。互联网的兴起，使得这两个领域开始融合，**现在我们必须考虑，如何开发在互联网环境中使用的软件**。

## 四、理解RESTful架构

### 1、起源

REST这个词，是[Roy Thomas Fielding](https://en.wikipedia.org/wiki/Roy_Fielding)在他2000年的[博士论文](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)中提出的。


![Roy Thomas Fielding](/asset/images/article/the-exploration-of-the-front-and-back-end-separation-3-002.jpg)

Fielding是一个非常重要的人，他是HTTP协议（1.0版和1.1版）的主要设计者、Apache服务器软件的作者之一、Apache基金会的第一任主席。所以，他的这篇论文一经发表，就引起了关注，并且立即对互联网开发产生了深远的影响。

他这样介绍论文的写作目的：

>本文研究计算机科学两大前沿----软件和网络----的交叉点。长期以来，软件研究主要关注软件设计的分类、设计方法的演化，很少客观地评估不同的设计选择对系统行为的影响。而相反地，网络研究主要关注系统之间通信行为的细节、如何改进特定通信机制的表现，常常忽视了一个事实，那就是改变应用程序的互动风格比改变互动协议，对整体表现有更大的影响。**我这篇文章的写作目的，就是想在符合架构原理的前提下，理解和评估以网络为基础的应用软件的架构设计，得到一个功能强、性能好、适宜通信的架构。**

>(This dissertation explores a junction on the frontiers of two research disciplines in computer science: software and networking. Software research has long been concerned with the categorization of software designs and the development of design methodologies, but has rarely been able to objectively evaluate the impact of various design choices on system behavior. Networking research, in contrast, is focused on the details of generic communication behavior between systems and improving the performance of particular communication techniques, often ignoring the fact that changing the interaction style of an application can have more impact on performance than the communication protocols used for that interaction. My work is motivated by the desire to understand and evaluate the architectural design of network-based application software through principled use of architectural constraints, thereby obtaining the functional, performance, and social properties desired of an architecture. )

### 2、名称

Fielding将他对互联网软件的架构原则，定名为REST，即Representational State Transfer的缩写。我对这个词组的翻译是"表现层状态转化"。

如果一个架构符合REST原则，就称它为RESTful架构。

**要理解RESTful架构，最好的方法就是去理解Representational State Transfer这个词组到底是什么意思，它的每一个词代表了什么涵义。**如果你把这个名称搞懂了，也就不难体会REST是一种什么样的设计。

#### （1）资源（Resources）

REST的名称"表现层状态转化"中，省略了主语。"表现层"其实指的是"资源"（Resources）的"表现层"。

**所谓"资源"，就是网络上的一个实体，或者说是网络上的一个具体信息。**它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的实在。你可以用一个URI（统一资源定位符）指向它，每种资源对应一个特定的URI。要获取这个资源，访问它的URI就可以，因此URI就成了每一个资源的地址或独一无二的识别符。

所谓"上网"，就是与互联网上一系列的"资源"互动，调用它的URI。

#### （2）资源的表述（Representation）

"资源"是一种信息实体，它可以有多种外在表现形式。**我们把"资源"具体呈现出来的形式，叫做它的"表现层"（Representation）。**

比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式；图片可以用JPG格式表现，也可以用PNG格式表现。

URI只代表资源的实体，不代表它的形式。严格地说，有些网址最后的".html"后缀名是不必要的，因为这个后缀名表示格式，属于"表现层"范畴，而URI应该只代表"资源"的位置。它的具体表现形式，应该在HTTP请求的头信息中用Accept和Content-Type字段指定，这两个字段才是对"表现层"的描述。

#### （3）状态转移（State Transfer）

将客户端对资源操作的状态通过 Api 的请求转移到服务端。比如客户端需要删除某个资源，请求完，服务器端该资源的状态也是删除的了。

#### （4）统一接口（Uniform Interface）

REST要求，必须通过统一的接口来对资源执行各种操作。对于每个资源只能执行一组有限的操作。以HTTP/1.1协议为例，HTTP/1.1协议定义了一个操作资源的统一接口，主要包括以下内容：

- 7个HTTP方法：GET/POST/PUT/DELETE/PATCH/HEAD/OPTIONS

- HTTP头信息（可自定义）

- HTTP响应状态代码（可自定义）

- 一套标准的内容协商机制（即接口内容输出之后，各个字段各个对象如何解析的定义）

- 一套标准的缓存机制

- 一套标准的客户端身份认证机制


#### （5）超文本驱动（Hypertext Driven）

就像网页里的超链接，得到当前接口的返回内容，app 布局上对应的各个按钮下一步可能会请求 api 需要在当前 api 予以给出。

举个例子 [https://developer.github.com/v3/git/commits/#get-a-commit](举个例子 https://developer.github.com/v3/git/commits/#get-a-commit 返回的内容为) 返回的内容为

```
{
  "sha": "7638417db6d59f3c431d3e1f261cc637155684cd",
  "url": "https://api.github.com/repos/octocat/Hello-World/git/commits/7638417db6d59f3c431d3e1f261cc637155684cd",
  "author": {
    "date": "2014-11-07T22:01:45Z",
    "name": "Scott Chacon",
    "email": "schacon@gmail.com"
  },
  "committer": {
    "date": "2014-11-07T22:01:45Z",
    "name": "Scott Chacon",
    "email": "schacon@gmail.com"
  },
  "message": "added readme, because im a good github citizen\n",
  "tree": {
    "url": "https://api.github.com/repos/octocat/Hello-World/git/trees/691272480426f78a0138979dd3ce63b77f706feb",
    "sha": "691272480426f78a0138979dd3ce63b77f706feb"
  },
  "parents": [
    {
      "url": "https://api.github.com/repos/octocat/Hello-World/git/commits/1acc419d4d6a9ce985db7be48c6349a0475975b5",
      "sha": "1acc419d4d6a9ce985db7be48c6349a0475975b5"
    }
  ]
}
```

里面的 `parents` 节点里有一个 `url` 字段，而对应的客户端上有一个查看其父节点的按钮，点击那个按钮，就会向这个 `url` 对应的 `api` 发送请求了。

下一步操作的执行，不是客户端需要自身关心的，而是 `api` 返回的文档内容驱动的。

### 3、Server 端代码的基本设计思想

按照 RESTful Api 的规范，严格来说根据 `endpoint` 找到在 server 端映射成一个资源对象，例如通过 `http://www.example.com/users/1` 找到 `UserResource` 对象（ps：当然资源对象没必非要以这种方式命名，我还是比较喜欢叫它 `User`）

而在这个对象里对应着该资源支持的 Http 协议的方法。如果一个对象支持7种方式的请求，则类似于：

```php
class User{
 
    private $user;
 
    public function __constructor($id) {
        $this->user = new UserModel($id);
    }
 
    /**
     * 获取单个用户
     * 从 new User($id) 开始
     * @return
     */
    public function get(){
        return $user;
    }
 
    /**
     * 覆盖用户的全部信息
     * 从 new User($id) 开始
     * @return
     */
    public function put(){
        return true;
    }
 
    /**
     * 只更新用户的部分信息
     * 从 new User($id) 开始
     * @return
     */
    public function patch(){
        return true;
    }
 
    /**
     * 创建一个 User
     * @return
     */
    public function post(){
        return 1;
    }
 
    /**
     * 删除用户
     * @return
     */
    public function delete(){
        return true;
    }
    
}
```

注：HTTP请求方法知识补充

[HTTP请求方法：GET、HEAD、POST、PUT、DELETE、CONNECT、OPTIONS、TRACE](http://itbilu.com/other/relate/EkwKysXIl.html)


### 4、一些常见的误解

#### （1）进一步认识 RESTful Api 

我们知道 URL 全称为“统一资源定位符（Uniform Resource Locator）”，用于描述 Web 资源所在的位置。RESTful Api 是以 HTTP 协议为强烈依托的，它的宗旨就是一个 URL 就应该是一个资源，不能包含任何动作。

```
[POST]     http://www.example.com/users   // 新增

[GET]      http://www.example.com/users/1 // 查询

[PATCH]    http://www.example.com/users/1 // 更新

[PUT]      http://www.example.com/users/1 // 覆盖，全部更新

[DELETE]   http://www.example.com/users/1 // 删除
```

`PUT` 和 `PATCH` 的功能都可以代表更新，但略有不同， `PUT` 大多时候表示更新该资源的全部信息，而 `PATCH` 则更新部分信息。

`PUT` 和 `POST` 又一些功能的重叠，都可以是新建一个资源，POST时，新建资源的地址是由服务器返回给客户端的。也就是说客户端在发送POST请求资源之前还无法预知该资源的地址，这在我们的 Api 开发中非常常见，新建一个帖子，新建一条评论，都如此。

而资源的地址是客户端预先知道的情况则比较少。也有人如此设计而使用 `PUT`，比如需要对 github 上的某人的项目 star ，则可能会设计成：https://github.com/AidanDai/AidanDai.github.io/star 这里把“对这个项目已经点赞过”看成了一个资源，那么就可以用 `PUT` ，因为要删除这个资源时，还是访问这个 URL。

关于这些功能上有交集的地方，可能后面会有一些更加标准的规范或者协议吧。

最后说下 `HEAD` 和 `OPTIONS` ，`HEAD` 请求的是资源的元数据，比如一张照片，的元数据则可能包含了，照片拍摄的设备，地点，时间等。服务器一般将对应资源的元数据置于响应的报头集合返回给客户端，这样的响应一般不具有主体部分。 `OPTIONS` 则是发送一种“探测”请求以确定针对某个目标地址的请求必须具有怎样的约束（比如应该采用怎样的HTTP方法以及自定义的请求报头），然后根据其约束发送真正的请求。

关于过滤筛选，排序和 token 等 query string 是支持使用，和唯一资源的概念并不冲突。

#### （2）无状态性和唯一资源

举一个实际的例子，用户黑名单的 CURD。我们可是设计成

```
[GET]      	/blacklist

[PUT]       /blacklist/{id} #把 id 加入到当前授权用户的黑名单中

[DELETE]   	/blacklist/{id}
```

如上的 api 设计中，首先如上的 url 设计中，通过授权码中获取登录用户 uid。则是有状态的了，其次因为所有的用户访问的资源都是同一个地址，这与唯一资源的概念是相违背的。如果是无状态的，那么就与唯一资源的概念相吻合了。（这只是 restful 所建议的，实际是否需要完全遵守视情况而定）

```
[GET]      	/user/{uid}/blacklist

[PUT]       /user/{uid}/blacklist/{id}  #把 id 加入到当前授权用户的黑名单中

[DELETE]   	/user/{uid}/blacklist/{id}
```

#### （3）REST 状态

理论上来说我们应该以 Http response status code 作为客户端的标准，而不是在 Http body 体里面定义。这样客户端的能够更快速的获取服务端的响应状态码。

但是由于国内某些网络商会劫持状态码非 `200` 的请求，跳转到他们的广告地址。所以大家还是考虑国内的实际情况。

#### （4）REST 架构风格约束

1、客户-服务器（Client-Server）通信只能由客户端单方面发起，表现为请求-响应的形式。

2、无状态（Stateless）通信的会话状态（Session State）应该全部由客户端负责维护。

3、缓存（Cache）响应内容可以在通信链的某处被缓存，以改善网络效率。

4、统一接口（Uniform Interface）通信链的组件之间通过统一的接口相互通信，以提高交互的可见性。

5、分层系统（Layered System）通过限制组件的行为（即，每个组件只能“看到”与其交互的紧邻层），将架构分解为若干等级的层。

### 5、下篇

[【总结】前后端分离的探索（五）](/archives/the-exploration-of-the-front-and-back-end-separation-5)