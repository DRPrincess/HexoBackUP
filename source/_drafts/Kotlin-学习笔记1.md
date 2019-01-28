---
title: Kotlin-学习笔记-基本类型
date: 2018-01-05 18:18:18
tags:
---

# 基本类型PK
Java基本类型有：
|类型名称|位数|
|:--|--|
byte|8 bits
short|6 bits
int|32 bits
long|64 bits
float|32 bits
double| 64 bits
boolean| java虚拟机决定
char| 16 bits

Kotlin的基本类型

|类型名称|位数|
|:--|--|
Byte|8 bits
Short|6 bits
Int|32 bits
Long|64 bits
Float|32 bits
Double| 64 bits
Boolean| 未知
Char| 未知

基本类型的定义和位数看起来没有差别，大的区别：
- 都变成了大写。
- Java中 int a，没给默认值就默认为0的，但是 Kotlin 中不行，不能给予空值，如果想要空值，就定义可空类型
- 不支持隐式转换，为此为每个类型都提供其他类型的转换方法，例如 toByte()
- 位运算，没有特殊字符来表示，而只可用中缀方式调用命名函数
- 装箱不保证同一性，只保证相等性
- 提供无符号整型

# 字符串PK

- 字符串的元素——字符可以使用索引运算符访问: s[i]
- 字符串模版替代String.format()模板表达式以美元符（$）开头，由一个简单的名字构成

# 数组
- Array 比Java 数组拥有更多的API，例如提供 set() get()方法
- 创建一个数组的区别，有两种方法：

```
//第一种方法：
val asc = arrayOf(1, 2, 3)

//第二种方法
val asc = Array(3, { i -> (i + 1).toString() })
```

- 提供无装箱开销的专门的类来表示原生类型数组: ByteArray、 ShortArray、IntArray 等等。这些类与 Array 并没有继承关系，但是它们有同样的方法属性集。


# 最深的感受

kotlin 中，所有的东西都是对象，可以直接像Java中类的操作一样，可以通过```.``` 去调用类方法和属性。在Java中有两大数据类型，基本类型和引用类型，基本类型是不能这样调用的，但是在 Kotlin 中可以。在这个意义上讲我们可以在任何变量上调用成员函数与属性。 一些类型可以有特殊的内部表示——例如，数字、字符以及布尔值可以在运行时表示为原生类型值，但是对于用户来说，它们看起来就像普通的类。
