---
layout: post
title:  "设计模式 - 工厂方法模式与实例集（持续更新）"
date: 2016-04-05
categories: 设计模式
tags: 设计模式
keyword: "设计模式, 工厂方法模式，工厂方法模式与实例集"
author: Aidan Dai
---

设计模式系列学习总结之工厂方法模式。


## 工厂方法模式

>工厂方法模式(Factory Method Pattern)：定义一个用于创建对象的接口，让子类决定将哪一个类实例化。工厂方法模式让一个类的实例化延迟到其子类。工厂方法模式又简称为工厂模式(Factory Pattern)，又可称作虚拟构造器模式(Virtual Constructor Pattern)或多态工厂模式(Polymorphic Factory Pattern)。工厂方法模式是一种类创建型模式。

![factory-method-uml](/asset/images/article/factory-method-uml.jpg)

### example1: 日志记录器的设计

Sunny软件公司欲开发一个系统运行日志记录器(Logger)，该记录器可以通过多种途径保存系统的运行日志，如通过文件记录或数据库记录，用户可以通过修改配置文件灵活地更换日志记录方式。在设计各类日志记录器时，Sunny公司的开发人员发现需要对日志记录器进行一些初始化工作，初始化参数的设置过程较为复杂，而且某些参数的设置有严格的先后次序，否则可能会发生记录失败。如何封装记录器的初始化过程并保证多种记录器切换的灵活性是Sunny公司开发人员面临的一个难题。

![日志记录器的设计](/asset/images/article/factory-example1.jpg)

```java
//日志记录器接口：抽象产品  
interface Logger {  
    public void writeLog();  
}  

//数据库日志记录器：具体产品  
class DatabaseLogger implements Logger {  
    public void writeLog() {  
        System.out.println("数据库日志记录。");  
    }  
}  

//文件日志记录器：具体产品  
class FileLogger implements Logger {  
    public void writeLog() {  
        System.out.println("文件日志记录。");  
    }  
}  

//日志记录器工厂接口：抽象工厂  
interface LoggerFactory {  
    public Logger createLogger();  
}  

//数据库日志记录器工厂类：具体工厂  
class DatabaseLoggerFactory implements LoggerFactory {  
    public Logger createLogger() {  
            //连接数据库，代码省略  
            //创建数据库日志记录器对象  
            Logger logger = new DatabaseLogger();   
            //初始化数据库日志记录器，代码省略  
            return logger;  
    }     
}  

//文件日志记录器工厂类：具体工厂  
class FileLoggerFactory implements LoggerFactory {  
    public Logger createLogger() {  
            //创建文件日志记录器对象  
            Logger logger = new FileLogger();   
            //创建文件，代码省略  
            return logger;  
    }     
}  

//客户端测试代码：
class Client {  
    public static void main(String args[]) {  
        LoggerFactory factory;  
        Logger logger;  
        factory = new FileLoggerFactory(); //可引入配置文件实现  
        logger = factory.createLogger();  
        logger.writeLog();  
    }  
}  
```

- 反射与配置文件

 Java反射(Java Reflection)是指在程序运行时获取已知名称的类或已有对象的相关信息的一种机制，包括类的方法、属性、父类等信息，还包括实例的创建和实例类型的判断等。在反射中使用最多的类是Class，Class类的实例表示正在运行的Java应用程序中的类和接口，其forName(String className)方法可以返回与带有给定字符串名的类或接口相关联的 Class对象，再通过Class对象的newInstance()方法创建此对象所表示的类的一个新实例，即通过一个类名字符串得到类的实例。如创建一个字符串类型的对象，其代码如下：

```java
//通过类名生成实例对象并将其返回  
Class c=Class.forName("String");  
Object obj=c.newInstance();  
return obj;  
```
利用反射技术实现工厂方法模式客户端代码如下：

```xml
<?xml version="1.0"?>  
<config>  
    <className>FileLoggerFactory</className>  
</config>  
```

```java
//工具类XMLUtil.java  
import javax.xml.parsers.*;  
import org.w3c.dom.*;  
import org.xml.sax.SAXException;  
import java.io.*;  

public class XMLUtil {  
//该方法用于从XML配置文件中提取具体类类名，并返回一个实例对象  
    public static Object getBean() {  
        try {  
            //创建DOM文档对象  
            DocumentBuilderFactory dFactory = DocumentBuilderFactory.newInstance();  
            DocumentBuilder builder = dFactory.newDocumentBuilder();  
            Document doc;                             
            doc = builder.parse(new File("config.xml"));   

            //获取包含类名的文本节点  
            NodeList nl = doc.getElementsByTagName("className");  
            Node classNode=nl.item(0).getFirstChild();  
            String cName=classNode.getNodeValue();  

            //通过类名生成实例对象并将其返回  
            Class c=Class.forName(cName);  
            Object obj=c.newInstance();  
            return obj;  
        }     
        catch(Exception e) {  
            e.printStackTrace();  
            return null;  
         }  
    }  
}

//客户端代码
class Client {  
    public static void main(String args[]) {  
        LoggerFactory factory;  
        Logger logger;  
        factory = (LoggerFactory)XMLUtil.getBean(); //getBean()的返回类型为Object，需要进行强制类型转换  
        logger = factory.createLogger();  
        logger.writeLog();  
    }  
}  
```

- 重载的工厂方法

![重载的工厂方法结构图](/asset/images/article/override-factory-example1.jpg)

```java
// 抽象工厂LoggerFactory
interface LoggerFactory {  
    public Logger createLogger();  
    public Logger createLogger(String args);  
    public Logger createLogger(Object obj);  
}  

 // 具体工厂类DatabaseLoggerFactory
 class DatabaseLoggerFactory implements LoggerFactory {  
     public Logger createLogger() {  
             //使用默认方式连接数据库，代码省略  
             Logger logger = new DatabaseLogger();   
             //初始化数据库日志记录器，代码省略  
             return logger;  
     }  

     public Logger createLogger(String args) {  
             //使用参数args作为连接字符串来连接数据库，代码省略  
             Logger logger = new DatabaseLogger();   
             //初始化数据库日志记录器，代码省略  
             return logger;  
     }     

     public Logger createLogger(Object obj) {  
             //使用封装在参数obj中的连接字符串来连接数据库，代码省略  
             Logger logger = new DatabaseLogger();   
             //使用封装在参数obj中的数据来初始化数据库日志记录器，代码省略  
             return logger;  
     }     
 }  

 //其他具体工厂类代码省略  
 ```

 - 工厂方法的隐藏

 ![隐藏工厂方法后的日志记录器结构图](/asset/images/article/abstract-factory-example1.jpg)

 ```java
 //改为抽象类  
abstract class LoggerFactory {  
    //在工厂类中直接调用日志记录器类的业务方法writeLog()  
    public void writeLog() {  
        Logger logger = this.createLogger();  
        logger.writeLog();  
    }  

    public abstract Logger createLogger();    
}

// 客户端
class Client {  
    public static void main(String args[]) {  
        LoggerFactory factory;  
        factory = (LoggerFactory)XMLUtil.getBean();  
        factory.writeLog(); //直接使用工厂对象来调用产品对象的业务方法  
    }  
}  
```