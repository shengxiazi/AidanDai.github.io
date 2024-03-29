---
layout: post
title:  "【笔记】你真的知道回车与换行吗？"
date: 2016-04-03
categories: others
tags: others
keyword: "回车和换行的区别, 回车, 换行"
author: Aidan Dai
---

今天，我总算搞清楚“回车”（carriage return）和“换行”（line feed）这两个概念的来历和区别了。

|符号  |  ASCII码    |  意义| 全称|
|---|---|---|---|
|\n  |      10   |     换行NL|Line Feed|
|\r   |     13     |   回车CR|Carriage Return|

- 回车 \r 本义是光标重新回到本行开头，r的英文return，控制字符可以写成CR，即Carriage Return

- 换行 \n 本义是光标往下一行（不一定到下一行行首），n的英文newline，控制字符可以写成LF，即Line Feed

在不同的操作系统这几个字符表现不同，比如在WIN系统下，这两个字符就是表现的本义，在UNIX类系统，换行\n就表现为光标下一行并回到行首，在MAC上，\r就表现为回到本行开头并往下一行，至于ENTER键的定义是与操作系统有关的。通常用的Enter是两个加起来。

在计算机还没有出现之前，有一种叫做电传打字机（Teletype Model 33）的玩意，每秒钟可以打10个字符。但是它有一个问题，就是打完一行换行的时候，要用去0.2秒，正好可以打两个字符。要是在这0.2秒里面，又有新的字符传过来，那么这个字符将丢失。

于是，研制人员想了个办法解决这个问题，就是在每行后面加两个表示结束的字符。一个叫做“回车”，告诉打字机把打印头定位在左边界；另一个叫做“换行”，告诉打字机把纸向下移一行。

这就是“换行”和“回车”的来历，从它们的英语名字上也可以看出一二。

后来，计算机发明了，这两个概念也就被般到了计算机上。那时，存储器很贵，一些科学家认为在每行结尾加两个字符太浪费了，加一个就可以。于是，就出现了分歧。

```
\n:     UNIX 系统行末结束符
\n\r:   window 系统行末结束符
\r:     MAC OS 系统行末结束符
```

一个直接后果是，Unix/Mac系统下的文件在Windows里打开的话，所有文字会变成一行；而Windows里的文件在Unix/Mac下打开的话，在每行的结尾可能会多出一个^M符号。（这也是经常说见到的现象，哈哈，原来是这样的）

c++语言编程时（windows系统）\r 就是return 回到 本行 行首 这就会把这一行以前的输出覆盖掉; 如：

```c
int main() {
    cout << "hahaha" << "\r" << "xixi" ;
}
```

最后只显示 xixi 而 hahaha 被覆盖了

\n 是回车＋换行 把光标 先移到 行首 然后换到下一行 也就是 下一行的行首拉

```c
int main() {
    cout << "hahaha" << "\n" << "xixi" ;
}
```

则 显示

```
hahaha
xixi
```
