---
layout: post
title: 'RxJava操作符(二)'
subtitle: ''
date: 2018-07-20
categories: RxJava
cover: ''
tags: RxJava Java Android Thread
---


#### distinct操作符
从词义可以知道,他是筛选出不同的发射的元素给观察者,很简单

如:
```java
import io.reactivex.*;
import io.reactivex.functions.Consumer;
import io.reactivex.functions.Function;

import java.util.ArrayList;
import java.util.List;

public class RxJavaDemo {

    public static void main(String[] args){
        Observable<String> myObservableF = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onComplete();
            }
        });

       myObservableF.distinct().subscribe(new Consumer<String>() {
           @Override
           public void accept(String s) throws Exception {
               System.out.println(s);
           }
       });

    }
}
```

#### `filiter`操作符
过滤器,也同样很简单
```java
import io.reactivex.*;
import io.reactivex.functions.Consumer;
import io.reactivex.functions.Function;
import io.reactivex.functions.Predicate;

import java.util.ArrayList;
import java.util.List;

public class RxJavaDemo {

    public static void main(String[] args){
        Observable<String> myObservableF = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("aest2");
                observableEmitter.onNext("best1");
                observableEmitter.onNext("test2");
                observableEmitter.onComplete();
            }
        });

       myObservableF.filter(new Predicate<String>() {
           @Override
           public boolean test(String s) throws Exception {
               return s.charAt(0)=='t';
           }
       }).subscribe(new Consumer<String>() {
           @Override
           public void accept(String s) throws Exception {
               System.out.println(s);
           }
       });

    }
}
```

#### buffer操作符
从名字可以知道是一个缓冲操作符,`buffer(count,skip)`,这个操作符返回的是一个数组,`count`参数表示一次缓冲的列表中最大的量,这就表示,还有可能是一次缓冲的量小于`count`的,`skip`是步长.

##### 如何运作
从发射的第0个元素开始,一次缓冲最大`count`个元素,一个元素列表,之后前进`skip`步,语文不好,描述得不明白,下面的代码可以说清楚

```java
import io.reactivex.*;
import io.reactivex.functions.Consumer;
import io.reactivex.functions.Function;
import io.reactivex.functions.Predicate;

import java.util.ArrayList;
import java.util.List;

public class RxJavaDemo {

    public static void main(String[] args){
        Observable<String> myObservableF = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onNext("test3");
                observableEmitter.onNext("test4");
                observableEmitter.onNext("test5");
                observableEmitter.onNext("test6");
                observableEmitter.onComplete();
            }
        });

    myObservableF.buffer(3,2).subscribe(new Consumer<List<String>>() {
        @Override
        public void accept(List<String> strings) throws Exception {
            for (String string:strings){
                System.out.print(string+" ");
            }
            System.out.println();
        }
    });

    }
}
```

输出结果:
```
test1 test2 test3 
test3 test4 test5 
test5 test6 
```

#### `timer`操作符
`timer`定时任务,定时任务我没怎么使用过,但一直觊觎这一种能力,定时任务能够实现时序上的一些逻辑,但我一直觉得它很麻烦,很开心能够遇到这么简单的处理定时任务的方法

但是很遗憾,它似乎依赖Android sdk,要在Android平台才能用,还是已经改了API我也不知道,以后再求证吧!