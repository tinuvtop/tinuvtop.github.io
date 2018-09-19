---
layout: post
title: '初识RxJava'
subtitle: ''
date: 2018-07-20
categories: RxJava
cover: ''
tags: RxJava Java Android
---
### 介绍
RxJava是JVM的响应式扩展,其实我现在也不知道它是什么意思,因为我现在也没有用过响应式编程,它提到了JVM,我也不知道它具体根JVM有多大的联系,它还提到了观察者模式的设计模式,扩展的观察者模式(`It extends the observer pattern`),这个我有一点了解.作为认识的一部分,我先摆在这里.

官方的介绍是这样的:

>RxJava is a Java VM implementation of Reactive Extensions: a library for composing asynchronous and event-based programs by using observable sequences.

翻译成中文:
>一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库


### 观察者模式
RxJava使用了扩展的观察者模式,与普通的观察者模式不同,最大的不同在于普通的观察者模式一次发布一个内容,而RxJava会维护一个队列,每出队一次分发一个事件,观察者接受和处理一个事件,当然也有一定的限制,限制如下:

* 当发射器调用onComplete()函数时(可以看作是特定信号),被观察者继续发射事件但观察者不会接收.
* 被观察者传递一个Disposable(通过接口),当调用这个对象的`Disposable.dispose()`函数时,发射器停止发射事件


### 用RxJava实现通用的观察者模式
#### 值得注意的地方:
* RxJava还依赖了`Reactive Streams`,如果找不到这个包会报`ClassNotFoundExpection`
* 通过继承`Observable<T>`的方式扩展被观察者的时候(此示例使用这种方式),注册,添加或者说注册订阅者和发布消息使用的是同一个函数,这是结构所决定的
* 使用第二点所说的方式实现通用的观察者模式似乎不能取消订阅

##### 被观察者
```java
import io.reactivex.Observable;
import io.reactivex.Observer;

public class MyObservable extends Observable<String> {


    @Override
    protected void subscribeActual(Observer<? super String> observer) {
        observer.onNext("test1");
        observer.onNext("test2");
        observer.onNext("test3");
        observer.onComplete();
    }
}
```

##### 观察者
```java
import io.reactivex.Observer;
import io.reactivex.disposables.Disposable;

public class MyObserver implements Observer<String> {
    @Override
    public void onSubscribe(Disposable disposable) {

    }

    @Override
    public void onNext(String s) {
        System.out.println("MyObsrever receiver"+s);
    }

    @Override
    public void onError(Throwable throwable) {

    }

    @Override
    public void onComplete() {
        System.out.println("MyObsrever receiver complete");
    }
}
```


##### 主函数
```java
public class Main {
    public static void main(String[] arg){
        MyObservable observable = new MyObservable();
        MyObserver observer = new MyObserver();
        MyObserver2 observer2 = new MyObserver2();
        observable.subscribeActual(observer);
        observable.subscribeActual(observer2);
    }
}

```
可以看到跟我自己实现的观察者模式来比是差不多的

#### 另一种方式
##### 被观察者
```java
import io.reactivex.Observable;
import io.reactivex.ObservableEmitter;
import io.reactivex.ObservableOnSubscribe;
import io.reactivex.Observer;

public class MyObservable implements ObservableOnSubscribe<String> {
    
    @Override
    public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
        observableEmitter.onNext("test1");
        observableEmitter.onNext("test2");
        observableEmitter.onNext("test3");
        observableEmitter.onComplete();
    }
}
```

##### 观察者
```java
import io.reactivex.Observer;
import io.reactivex.disposables.Disposable;

public class MyObserver implements Observer<String> {
    @Override
    public void onSubscribe(Disposable disposable) {

    }

    @Override
    public void onNext(String s) {
        System.out.println("MyObsrever receiver"+s);
    }

    @Override
    public void onError(Throwable throwable) {

    }

    @Override
    public void onComplete() {
        System.out.println("MyObsrever receiver complete");
    }
}
```

主函数
```java
import io.reactivex.Observable;

public class Main {
    public static void main(String[] arg){
        MyObserver observer = new MyObserver();
        MyObserver2 observer2 = new MyObserver2();
        Observable<String> observable = Observable.create(new MyObservable());
        observable.subscribe(observer);
        observable.subscribe(observer2);
    }
}
```

#### 链式调用
```java
import io.reactivex.Observable;
import io.reactivex.ObservableEmitter;
import io.reactivex.ObservableOnSubscribe;
import io.reactivex.Observer;
import io.reactivex.disposables.Disposable;

public class Main {
    public static void main(String[] arg){
        Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onNext("test3");
                observableEmitter.onComplete();
            }
        }).subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(Disposable disposable) {

            }

            @Override
            public void onNext(String s) {
                System.out.println("receive message  "+s);
            }

            @Override
            public void onError(Throwable throwable) {

            }

            @Override
            public void onComplete() {
                System.out.println("receive complete!");
            }
        });
    }
}
```

### 小结
Rxjava当然不是只是实现观察者模式这么简单,但观察者模式的的确确是它很重要的一个特征.