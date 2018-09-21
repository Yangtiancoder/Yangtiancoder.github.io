---
layout: post
title: 慎用equals方法和==
category: blog
tags: [Java]
description: 用法小结。
---

  
前言：看到这个问题痛心疾首，忽然想起当年面试的时候问过，正好给说反了，十分尴尬，如今再次看到这个问题，顺一下思路，整理了一下。

## Java中的==  

== 比较的是变量(栈)内存中存放的对象的(堆)内存地址，即是否是指相同一个对象。

## equals方法

equals用来比较的是两个对象的内容是否相等。

## 区别

先来看Object中的equals源码
```java
public boolean equals(Object obj) {
    return (this == obj);
    }
```

可以看到，在object中==和equals是等效的，但是不同的原因就在于有些类（像String、Integer等类）对equals进行了重写，但是没有对equals进行重写的类（比如我们自己写的类）就只能从Object类中继承equals方法，其equals方法与==就也是等效的，除非我们在此类中重写equals。

我们继续看一些代码：

```java
public class test {
    public static void main(String[] args) {
        String a = new String("ab"); // a 为一个引用
        String b = new String("ab"); // b为另一个引用,对象的内容一样
        String aa = "ab"; // 放在常量池中
        String bb = "ab"; // 从常量池中查找
        if (aa == bb) // true
            System.out.println("aa==bb");
        if (a == b) // false，非同一对象
            System.out.println("a==b");
        if (a.equals(b)) // true
            System.out.println("aEQb");
        if (42 == 42.0) { // true
            System.out.println("true");
        }
    }
}
```
可能会产生疑问，String bb="ab"是一种非常特殊的形式,和new（）有本质的区别。当声明这样的一个字符串后，JVM会在常量池中先查找有有没有一个值为"ab"的对象,如果有,就会把它赋给当前引用.即原来那个引用和现在这个引用指点向了同一对象,如果没有,则在常量池中新创建一个"ab"。
而String b = new String("ab")和其它任何对象一样.每调用一次就产生一个对象。
