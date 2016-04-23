---
layout: post
title:  "【笔记】HTML5的文档节段和纲要"
date:   2016-03-15
categories: html
tags: html5
keyword: "html5,HTML5的文档节段,纲要"
author: Aidan Dai
---

## 一、HTML4的文档结构

文档结构，即，<body>标记之间内容的语义结构，对呈现页面给用户是重要的。HTML4用文档中章节和子章节的概念去描述文档结构。一个章节由一个包含着标题元素（h1-h6）的div元素表示。这些html划分元素（HTML Dividing Elements）和标题元素（HTML Heading Elements）形成了文档的结构和纲要。

```html
<h1>Forest elephants</h1>
  <p>In this section, we discuss the lesser known forest elephants.
    ...this section continues...
  <h2>Habitat</h2>
  <p>Forest elephants do not live in trees but among them.
    ...this subsection continues...
  <h2>Diet</h2>
<h1>Mongolian gerbils</h1>
```

<pre>
1. Forest elephants
   1.1 Habitat
   1.2 Diet
2. Mongolian gerbils
</pre>

>HTML div元素（ &lt;div&gt;elements）并不强制性地定义一个章节。一个HTML 标题元素（ HTML Heading Element）的出现就足以意味着新的章节.

## 二、HTML5解决的问题

>HTML 4 的文档结构定义和其隐含的大纲算法非常粗糙而且造成了很多问题：

>- 定义语义性章节的&lt;div&gt; 元素的用法,如果没有为class属性赋以特殊的值，使生成自动生成大纲的算法变得不可能 ("一个div元素&lt;div&gt; 是不是大纲的一部分, 定义的是章节还是子章节?" 或者 "该div元素 &lt;div&gt;是仅仅为了样式化?")。换句话说, HTML4规范在章节的定义和章节的范围都不精确。 自动生成大纲是重要的,尤其是在倾向于通过根据文档大纲内容去展示内容的辅助技术（ assistive technology）。 HTML5 在自动生成大纲算法的过程中去掉了div元素（&lt;div&gt;），并新增了一个元素，section元素（&lt;section&gt;）。

>- 合并多个文档是困难的：主文档中包含子文档意味着改变HTML标题元素的级别，以使得文档大纲能够保持下来。 这个已经被HTML5的新的章节元素解决了，因为新引入的元素(&lt;article&gt;, &lt;section&gt;, &lt;nav&gt; 和 &lt;aside&gt;) 总是距离其最近的祖先章节的子章节, 与子文档章节内部的标题没有关系.

>- HTML4中，所有的章节都是文档大纲中的一部分。但是文档并不总是这样。文档可以包含那些不是大纲的特殊章节, 但是与文档有关的, 就像广告块和解释区域。 HTML5 引入aside元素 &lt;aside&gt;使得这样的节点不会插入到主纲要中。

>- 另外, 因为在 HTML4中任何的部分都是文档大纲的一部分, 没有办法产生与网站相关而不是与文档相关的节段，比如logos,menus,目录或版权信息和法律声明。为了这个目的, HTML5 引入了三个特殊的节段 元素： 包含链接集合的nav元素&lt;nav&gt; , 例如目录, 包含网站相关信息的footer元素&lt;footer&gt; 和header元素 &lt;header&gt; 。

## 三、HTML5的大纲算法

### 1、在HTML5中定义章节

>&lt;body&gt; 元素中的所有内容都是节段中的一部分。节段在HTML5中是可以嵌套的。Beside the main section, defined by the &lt;body&gt; element, section limits are defined either explicitly or implicitly. 显示定义的节段 是通过&lt;body&gt;,  &lt;section&gt;,  &lt;article&gt;,  &lt;aside&gt;, &lt;footer&gt;,  &lt;header&gt;和 &lt;nav&gt; 这些标记中的内容。

>*注意：每个section可以有自己的标题结构。因此，即使是一个嵌套的section也能有&lt;h1&gt;. 具体查看 Defining Headings in HTML5.*

example:

```html
<section>
  <h1>Forest elephants</h1>

  <section>     
    <h1>Introduction</h1>     
    <p>In this section, we discuss the lesser known forest elephants.</p>
  </section>

  <section>     
    <h1>Habitat</h1>
    <p>Forest elephants do not live in trees but among them.</p>
  </section>

  <aside>
    <p>advertising block</p>
  </aside>
</section>

<footer>
  <p>(c) 2010 The Example company</p>
</footer>
```

<pre>
1. Forest elephants
   1.1 Introduction
   1.2 Habitat
   1.3 Section (aside)
</pre>

example:

```html
<section>
  <h1>Forest elephants</h1>    
  <p>In this section, we discuss the lesser known forest elephants.
    ...this section continues...
  <section>
    <h2>Habitat</h2>  
    <p>Forest elephants do not live in trees but among them.
        ...this subsection continues...
  </section>
</section>
<section>
  <h3>Mongolian gerbils</h3>
  <p>In this section, we discuss the famous mongolian gerbils.
     ...this section continues...
</section>
```

<pre>
1. Forest elephants
   1.1 Habitat
2. Mongolian gerbils
</pre>

注意标题元素的级别（例子中的第一个顶层节段的 &lt;h1&gt;，子节段中的&lt;h2&gt; 和第二个顶层节段中的&lt;h3&gt;）并不重要。（任何级别可以用作显示定义的节段的标题，虽然这种做法并不推荐。）

### 2、隐式分节

**不是个好习惯！**

```html
<body>
  <h1>Mammals</h1>
  <h2>Whales</h2>
  <p>In this section, we discuss the swimming whales.
    ...this section continues...
  <section>
    <h3>Forest elephants</h3>  
    <p>In this section, we discuss the lesser known forest elephants.
      ...this section continues...
    <h3>Mongolian gerbils</h3>
      <p>Hordes of gerbils have spread their range far beyond Mongolia.
         ...this subsection continues...
    <h2>Reptiles</h2>
      <p>Reptiles are animals with cold blood.
          ...this subsection continues...
  </section>
</body>
```

<pre>
1. Mammals
	1.1 Whales (implicitly defined by the h2 element)
	1.2 Forest elephants (explicitly defined by the section element)
	1.3 Mongolian gerbils (implicitly defined by the h3 element, which closes the previous section at the same time)
	1.4 Reptiles (implicitly defined by the h2 element, which closes the previous section at the same time)
</pre>

### 3、分结根

>分节根是一个HTML元素，这个元素可以拥有自己的大纲，但是元素内部的节段和标题对其祖先的大纲没有贡献。与文档的逻辑分节根&lt;body&gt;元素相比，这些元素经常在页面中引入外部内容：&lt;blockquote&gt;, &lt;details&gt;, &lt;fieldset&gt;, &lt;figure&gt; 和&lt;td&gt;。

example:

```html
<section>
  <h1>Forest elephants</h1> 
  <section>
    <h2>Introduction</h2>
    <p>In this section, we discuss the lesser known forest elephants</p>
  </section>
  <section>
    <h2>Habitat</h2>
    <p>Forest elephants do not live in trees but among them. Let's
       look what scientists are saying in "
       <cite>The Forest Elephant in Borneo</cite>
       ":</p>
    <blockquote>
       <h1>Borneo</h1>
       <p>The forest element lives in Borneo...</p>
    </blockquote>
  </section>
</section>
```

<pre>
1. Forest elephants
	1.1 Introduction
	1.2 Habitat
</pre>

>这个大纲并不包含 &lt;blockquote&gt; 元素的内部大纲。&lt;blockquote&gt; 元素是一个外部引用，是一个分节根并隔离了他内部的大纲

### 4、大纲之外的节段

>HTML5引入了4个新的元素，用来定义那些不属于web文档主要大纲中的节段。

>1、HTML 侧边分节元素 (&lt;aside&gt;) 定义了这样的节段, 虽然是主要的分节元素, 但并不属于主要的文档流, 就像解释栏或广告栏. aside元素内部有自己的大纲，但并不计入文档大纲中

>2、HTML 导航分节元素 (&lt;nav&gt;) 定义的节段包含了很多导航links。文档中可以有好几个这样的元素，比如文档内部的链接，就像目录，和链接到其他站点的导航links。这些链接并不是主文档流和文档大纲中的一部分 ，并且能够特别让屏幕浏览器和类似的辅助技术从一开始就不渲染该标记里的内容。

>3、HTML 头部分节元素 (&lt;header&gt;) 定义了页面的头部,通常会包含logo和站点名称以及可能有的水平菜单. 尽管名字是header，但是不一定是在页面的开始

>4、HTML 脚部分节元素 (&lt;footer&gt;) 定义了页脚, 通常会包含版权信息和法律声明以及一些其他链接。同样，其不一定是在页面的底部出现。

## 四、分节元素中的地址和发表时间

>一个文档可以由不同作者的不同节段组成。一个从其他作者而不是文档作者写的节段用&lt;article&gt;元素定义。因此， &lt;address&gt; 元素连接到距离最近的&lt;body&gt;或&lt;article&gt; 祖先元素。

>同样的，新的HTML5标记 &lt;time&gt;元素，使用pubdate布尔值，表示整个文档的发布时间，分别给文章，与其最近的&lt;body&gt;元素或&lt;article&gt; 元素的祖先元素相关。

## 五、在不支持HTML5的浏览器器中使用HTML5

>分节和标题元素应该在大部分的不支持HTML5的浏览器中工作。尽管不支持，但不必使用特殊的DOM接口。仅仅只需要一个特殊的CSS样式，因为未知元素默认会样式化为display:inline：

```css
section, article, aside, footer, header, nav, hgroup {
  display:block;
}
```

html结构总插入以下代码：

```html
<!--[if lt IE 9]>
  <script>
    document.createElement("header" );
    document.createElement("footer" );
    document.createElement("section"); 
    document.createElement("aside"  );
    document.createElement("nav"    );
    document.createElement("article"); 
    document.createElement("hgroup" ); 
    document.createElement("time"   );
  </script>
  <noscript>
     Warning !Because your browser does not 
     support HTML5, some elements are simulated 
     using JScript. Unfortunately your browser 
     has disabled scripting. Please enable it 
     in order to display this page.
  </noscript>
<![endif]-->
```

## 六、总结

HTML5中新的节段和标题标签带来了以标准的方法来描述web文档的结构和大纲。其为人们使用HTML5浏览器和需要结构来帮助他们理解页面带来了一个很大的优势。例如，人们需要一些辅助技术的帮助。这些新的语义元素时间用简单，几乎没有负担，也可以在不支持HTML5的浏览器中工作。因此，他们应该被广泛使用。