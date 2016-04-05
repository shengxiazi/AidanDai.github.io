---
layout: post
title:  "设计模式 - 简单工厂模式与实例集（持续更新）"
date: 2016-04-05
categories: 设计模式
tags: 设计模式
keyword: "设计模式, 简单工厂模式，简单工厂模式与实例集"
author: Aidan Dai
---

先说明下，这篇博客是我在学习设计模式过程中一系列模式总结中的一篇，在这些模式总结中我会不断的补充每种模式的实例集，他们与语言无关，重点是理解各种设计模式及其应用场景，以及面向对象软件七大设计原则（参见我的另一篇博客[面向对象设计原则总结](/archives/summary-of-object-oriented-design-principle)）；在这里你将看到各种强类型的语言，C++、C#、java等，为什么没有弱类型语言呢？个人觉得程序员只有使用强类型的语言才能让自己对接口编程或对抽象编程有更深入的理解，也就是所谓面向对象的精华所在，用好封装、继承与多态。然而其他语言体现的并不是特变明显，每一门弱类型的语言设计它们的前辈们已经帮我们考虑过这些了，所以要想学好设计模式，还是通过强类型语言的实际例子，把它理解透彻了，然后我们再在其它项目中用弱类型语言去模仿这些经典的设计模式的实现！去展现面向对象的精华！

## 简单工厂模式

>简单工厂模式(Simple Factory Pattern)：定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的实例通常都具有共同的父类。因为在简单工厂模式中用于创建实例的方法是静态(static)方法，因此简单工厂模式又被称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。

![easy-factory-uml](/asset/images/article/easy-factory-uml.jpg)

### example1: 图表库的设计

Sunny软件公司欲基于Java语言开发一套图表库，该图表库可以为应用系统提供各种不同外观的图表，例如柱状图、饼状图、折线图等。Sunny软件公司图表库设计人员希望为应用系统开发人员提供一套灵活易用的图表库，而且可以较为方便地对图表库进行扩展，以便能够在将来增加一些新类型的图表。

![图表库的设计](/asset/images/article/easy-factory-example1.jpg)

```java
//抽象图表接口：抽象产品类  
interface Chart {  
    public void display();  
}  

//柱状图类：具体产品类  
class HistogramChart implements Chart {  
    public HistogramChart() {  
        System.out.println("创建柱状图！");  
    }  

    public void display() {  
        System.out.println("显示柱状图！");  
    }  
}  

//饼状图类：具体产品类  
class PieChart implements Chart {  
    public PieChart() {  
        System.out.println("创建饼状图！");  
    }  

    public void display() {  
        System.out.println("显示饼状图！");  
    }  
}  

//折线图类：具体产品类  
class LineChart implements Chart {  
    public LineChart() {  
        System.out.println("创建折线图！");  
    }  

    public void display() {  
        System.out.println("显示折线图！");  
    }  
}  

//图表工厂类：工厂类  
class ChartFactory {  
    //静态工厂方法  
    public static Chart getChart(String type) {  
        Chart chart = null;  
        if (type.equalsIgnoreCase("histogram")) {  
            chart = new HistogramChart();  
            System.out.println("初始化设置柱状图！");  
        }  
        else if (type.equalsIgnoreCase("pie")) {  
            chart = new PieChart();  
            System.out.println("初始化设置饼状图！");  
        }  
        else if (type.equalsIgnoreCase("line")) {  
            chart = new LineChart();  
            System.out.println("初始化设置折线图！");              
        }  
        return chart;  
    }  
}  

// 客户端测试代码
class Client {  
    public static void main(String args[]) {  
        Chart chart;  
        chart = ChartFactory.getChart("histogram"); //通过静态工厂方法创建产品  
        chart.display();  
    }  
}  
```