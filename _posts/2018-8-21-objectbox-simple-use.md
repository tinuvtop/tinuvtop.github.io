---
layout: post
title: 'ObjectBox的简单使用'
subtitle: ''
date: 2018-08-21
categories: Android后端框架
cover: ''
tags: Android 数据库
---


# 介绍
ObjectBox也是greenrobot团队开发的orm框架,一开始我看这名字以为他能够储存一些复杂的对象,或者自动依照关系储存复杂的对象,但很遗憾,它并不能,要储存复杂的对象还是需要手动的定义一对一关系或者一对多关系,数据库只能储存一些基本数据类型.这些关系需要学习数据库的设计,我打算从现在(2018年8月21日)开始学习,反正这个学期也要学数据库了.作为学习的一部分,这篇博客会记录下ObjectBox的基本使用.

# 引入
ObjectBox只要引入一个插件就可以了

在project的build.gradle中引入插件
```Java
buildscript {
    ext.objectboxVersion = '2.1.0'
    dependencies {
        classpath "io.objectbox:objectbox-gradle-plugin:$objectboxVersion"
    }
}
```
在app模块的bulid.gradle应用插件
```Java
apply plugin: 'io.objectbox' // after applying Android plugin
```

# 简单使用

## 建立实体类
```Java
@Entity
public class Rating {

    @Id
    private long id;
    private String max;
    private String average;
    private String stars;
    private String min;
    
    // 省略getter/setter
}
```
它的id好像要求一定要long类型的,官方是这样说的,整型的id我没有测试,我只使用了long类型
>Note that ObjectBox uses IDs of type long (see Object IDs doc)

## 构建项目使插件生效
在Android Studio的菜单找到`Build->Make Project`构建一下就可以了

## 初始化
官方推荐在Application或者Application的子类初始化,因为这样只需要初始化一次

比如
```Java
public BoxStore getBoxStore() {
        return MyObjectBox.builder().androidContext(getApplicationContext()).build();
    }
```

## 进行操作
```Java
BoxStore boxStore = ((MyApplication) getApplication()).getBoxStore();
        Box<Rating> ratingBox = boxStore.boxFor(Rating.class);
        Rating rating = new Rating();
        rating.setStars("100");
        rating.setAverage("7");
        rating.setMax("10");
        rating.setMin("5");
        ratingBox.put(rating);
```
这个Box<Rating>就相当于ORM框架的DAO层,存放和各种操作数据库的方法.
