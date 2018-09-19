---
layout: post
title: 'Java反射机制入门'
subtitle: ''
date: 2018-07-22
categories: Java
cover: ''
tags: Java 反射
---
#### 介绍
Java的反射机制我学习Java以来一直很忽视,一直到我最近学习Spring框架和各种注解框架了解到幕后的反射机制是背后的功臣,才下定决心要好好的学一学.Java反射机制是在运行状态中,对于任意一个类,都能获得这个类的所有属性和方法,对于任何一个对象都能调用它的任意一个属性和方法.要使用Java的反射机制主要使用的是Class类java.lang.reflect包

#### 获得Class对象
要获得类或对象的属性和方法必须要获得一个class对象.获得class对象的方法有三种.


##### 通过Object.getClass()方法获得class对象
object类是所有类的基类,这个类有一个getClass()方法,这个方法是公共方法,因此是获取对象的属性和方法,就是说类必须实例化成对象才能调用.

```java
MovieEntity movieEntity = new MovieEntity();
Class<MovieEntity> movieEntityClass = (Class<MovieEntity>) movieEntity.getClass();
System.out.println(movieEntityClass.getName());
```
Class里面的泛型也可以这样写,表示任意类型,Class里面的泛型要求是子类就可以,比如上面的例子,要求是MovieEntity的子类
```
Class<?> movieEntityClass = movieEntity.getClass();
```

##### 通过object的静态字段获得class对象
这个是获得类的属性和方法,无需将类实例化
```java
Class<?> movieEntityClass = MovieEntity.class;
```

##### 通过Class.forName()获得class对象
这个方法也是静态方法,要注意的可以不用将完整的包名传入.
如
```java
Class<?> movieEntityClass = Class.forName("MovieEntity");
Class<?> muiscClass = Class.forName("test.Music");
```


#### 获得对象或类的属性和方法

##### 获得构造方法并实例化
```java
Class<?> movieEntityClass = Class.forName("MovieEntity");
Constructor<MovieEntity> movieEntityConstructor = (Constructor<MovieEntity>) movieEntityClass.getConstructor();
MovieEntity movieEntity = movieEntityConstructor.newInstance();
```
这里面捕获的异常是很多的,这里获取了一个无参的构造方法,也可以获取有参的构造方法

##### 获得里面的属性(字段)
###### 获得公共属性
```java
Class<?> movieEntityClass = Class.forName("MovieEntity");
String title = movieEntityClass.getField("rate");
//Field field = movieEntityClass.getField("rate");也可以将Filed转化类型
```
###### 获得私有属性
```java
Class<?> movieEntityClass = Class.forName("MovieEntity");
Field field = movieEntityClass.getDeclaredField("title");
field.setAccessible(true); //设置有权限访问
```

##### 获得类或对象的方法并运行
###### 获得私有方法并运行
```java
Class<?> movieEntityClass = Class.forName("MovieEntity");
Constructor<MovieEntity> movieEntityConstructor = (Constructor<MovieEntity>) movieEntityClass.getConstructor();
MovieEntity movieEntity = movieEntityConstructor.newInstance();
Method showMessage = movieEntityClass.getDeclaredMethod("showMessage",null);
showMessage.setAccessible(true);
showMessage.invoke(movieEntity,null);
```
###### 获得公共方法并运行
```java
Class<?> movieEntityClass = Class.forName("MovieEntity");
Constructor<MovieEntity> movieEntityConstructor = (Constructor<MovieEntity>) movieEntityClass.getConstructor();
MovieEntity movieEntity = movieEntityConstructor.newInstance();
Method method = movieEntityClass.getMethod("setId", int.class);
method.invoke(movieEntity,10);
```