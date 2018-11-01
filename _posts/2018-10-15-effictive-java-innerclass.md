---
layout: post
title: java的嵌套类
category: blog
tags: [java]
description: 四种嵌套类介绍及其应用场景。
---

## 定义

在 Java 语言中允许在另外一个类中定义一个类，这样的类被称为嵌套类。包含嵌套类的类称为外部类（outerclass）。嵌套类是它的外部类的成员，非静态嵌套类（内部类）可以访问外部类的其他成员，即使该成员是私有的。而静态嵌套类只能访问外部类的静态成员。  

嵌套类作为外部类的一个成员，可以被声明为：private,public,protected或者包范围（注意：外部类只能被声明为public或者包范围）。

## 存在的意义

-  嵌套类可以访问外部类的所有数据成员和方法，即使它是私有的。
-  提高可读性和可维护性：因为如果一个类只对另外一个类可用，那么将它们放在一起，这更便于理解和维护。
-  提高封装性：给定两个类A和B，如果需要访问A类中的私有成员，则可以将B类封装在A类中，这样不仅可以使得B类可以访问A类中的私有成员，并且可以在外部隐藏B类本身。

## 嵌套类的类型

前文已经介绍了嵌套类分为静态嵌套类和非静态嵌套类，而非静态嵌套类又称为内部类（内部类是嵌套类的子集）。静态嵌套类可以使用外部类的名称来访问它。

非静态嵌套类(内部类)又可以分为以下三种：

-  成员内部类（Member inner class）
-  匿名内部类（Anonymous inner class）
-  局部内部类（Local inner class）

**static嵌套类**

因为static嵌套类不能直接访问所在类的非static成员变量和方法，所以static嵌套类必须通过绑定所在类的实例来进行访问。而对于所在类的静态成员和方法包括private、protected和public的，可以访问。因为它也有static修饰。

static嵌套类通过写出封装的类名来进行实例化和访问其内部成员：
```java
OuterClass.StaticNestedClass nestedObject = new OuterClass.StaticNestedClass();
```
**成员内部类**

因为内部类是所在类的成员，所以它可以访问所在类的任意变量和方法，但是它本身却不能定义任何static的变量或方法。

同时，内部类的实例化方式也与static嵌套类有所不同：
```java
OuterClass outerObject=new OuterClass();
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```
static嵌套类与non-static嵌套类，在形式上只有是否含有static关键字的区别，但是JVM在初始化时两者还是有差别的：差别就是后者在实例化时会自动地与外围实例建立一种联系，且这种联系不得修改。JVM在实例化non-static嵌套类时会生成一个指向外围实例的对象引用(this)，保存这种引用将会消耗时间和空间，同时，在外围实例满足垃圾回收的条件时仍然得以留存。

注意：当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象，即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问：
```java
外部类.this.成员变量
外部类.this.成员方法
```
虽然成员内部类可以无条件地访问外部类的成员，而外部类想访问成员内部类的成员却不是这么随心所欲了。在外部类中如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问：
```java
class Circle {
    private double radius = 0;
 
    public Circle(double radius) {
        this.radius = radius;
        getDrawInstance().drawSahpe();   //必须先创建成员内部类的对象，再进行访问
    }
     
    private Draw getDrawInstance() {
        return new Draw();
    }
     
    class Draw {     //内部类
        public void drawSahpe() {
            System.out.println(radius);  //外部类的private成员
        }
    }
}
```

**局部内部类**

定义在方法内部的类叫作“局部内部类”。它的作用域仅限于方法作用域内，只能在方法的作用域内定义和实例化，是用处最小的类类型。和局部变量一样，它不能被修饰为private, public, protected和static的。
```java
class LocalInner
{
    int a = 1;

    public void doSomething()
    {
        int b = 2;
        final int c = 3;
        // 定义一个局部内部类
        class Inner3
        {
            public void test()
            {
                System.out.println("Hello World");
                System.out.println(a);

                // 不可以访问非final的局部变量
                // error: Cannot refer to a non-final variable b inside an inner
                // class defined in a different method
                // System.out.println(b);

                // 可以访问final变量
                System.out.println(c);
            }
        }

        // 创建局部内部类的实例并调用方法
        new Inner3().test();
    }
}

public class LocalInnerClassTest
{
    public static void main(String[] args)
    {
        // 创建外部类对象
        LocalInner inner = new LocalInner();
        // 调用外部类的方法
        inner.doSomething();
    }

}
```

**匿名内部类**

顾名思义，匿名内部类就是没有名字的局部类。它不使用关键字class, extends, implements以及构造函数。它通常作为方法的一个参数传入，比如在android开发中对一个Button添加一个OnClickListener监听器。

匿名内部类隐匿的继承了一个父类或者实现了一个接口。比如：
```java
mUiHandler.post(new Runnable{
    @override
    public void run(){
    //
    }

});

AsyncClient.get(url, new JsonHttpResponseHandler() {
            @Override
            public void onSuccess(int statusCode, Header[] headers,
                    JSONObject response) {
                // TODO Auto-generated method stub
                super.onSuccess(statusCode, headers, response);}} );
```

## 应用场景

四种不同的嵌套类，每一种都有自己的用途。如果一个嵌套类需要在单个方法之外可见，或者它太长了，不适合于放在方法内部，就应该使用成员类。如果成员类的每个实例都需要指向其外围实例的引用，就要把成员类做成非静态的；否则，就做成静态的。假设这个嵌套类属于一个方法的内部，如果你只需要在一个地方创建实例，并且已经有了一个预置类型可以说明这个类的特征，就把它做成匿名类；否则，就做成局部类。




