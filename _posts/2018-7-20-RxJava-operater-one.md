---
layout: post
title: 'RxJava操作符(一)'
subtitle: ''
date: 2018-07-20
categories: RxJava
cover: ''
tags: RxJava Java Android Thread
---

#### RxJava操作符
如果知道函数式编程那么理解RxJava的操作符就很容易了

>函数式编程是种编程方式，它将电脑运算视为函数的计算。函数编程语言最重要的基础是λ演算（lambda calculus），而且λ演算的函数可以接受函数当作输入（参数）和输出（返回值）。

我刚好懂一点.

##### map操作符(map())函数.
在Python语言中map()函数接受一个函数和一个`Iterator`(可迭代对象如数组),把每一个可迭代对象中的元素都作用在函数上,作用后的元素又形成一个可迭代对象,返回一个惰性的可迭代对象.在前面说过,`RxJava`会维持一个队列,这个队列相当于一个可迭代对象,这个队列的每一个元素都会通过一个函数,(实际是实现接口的类),然后处理后的数据会送到观察者.

如
```java
import io.reactivex.Observable;
import io.reactivex.ObservableEmitter;
import io.reactivex.ObservableOnSubscribe;
import io.reactivex.Observer;
import io.reactivex.disposables.Disposable;
import io.reactivex.functions.Function;

public class Main {
    public static void main(String[] arg) {
        Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) {
                observableEmitter.onNext("aest1");
                observableEmitter.onNext("best2");
                observableEmitter.onNext("cest3");
                observableEmitter.onComplete();
            }
        }).map(new Function<String, Integer>() {
            @Override
            public Integer apply(String s) throws Exception {

                return Integer.valueOf(s.charAt(0));
            }
        }).subscribe(new Observer<Integer>() {

                         @Override
                         public void onSubscribe(Disposable disposable) {

                         }

                         @Override
                         public void onNext(Integer integer) {
                            System.out.println(integer);
                         }

                         @Override
                         public void onError(Throwable throwable) {

                         }

                         @Override
                         public void onComplete() {

                         }
                     }
        );
    }
}
```
在上述的例子中`map()`函数实际上是接受了一个接口,或者说是一个内部类,并实现了`apply()`方法来处理事件,`Function`接口中的两个泛型分别是要处理的类型和处理后的类型.


##### `zip`操作符`zip()`
>zip 专用于合并事件，该合并不是连接（连接操作符后面会说），而是两两配对，也就意味着，最终配对出的 Observable 发射事件数目只和少的那个相同。

zip操作符用于两个被观察者对象(`Observable`)发射事件的合并,不过有一个规则,就是`发射事件数目只和少的那个相同`,总结起来就是`短板原则`

如:
```java
import io.reactivex.Observable;
import io.reactivex.ObservableEmitter;
import io.reactivex.ObservableOnSubscribe;
import io.reactivex.Observer;
import io.reactivex.disposables.Disposable;
import io.reactivex.functions.BiFunction;

public class RxJavaDemo {

    public static void main(String[] args){
        Observable<String> myObservableF = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onNext("test3");
            }
        });

        Observable<Integer> myObservableS = Observable.create(new ObservableOnSubscribe<Integer>() {
            @Override
            public void subscribe(ObservableEmitter<Integer> observableEmitter) throws Exception {
                observableEmitter.onNext(1);
                observableEmitter.onNext(2);
                observableEmitter.onNext(3);
                observableEmitter.onNext(4);
            }
        });

        Observable.zip(myObservableF, myObservableS, new BiFunction<String, Integer, String>() {
            @Override
            public String apply(String s, Integer integer) throws Exception {
                return s+String.valueOf(integer);
            }
        }).subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(Disposable disposable) {

            }

            @Override
            public void onNext(String s) {
                System.out.println("receive"+s);
            }

            @Override
            public void onError(Throwable throwable) {

            }

            @Override
            public void onComplete() {

            }
        });
    }
}
```

##### `concat`操作符`concat()`函数
这个操作符的作用是连接连接两个被观察者发射的事件为一个,就是一个被观察者对象会追加到另一个被观察者对象,如果两个被观察者对象发送的是不同的类型,那么观察者会接受到一个`可序列化`的对象,可以相信被观察者发送的对象都实现了可序列化接口.

```java
import io.reactivex.Observable;
import io.reactivex.ObservableEmitter;
import io.reactivex.ObservableOnSubscribe;
import io.reactivex.Observer;
import io.reactivex.disposables.Disposable;
import io.reactivex.functions.BiFunction;
import io.reactivex.functions.Consumer;

import java.io.Serializable;

public class RxJavaDemo {

    public static void main(String[] args){
        Observable<String> myObservableF = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onNext("test3");
                observableEmitter.onComplete();
            }
        });

       Observable<String> myObservableS = Observable.create(new ObservableOnSubscribe<String>() {
           @Override
           public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
               observableEmitter.onNext("test4");
               observableEmitter.onNext("test5");
               observableEmitter.onNext("test6");
           }
       });

       Observable.concat(myObservableF,myObservableS).subscribe(new Observer<String>() {
           @Override
           public void onSubscribe(Disposable disposable) {

           }

           @Override
           public void onNext(String s) {
                System.out.println(s);
           }

           @Override
           public void onError(Throwable throwable) {

           }

           @Override
           public void onComplete() {

           }
       });
    }
```
上例中首先从第一个被观察者发射,再从第二被观察者发射
###### 值得注意的一点
像以上方式追加(连接)结束一定要加`onCompelte()`.

#### `FlatMap`操作符
这个操作符可以把一个`Observable`根据发射的事件分解成多个`Observable`以供处理,然后又将这多个`Observable`组成一个`Observable`,但是重新组装的`Observable`发射的顺序并不能保证根原来的`Observable`是一样的,这个一看其实根`map`操作符差不了多少,只不过`map`操作符操作的是发射的事件,而这个的操作符操作的是分解的Observable.

如:
```java
import io.reactivex.*;
import io.reactivex.functions.Consumer;
import io.reactivex.functions.Function;

public class RxJavaDemo {

    public static void main(String[] args){
        Observable<String> myObservableF = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                observableEmitter.onNext("test1");
                observableEmitter.onNext("test2");
                observableEmitter.onNext("test3");
                observableEmitter.onComplete();
            }
        });

        myObservableF.flatMap(new Function<String, ObservableSource<String>>() {
            @Override
            public ObservableSource<String> apply(String s) throws Exception {
                Observable<String> flatObservable = Observable.create(new ObservableOnSubscribe<String>() {
                    @Override
                    public void subscribe(ObservableEmitter<String> observableEmitter) throws Exception {
                        observableEmitter.onNext(s+"tinuv");
                    }
                });
                return flatObservable;
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

#### ConcatMap操作符
CancatMap操作符与FlatMap操作符差不多,唯一的区别在于ConcatMap操作符能保证顺序,就是说发射时是什么顺序,组装好后还是什么顺序.

##### 非常值得注意的一点
在`FlatMap`和`ConcatMap`中其接口都会把所有的发射的元素发射过来
```java
 myObservableF.concatMap(new Function<String, ObservableSource<String>>() {
            @Override
            public ObservableSource<String> apply(String s) throws Exception {
                List<String> list = new ArrayList<>();// 可以建列表保存所有发射过来的元素
                list.add(s+"tinuv");
                return Observable.fromIterable(list);
            }
```


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
                observableEmitter.onNext("test3");
                observableEmitter.onComplete();
            }
        });

        myObservableF.concatMap(new Function<String, ObservableSource<String>>() {
            @Override
            public ObservableSource<String> apply(String s) throws Exception {
                List<String> list = new ArrayList<>();
                list.add(s+"tinuv");
                return Observable.fromIterable(list);
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