---
layout: post
title:  "设计模式 - 原型模式与实例集（持续更新）"
date: 2016-04-07
categories: 设计模式
tags: 设计模式
keyword: "设计模式, 原型模式，原型模式与实例集"
author: Aidan Dai
---

设计模式系列学习总结之原型模式。

## 原型模式

原型模式(Prototype  Pattern)：使用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。原型模式是一种对象创建型模式。

```java
class ConcretePrototype implements Prototype
{
	private String  attr; //成员属性
	public void  setAttr(String attr)
	{
	    this.attr = attr;
	}

	public String  getAttr()
	{
	    return this.attr;
	}

	public Prototype clone() //克隆方法
	{
	    Prototype  prototype = new ConcretePrototype(); //创建新对象
	    prototype.setAttr(this.attr);
	    return prototype;
	}
}

//客户端

Prototype obj1  = new ConcretePrototype();
obj1.setAttr("Sunny");

Prototype obj2  = obj1.clone();
```
![prototype-pattern](/asset/images/article/prototype-pattern.gif)

### example1：大同小异的工作周报

Sunny软件公司一直使用自行开发的一套OA (Office Automatic，办公自动化)系统进行日常工作办理，但在使用过程中，越来越多的人对工作周报的创建和编写模块产生了抱怨。追其原因，Sunny软件公司的OA管理员发现，由于某些岗位每周工作存在重复性，工作周报内容都大同小异，如图7-1工作周报示意图。这些周报只有一些小地方存在差异，但是现行系统每周默认创建的周报都是空白报表，用户只能通过重新输入或不断复制粘贴来填写重复的周报内容，极大降低了工作效率，浪费宝贵的时间。如何快速创建相同或者相似的工作周报，成为Sunny公司OA开发人员面临的一个新问题。

![wekend-summary](/asset/images/article/wekend-summary.gif)

![快速创建工作周报结构图](/asset/images/article/wekend-summary-result-method.gif)

```java
//工作周报WeeklyLog：具体原型类，考虑到代码的可读性和易理解性，只列出部分与模式相关的核心代码
class WeeklyLog implements Cloneable
{
	private  String name;
	private  String date;
	private  String content;

	public  void setName(String name) {
		this.name  = name;
	}

	public  void setDate(String date) {
		this.date  = date;
	}

	public  void setContent(String content) {
		this.content  = content;
	}

	public  String getName() {
		return  (this.name);
	}
	public  String getDate() {
		return  (this.date);
	}

	public  String getContent() {
		return  (this.content);
	}

	//克隆方法clone()，此处使用Java语言提供的克隆机制
	public WeeklyLog clone()
	{
		Object obj = null;
		try
		{
			obj = super.clone();
			return (WeeklyLog)obj;     
		}
		catch(CloneNotSupportedException e)
		{
			System.out.println("不支持复制！");
			return null;
		}
	}
}

//客户端测试代码
class Client
{
	public static void main(String args[])
	{
		WeeklyLog log_previous = new WeeklyLog();  //创建原型对象
		log_previous.setName("张无忌");
		log_previous.setDate("第12周");
		log_previous.setContent("这周工作很忙，每天加班！");

		System.out.println("****周报****");
		System.out.println("周次：" +  log_previous.getDate());
		System.out.println("姓名：" +  log_previous.getName());
		System.out.println("内容：" +  log_previous.getContent());
		System.out.println("--------------------------------");

		WeeklyLog  log_new;
		log_new  = log_previous.clone(); //调用克隆方法创建克隆对象
		log_new.setDate("第13周");
		System.out.println("****周报****");
		System.out.println("周次：" + log_new.getDate());
		System.out.println("姓名：" + log_new.getName());
		System.out.println("内容：" + log_new.getContent());
	}
}
```

### example2：带附件的周报

#### 1.浅克隆

浅克隆中，如果原型对象的成员变量是值类型，将复制一份给克隆对象；如果原型对象的成员变量是引用类型，则将引用对象的地址复制一份给克隆对象，也就是说原型对象和克隆对象的成员变量指向相同的内存地址。简单来说，在浅克隆中，当对象被复制时只复制它本身和其中包含的值类型的成员变量，而引用类型的成员对象并没有复制，如下图所示：

![clone](/asset/images/article/clone.gif)

为了让大家更好地理解浅克隆和深克隆的区别，我们首先使用浅克隆来实现工作周报和附件类的复制，其结构如下图所示：

![clone-example](/asset/images/article/attachment-weeklog.gif)

```java
// 附件类
class Attachment
{
	private  String name; //附件名

	public  void setName(String name)
	{
		this.name  = name;
	}

	public  String getName()
	{
		return  this.name;
	}

	public void download()
	{
		System.out.println("下载附件，文件名为" + name);
	}
}


// 工作周报WeeklyLog
class WeeklyLog implements Cloneable
{
	// 为了简化设计和实现，假设一份工作周报中只有一个附件对象，实际情况中可以包含多个附件，可以通过List等集合对象来实现
	private Attachment attachment;
	private String name;
	private  String date;
	private  String content;

	public void setAttachment(Attachment  attachment) {
		this.attachment = attachment;
	}

	public  void setName(String name) {
		this.name  = name;
	}

	public  void setDate(String date) {
		this.date  = date;
	}

	public  void setContent(String content) {
		this.content  = content;
	}

	public Attachment  getAttachment(){
		return (this.attachment);
	}

	public  String getName() {
		return  (this.name);
	}

	public  String getDate() {
		return  (this.date);
	}

	public  String getContent() {
		return  (this.content);
	}

	//使用clone()方法实现浅克隆
	public WeeklyLog clone()
	{
		Object obj = null;
		try
		{
			obj = super.clone();
			return (WeeklyLog)obj;
		}
		catch(CloneNotSupportedException  e)
		{
			System.out.println("不支持复制！");
			return null;
		}
	}
}

// 客户端代码
class Client
{
	public  static void main(String args[])
	{
		WeeklyLog  log_previous, log_new;

		log_previous  = new WeeklyLog(); //创建原型对象
		Attachment  attachment = new Attachment(); //创建附件对象
		log_previous.setAttachment(attachment);  //将附件添加到周报中

		log_new  = log_previous.clone(); //调用克隆方法创建克隆对象
		
		//比较周报
		System.out.println("周报是否相同？ " + (log_previous ==  log_new));
		
		//比较附件
		System.out.println("附件是否相同？ " +  (log_previous.getAttachment() == log_new.getAttachment()));
	}
}
```

#### 2.深克隆

在深克隆中，无论原型对象的成员变量是值类型还是引用类型，都将复制一份给克隆对象，深克隆将原型对象的所有引用对象也复制一份给克隆对象。简单来说，在深克隆中，除了对象本身被复制外，对象所包含的所有成员变量也将复制，如下图所示：

![deepclone](/asset/images/article/deepclone.gif)

在Java语言中，如果需要实现深克隆，可以通过序列化(Serialization)等方式来实现。序列化就是将对象写到流的过程，写到流中的对象是原有对象的一个拷贝，而原对象仍然存在于内存中。通过序列化实现的拷贝不仅可以复制对象本身，而且可以复制其引用的成员对象，因此通过序列化将对象写到一个流中，再从流里将其读出来，可以实现深克隆。需要注意的是能够实现序列化的对象其类必须实现Serializable接口，否则无法实现序列化操作。下面我们使用深克隆技术来实现工作周报和附件对象的复制，由于要将附件对象和工作周报对象都写入流中，因此两个类均需要实现Serializable接口，其结构如下图所示：

![deepclone-example](/asset/images/article/deepclone-example.gif)

```java

import  java.io.*;

//附件类
class  Attachment implements Serializable
{
	private  String name; //附件名

	public  void setName(String name)
	{
		this.name  = name;
	}

	public  String getName()
	{
		return  this.name;
	}

	public void download()
	{
		System.out.println("下载附件，文件名为" + name);
	}
}

import  java.io.*;

//工作周报类
class  WeeklyLog implements Serializable
{
	private  Attachment attachment;
	private  String name;
	private  String date;
	private  String content;

	public  void setAttachment(Attachment attachment) {
		this.attachment  = attachment;
	}

	public  void setName(String name) {
		this.name  = name;
	}

	public  void setDate(String date) {
		this.date  = date;
	}

	public  void setContent(String content) {
		this.content  = content;
	}

	public  Attachment getAttachment(){
		return  (this.attachment);
	}

	public  String getName() {
		return  (this.name);
	}

	public  String getDate() {
		return  (this.date);
	}

	public  String getContent() {
		return  (this.content);
	}

	//使用序列化技术实现深克隆
	public WeeklyLog deepClone() throws  IOException, ClassNotFoundException, OptionalDataException
	{
		//将对象写入流中
		ByteArrayOutputStream bao = new  ByteArrayOutputStream();
		ObjectOutputStream oos = new  ObjectOutputStream(bao);
		oos.writeObject(this);

		//将对象从流中取出
		ByteArrayInputStream bis = new  ByteArrayInputStream(bao.toByteArray());
		ObjectInputStream ois = new  ObjectInputStream(bis);
		return  (WeeklyLog)ois.readObject();
	}
}

// 客户端代码
class Client
{
	public  static void main(String args[])
	{
		WeeklyLog  log_previous, log_new = null;
		log_previous  = new WeeklyLog(); //创建原型对象
		Attachment  attachment = new Attachment(); //创建附件对象
		log_previous.setAttachment(attachment);  //将附件添加到周报中
		try
		{
			log_new =  log_previous.deepClone(); //调用深克隆方法创建克隆对象                  
		}
		catch(Exception e)
		{
			System.err.println("克隆失败！");
		}

		//比较周报
		System.out.println("周报是否相同？ " + (log_previous ==  log_new));

		//比较附件
		System.out.println("附件是否相同？ " +  (log_previous.getAttachment() == log_new.getAttachment()));
	}
}
```

### example3：原型管理器设计实现公文管理器

原型管理器(Prototype Manager)是将多个原型对象存储在一个集合中供客户端使用，它是一个专门负责克隆对象的工厂，其中定义了一个集合用于存储原型对象，如果需要某个原型对象的一个克隆，可以通过复制集合中对应的原型对象来获得。在原型管理器中针对抽象原型类进行编程，以便扩展。其结构如下图所示：

![prototype-manager](/asset/images/article/prototype-manager.gif)

Sunny软件公司在日常办公中有许多公文需要创建、递交和审批，例如《可行性分析报告》、《立项建议书》、《软件需求规格说明书》、《项目进展报告》等，为了提高工作效率，在OA系统中为各类公文均创建了模板，用户可以通过这些模板快速创建新的公文，这些公文模板需要统一进行管理，系统根据用户请求的不同生成不同的新公文。

![officialDocument](/asset/images/article/officialDocument.gif)

```java
import java.util.*;
 
// 抽象公文接口，也可定义为抽象类，提供clone()方法的实现，将业务方法声明为抽象方法
interface OfficialDocument extends Cloneable
{
   public  OfficialDocument clone();
   public  void display();
}
 
// 可行性分析报告(Feasibility Analysis Report)类
class FAR implements OfficialDocument
{
	public  OfficialDocument clone()
	{
		OfficialDocument  far = null;
		try
		{
			far = (OfficialDocument)super.clone();
		}
		catch(CloneNotSupportedException  e)
		{
			System.out.println("不支持复制！");
		}
		return  far;
	}

	public  void display()
	{
		System.out.println("《可行性分析报告》");
	}
}
 
// 软件需求规格说明书(Software Requirements Specification)类
class SRS implements OfficialDocument
{
	public  OfficialDocument clone()
	{
		OfficialDocument  srs = null;
		try
		{
			srs  = (OfficialDocument)super.clone();
		}
		catch(CloneNotSupportedException  e)
		{ 
			System.out.println("不支持复制！");
		}
		return  srs;
	}

	public  void display()
	{
		System.out.println("《软件需求规格说明书》");
	}
}
 
// 原型管理器（使用饿汉式单例实现）
class  PrototypeManager
{
    //定义一个Hashtable，用于存储原型对象
    private Hashtable ht = new Hashtable();
    private static PrototypeManager pm =  new PrototypeManager();
      
    //为Hashtable增加公文对象   
    private PrototypeManager()
    {
        ht.put("far",new  FAR());
        ht.put("srs",new  SRS());               
    }
  
    //增加新的公文对象
    public void addOfficialDocument(String  key, OfficialDocument doc)
    {
    	ht.put(key,doc);
    }

    //通过浅克隆获取新的公文对象
    public OfficialDocument  getOfficialDocument(String key)
    {
    	return  ((OfficialDocument)ht.get(key)).clone();
    }

    public static PrototypeManager  getPrototypeManager()
    {
    	return pm;
    }
}

// 客户端代码如下所示：
class Client
{
	public  static void main(String args[])
	{
		//获取原型管理器对象
		PrototypeManager pm =  PrototypeManager.getPrototypeManager();  

		OfficialDocument  doc1, doc2, doc3, doc4;

		doc1 = pm.getOfficialDocument("far");
		doc1.display();

		doc2  = pm.getOfficialDocument("far");
		doc2.display();

		System.out.println(doc1  == doc2);

		doc3  = pm.getOfficialDocument("srs");
		doc3.display();

		doc4  = pm.getOfficialDocument("srs");
		doc4.display();

		System.out.println(doc3  == doc4);
	}
}
```
