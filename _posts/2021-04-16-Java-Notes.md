---
layout:     post   				    # 使用的布局（不需要改）
title:      Java Notes		# 标题 
subtitle:   Java笔记（再学一遍！！） #副标题
date:       2021-04-16			# 时间
author:     Leo 						# 作者
header-img: img/post-bg-java.png	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Programming Language
    - Java
---

## 1 数据类型

### 1.1 基本数据类型

基本数据类型：有着固定数据，范围的数据类型

整数型：byte short int long<br>
浮点型: float double<br>
字符：  char 0-65535 unicode<br>
布尔：  boolean 0/1<br>

传值,  `==`  比较值

### 1.2 引用数据类型

引用数据类型：没有固定长度尺寸、范围，初始默认为null

数组： `int arr[] = null`   `arr = new int[10]`<br>
字符串： `String str = null`<br>
类（接口），对象: `Object obj = null`<br>

传引用，`==`  比较地址

>Q:String的长度最大为多少？<br>
A:String的底层是一个char数组，所以String的最大长度为数组的最大长度，可参照数组的length属性，一个32位的有符号整数，最大值为2<sup>31</sup>-1，即2G.

## 2 面向对象

### 2.1 类——封装性

Java 语言的基本封装单位是类。由于类的用途是封装复杂性，所以类的内部有隐藏实现复杂性的机制。Java 提供了私有和公有的访问模式，类的公有接口代表外部的用户应该知道或可以知道的每件东西，私有的方法数据只能通过该类的成员代码来访问，这就可以确保不会发生不希望的事情。

类中包含：属性，方法，代码块（创建对象时调用）

#### 2.1.1 访问修饰符

- public<br>
  整个项目可见
- private<br>
  仅当前类可见
- default(缺省)<br>
  包内可见
- protected<br>
  包内以及不同包的子类可见

#### 2.1.2 final,static

- final<br>
    最终形态，不可变

final 修饰的 属性、类，不可被继承

方法不能被重写

- static<br>
静态，唯一

static修饰的属性，方法不属于对象，属于类，可由类名直接调用

static修饰的代码块仅在创建的对象的会被执行一次

