---
layout: post
title: Android之内容提供器
category: blog
tags: [Android, Java]
description: 这节内容比较简单，摘录自《第一行代码》。
---


## 简介

内容提供器主要用于在不同的应用程序直接实现数据共享。

内容提供器一般有两种，一种是使用现有的内容提供器另一种是创建自己的内容提供器给我们程序的数据提供外部访问接口。

## 访问其他程序的数据

 要访问内容提供器中共享的数据，就一定要借助ContentResolve 类，可以通过 Context 中的 getContentResolver()方法获取到该类的实例。ContentResolver提供insert()、update()、delete()、query()操作数据。不同于 SQLiteDatabase，ContentResolver 中的方法使用一个 Uri 参数代替表名参数，成为内容URL。内容 URI由两部分组成，权限（authority）和路径（path） 。权限用于对应用程序做区分，路径用于对表做区分。内容URL标准格式写法如下：
 ```java
content://com.example.app.provider/table1
 ```
在得到了内容 URI 字符串之后，我们还需要调用 Uri.parse()方法将它解析成 Uri 对象才可以作为参数传入。示例如下：
```java
Uri uri = Uri.parse("content://com.example.app.provider/table1")
```

查询代码：

```java
Cursor cursor = getContentResolver().query(
uri,
projection,
selection,
selectionArgs,
sortOrder);
```
![](https://github.com/Yangtiancoder/Yangtiancoder.github.io/blob/master/assets/images/ContentProvider.png?raw=true)

之后对返回的Cursor对象进行解析

```java
if (cursor != null) {
while (cursor.moveToNext()) {
String column1 = cursor.getString(cursor.getColumnIndex("column1"));
int column2 = cursor.getInt(cursor.getColumnIndex("column2"));
}
cursor.close();
}
```

增删改方法形式参考：

```java

insert():

ContentValues values = new ContentValues();
values.put("column1", "text");
values.put("column2", 1);
getContentResolver().insert(uri, values);

update():

ContentValues values = new ContentValues();
values.put("column1", "");
getContentResolver().update(uri, values, "column1 = ? and column2 = ?", new
String[] {"text", "1"});

delete():

getContentResolver().delete(uri, "column2 = ?", new String[] { "1" });
```

## 创建自己的内容提供器

我们可以通过新建一个类去继承 ContentProvider 的方式来创建一个自己的内容提供器。ContentProvider 类中有六个抽象方法，我们需要重写这六个方法。新建 MyProvider 继承自 ContentProvider，示例代码如下：
```java
public class MyProvider extends ContentProvider {
@Override
public boolean onCreate() {
return false;
}
@Override
public Cursor query(Uri uri, String[] projection, String selection,
String[] selectionArgs, String sortOrder) {
return null;
}
@Override
public Uri insert(Uri uri, ContentValues values) {
return null;
}
@Override
public int update(Uri uri, ContentValues values, String selection,
String[] selectionArgs) {
return 0;
}
@Override
public int delete(Uri uri, String selection, String[] selectionArgs) {
return 0;
}
@Override
public String getType(Uri uri) {
return null;
}
}
```

其中CRUD操作跟前面一样，onCreate()在初始化内容提供器的时候调用。通常会在这里完成对数据库的创建和升级等操作，返回 true 表示内容提供器初始化成功，返回 false 则表示失败。getType()是根据传入的内容 URI 来返回相应的 MIME 类型。
内容 URI 的格式主要有以下两种，以路径结尾就表示期望访问该表中所有的数据，以 id 结尾就表示期望访问该表中拥有相应 id 的数据
```java
content://com.example.app.provider/table1
content://com.example.app.provider/table1/1
```
使用通配符的方式来分别匹配这两种格式的内容 URI，规则如下
1. *：表示匹配任意长度的任意字符
2. #：表示匹配任意长度的数字
```java
\\一个能够匹配任意表的内容 URI 格式就可以写成：
content://com.example.app.provider/*
\\一个能够匹配 table1 表中任意一行数据的内容 URI 格式就可以写成：
content://com.example.app.provider/table1/#
```
接着借助 UriMatcher这个类就可以实现匹配内容 URI的功能。 UriMatcher中提供了一个 addURI()方法，这个方法接收三个参数，分别是权限、路径和一个自定义代码。这样，当调用 UriMatcher 的 match()方法时，就可以将一个 Uri 对象传入，返回值是某个能够匹配这个 Uri 对象所对应的自定义代码，利用这个代码，我们就可以判断出调用方期望访问的是哪张表中的数据了。

**query()**
示例代码如下，其他CRUD可参考。
```java
public class MyProvider extends ContentProvider {
public static final int TABLE1_DIR = 0;
public static final int TABLE1_ITEM = 1;
public static final int TABLE2_DIR = 2;
public static final int TABLE2_ITEM = 3;
private static UriMatcher uriMatcher;
static {
uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
uriMatcher.addURI("com.example.app.provider", "table1", TABLE1_DIR);
uriMatcher.addURI("com.example.app.provider ", "table1/#", TABLE1_ITEM);
uriMatcher.addURI("com.example.app.provider ", "table2", TABLE2_ITEM);
uriMatcher.addURI("com.example.app.provider ", "table2/#", TABLE2_ITEM);
}
……
@Override
public Cursor query(Uri uri, String[] projection, String selection,
String[] selectionArgs, String sortOrder) {
switch (uriMatcher.match(uri)) {
case TABLE1_DIR:
// 查询table1表中的所有数据
break;
case TABLE1_ITEM:
// 查询table1表中的单条数据
break;
case TABLE2_DIR:
// 查询table2表中的所有数据
break;
case TABLE2_ITEM:
// 查询table2表中的单条数据
break;
default:
break;
}
……
}
……
}
```

**getType()**

它是所有的内容提供器都必须提供的一个方法，用于获取 Uri 对象所对应的 MIME 类型。一个内容 URI 所对应的 MIME字符串主要由三部分组分，格式如下：
1. 必须以 vnd 开头。
2. 如果内容 URI 以路径结尾，则后接 android.cursor.dir/，如果内容 URI 以 id 结尾，
则后接 android.cursor.item/。
3. 最后接上 vnd.<authority>.<path>。
所以，对于 content://com.example.app.provider/table1 这个内容 URI，它所对应的 MIME
类型就可以写成：
vnd.android.cursor.dir/vnd.com.example.app.provider.table1
对于 content://com.example.app.provider/table1/1 这个内容 URI，它所对应的 MIME 类型
就可以写成：
vnd.android.cursor.item/vnd. com.example.app.provider.table1
getType()示例代码如下：
```java
public class MyProvider extends ContentProvider {
……
@Override
public String getType(Uri uri) {
switch (uriMatcher.match(uri)) {
case TABLE1_DIR:
return "vnd.android.cursor.dir/vnd.com.example.app.provider.
table1";
case TABLE1_ITEM:
return "vnd.android.cursor.item/vnd.com.example.app.provider.
table1";
case TABLE2_DIR:
return "vnd.android.cursor.dir/vnd.com.example.app.provider.
table2";
case TABLE2_ITEM:
return "vnd.android.cursor.item/vnd.com.example.app.provider.
table2";
default:
break;
}
return null;
}
}
```

**参考资料**

《Android第一行代码》


