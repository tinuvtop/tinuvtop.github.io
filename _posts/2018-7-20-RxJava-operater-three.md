---
layout: post
title: 'RxJava操作符(三)'
subtitle: ''
date: 2018-07-20
categories: RxJava
cover: ''
tags: RxJava Java Android Thread
---


#### doOnNext操作符

这个操作符就是让观察者在接受事件之前干一点是,`doOnNext()`也接受一个`consumer`的接口

```java
public class RxJavaDemo {

    public static void main(String[] args) {
        Observable<String> myObservableF = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onNext("test3");
                observableEmitter.onComplete();
            }
        });

        myObservableF.doOnNext(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                System.out.println(s);
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

输出的结果是
```
test1
test1
test2
test2
test3
test3
```

#### skip操作符
接受一个`long`类型参数表示跳过的条数
```java
public class RxJavaDemo {

    public static void main(String[] args) {
        Observable<String> myObservableF = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onNext("test3");
                observableEmitter.onComplete();
            }
        });

        myObservableF.skip(1)
                .subscribe(new Consumer<String>() {
                    @Override
                    public void accept(String s) throws Exception {
                        System.out.println(s);
                    }
                });
    }
}
```

#### 小结
操作符是RxJava的一大特色,RxJava的操作远远不止这些,以后有机会还会慢慢总结
操作符这个部分参考自`https://www.jianshu.com/p/0cd258eecf60`