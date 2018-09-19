---
layout: post
title: 'JavaScript简单学习'
subtitle: ''
date: 2018-08-29
categories: JavaScript
cover: ''
tags: JavaScript
---
# 介绍
这个学期选了数据可视化,要使用一个基于JavaScript的库,所以必须要简单的学习一下这个语言,我不会把这个语言学得太过于深入,但是简单的掌握是有必要的.

# 简单学习
## 引入其他的库
在html的头部引入相应的库就好了,可以使用本地文件引入,也可以用超链接来引入,如:
```html
<head>
    <meta charset="utf-8">
    <title>my frist page</title>
    <!--引入ECharts-->
    <script src="echarts.js"></script>
</head>
```
这个例子通过文件的方式引入这个JavaScript库

## 变量
JavaScript是动态变量类型,变量类型可以是可以是基本数据类型(这里基本数据类型意思是指语言本身定义的)(字符串（String）、数字(Number)、布尔(Boolean)、数组(Array)),使用关键词`var`就可以定义一个变量,如
```javascript
var a = 10;
var b = true;
var c = "test";
var d = ["tinuv","lyy"];
var e = [1,2,3,4]
```

## 面向对象

### 普通的创建对象
Javascript类的基本特征是**键值对**,键可以看作是变量名,值的类型可以是基本数据类型,可以是数组,也可以是JavaScript类,**但是无论如何嵌套,最底层的永远是基本数据类型**,不同的键值对用逗号隔开.json的语法就是取自JavaScript类的语法.
如
```Javascript
var myObject = {
    name:"tinuv",
    age:20,
    hasgf:false,
    like:["lyy"],
    lan:[{name:"cn"},{name:"en"}],
    proLan:{name:"java"}
}
```
### 动态的创建对象
动态的创建对象相对于普通的创建对象,普通的创建对象在创建对象时就被赋予了值,而动态创建则是在外部创建属性.如
```javascript
      var person = new Object();
      person.x = 10;
      person.y = 20;
      person.name = "tinuv";
      person.love = ["lyy"];
      document.write(person.love[0]);
```
要注意一定是`new Object()`,不仅可以这样还可在已有的对象添加属性.

## 函数
### 定义普通函数
因为JavaScript是动态类型的,所以使用`function`作为关键字就可声明一个方法,如:
```javascript
    function showName(age) {
        console.log(age);
    }
```
要注意在html中使用字符串的时候最好用单引号,因为双引号可能报错,如
```html
<button onclick="showName('tinuv')">show</button>
```
### 定义对象函数
可以这样定义对象内函数
```javascript
    var myObject = {
      name: "tinuv",
      showName: function() {
        alert(this.name);
      }
    }
```
this关键词仍然指代此对象

可以这样访问对象函数
```Javascript
myObject.showName()
```