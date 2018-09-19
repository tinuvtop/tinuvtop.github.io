---
layout: post
title: 'OMG Partical Practice'
subtitle: ''
date: 2018-09-12
categories: D3
cover: ''
tags: D3 数据可视化
---
# 介绍
通过D3可以用很简单的代码实现很炫酷的效果,这个动画主要通过鼠标的移动为驱动的动画,它分为两个部分,一个是获得鼠标或触摸的位置坐标,一个是圆的生成和消散动画.

[示例](https://huiyanpohun.github.io/DataVisualization/circle.html):https://huiyanpohun.github.io/DataVisualization/circle.html


# 填充一个SVG
```JavaScript
// 在body中填充一个svg标签
var svg = d3.select("body")
    .append("svg")
    .attr("width", width)
    .attr("height", height);
```
这个不用多讲,在body中填充一个SVG标签,设置一下宽高属性


# 在SVG中填充一个矩形
在SVG中填充一个矩形主要是为了方便监听鼠标移动或者触摸移动事件
```javascript
// 在svg中填充一个rect矩形对象
var rect = svg.append("rect")
    .attr("width", width)
    .attr("height", height)
    // 监听是否为手机触摸或者鼠标移动
    // partical是一个回调函数
    .on("ontouchstart" in document ? "touchmove" : "mousemove", partical);
    //.on("click",partical);
```

# 回调函数
回调函数是这个例子中最重要的一个函数,鼠标每移动一次,或者屏幕每触摸移动一次就会调用一次这个函数
```Javascript
// 回调函数,鼠标每移动一次,调用函数一次
function partical() {
    /**
     * 通过d3.mouse(this)可以得到鼠标的位置坐标
     * 得到的参数是数组类型的
     * x = param[0]
     * y = param[1]
     */
    /**
     * tips:
     * hsl颜色模式通过色度:h,饱和度:s和亮度:l来定义一种颜色
     * h的取值范围为[0,360],s的取值范围为[0,1],l的取值范围为[0,1]
     */
    console.log("i==>"+i);
    var param = d3.mouse(this);
    svg.insert("circle", "rect")
        .attr("cx", param[0])
        .attr("cy", param[1])
        .attr("r", 1e-6)
        // 描边
        .style("stroke", d3.hsl((i = (i + 1) % 360), 1, 0.5))
        // 描边的透明度
        .style("stroke-opacity", 1)
        // 平滑过渡
        .transition()
        // 持续时间
        .duration(2000)
        // 缓动函数,设定如何属性如何变化,在这个例子中
        .ease(/**function (x) {
         return x*x;
        }**/Math.sqrt())
        .attr("r", 100)
        .style("stroke-opacity", 1e-6)
        .remove();

    d3.event.preventDefault();
}
```
接下来说一下比较重要的操作符
## transition()
过渡操作符,~~过渡操作符是操作元素过渡动画的操作符(废话)~~,这个操作的元素是是什么呢,当然就是svg中插入矩形的圆了,刚才说回调函数在鼠标每一次移动的时候都会调用一次,也就是鼠标每移动一次都会插入一个圆,(当然这个圆最后会通过remove()操作符移除掉),当使用呢这个操作符以后的就是设置如何过渡的呢

## duration()
动画是时间的函数,当然需要一个持续时间,这个持续时间传入的参数是以毫秒为单位

## ease()
这个操作符其实有点难说,可以把它看作为速度,持续时间可以看作是路程,x是一个变量它的范围是[0,1],路程是一定的,如果是线性的,就是匀速直线运动,当然也可以是加速运动,这个取决于你传入的函数.比如我要将圆从0变化到100,可以随时间匀速的变化到一百,也可以在某段时间变化得很快,一段时间变化得很慢.

## transition.attr()
这个是要变化的属性,在这个例子中r的大小会变化,半径从1e-6变化到100

## transition.style()
要变化的样式,在这个例子中描边的透明度(stroke-opacity)发生了变化从1变化到了1e-6
