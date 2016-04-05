---
layout: post
title:  "设计模式 - 单例模式与实例集（持续更新）"
date: 2016-04-05
categories: 设计模式
tags: 设计模式
keyword: "设计模式, 单例模式，单例模式与实例集"
author: Aidan Dai
---

设计模式系列学习总结之单例模式。

## 单例模式

>单例模式(Singleton Pattern)：确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，它提供全局访问的方法。单例模式是一种对象创建型模式。

![singleton-pattern-uml](/asset/images/article/singleton-pattern-uml.gif)

```java
class TaskManager  
{  
    private static TaskManager tm = null;  
    private TaskManager() {} //初始化窗口  
    public void  displayProcesses() {} //显示进程  
    public void  displayServices() {} //显示服务  
    public static TaskManager getInstance()  
    {  
        if (tm == null)  
        {  
            tm = new TaskManager();  
        }  
        return tm;  
    }  
   	//……  
}  
```

### example1: 负载均衡器的设计与实现

Sunny软件公司承接了一个服务器负载均衡(Load Balance)软件的开发工作，该软件运行在一台负载均衡服务器上，可以将并发访问和数据流量分发到服务器集群中的多台设备上进行并发处理，提高系统的整体处理能力，缩短响应时间。由于集群中的服务器需要动态删减，且客户端请求需要统一分发，因此需要确保负载均衡器的唯一性，只能有一个负载均衡器来负责服务器的管理和请求的分发，否则将会带来服务器状态的不一致以及请求分配冲突等问题。如何确保负载均衡器的唯一性是该软件成功的关键。

![负载均衡器的设计与实现](/asset/images/article/singleton-pattern-example-LoadBalancer.gif)

```java
import java.util.*;  
  
//负载均衡器LoadBalancer：单例类，真实环境下该类将非常复杂，包括大量初始化的工作和业务方法，考虑到代码的可读性和易理解性，只列出部分与模式相关的核心代码  
class LoadBalancer {  
    //私有静态成员变量，存储唯一实例  
    private static LoadBalancer instance = null;  
    //服务器集合  
    private List serverList = null;  
      
    //私有构造函数  
    private LoadBalancer() {  
        serverList = new ArrayList();  
    }  
      
    //公有静态成员方法，返回唯一实例  
    public static LoadBalancer getLoadBalancer() {  
        if (instance == null) {  
            instance = new LoadBalancer();  
        }  
        return instance;  
    }  
      
    //增加服务器  
    public void addServer(String server) {  
        serverList.add(server);  
    }  
      
    //删除服务器  
    public void removeServer(String server) {  
        serverList.remove(server);  
    }  
      
    //使用Random类随机获取服务器  
    public String getServer() {  
        Random random = new Random();  
        int i = random.nextInt(serverList.size());  
        return (String)serverList.get(i);  
    }  
}  

// 客户端测试代码
class Client {  
    public static void main(String args[]) {  
        //创建四个LoadBalancer对象  
        LoadBalancer balancer1,balancer2,balancer3,balancer4;  
        balancer1 = LoadBalancer.getLoadBalancer();  
        balancer2 = LoadBalancer.getLoadBalancer();  
        balancer3 = LoadBalancer.getLoadBalancer();  
        balancer4 = LoadBalancer.getLoadBalancer();  
          
        //判断服务器负载均衡器是否相同  
        if (balancer1 == balancer2 && balancer2 == balancer3 && balancer3 == balancer4) {  
            System.out.println("服务器负载均衡器具有唯一性！");  
        }  
          
        //增加服务器  
        balancer1.addServer("Server 1");  
        balancer1.addServer("Server 2");  
        balancer1.addServer("Server 3");  
        balancer1.addServer("Server 4");  
          
        //模拟客户端请求的分发  
        for (int i = 0; i < 10; i++) {  
            String server = balancer1.getServer();  
            System.out.println("分发请求至服务器： " + server);  
      }  
    }  
}  
```

## 饿汉式单例与懒汉式单例

### 饿汉式单例

![饿汉式单例](/asset/images/article/eager-singleton-pattern.gif)

```java
class EagerSingleton {   
    private static final EagerSingleton instance = new EagerSingleton();   
    private EagerSingleton() { }   
  
    public static EagerSingleton getInstance() {  
        return instance;   
    }     
}
```

### 懒汉式单例类与线程锁定

![懒汉式单例类](/asset/images/article/lazy--singleton-pattern.gif)

```java
class LazySingleton {   
    private volatile static LazySingleton instance = null;   
  
    private LazySingleton() { }   
  
    public static LazySingleton getInstance() {   
        //第一重判断  
        if (instance == null) {  
            //锁定代码块  
            synchronized (LazySingleton.class) {  
                //第二重判断  
                if (instance == null) {  
                    instance = new LazySingleton(); //创建单例实例  
                }  
            }  
        }  
        return instance;   
    }  
}  
```

懒汉式单例类与线程锁定依赖于语言特性。