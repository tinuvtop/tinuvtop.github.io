---
layout: post
title: '观察者模式'
subtitle: ''
date: 2018-07-20
categories: 设计模式
cover: ''
tags: 观察者模式 设计模式
---

### 介绍
观察者模式属于行为型模式.当对象存在一对多的关系时,使用观察者模式.比如当一个对象被修改时,自动通知它的依赖对象

### 分析
我觉得很重要的一点是搞清哪一个是观察者,哪一个是被观察者,就是说到底`一`是观察者还是`一`被观察者,从它的另一个名字:`订阅发布模式`可以知道很明显`一`就是`发布者`也就是被观察者,`多`是订阅者,也就是观察者.从这两个名字的措辞来看,这两个名字的主被动关系刚好完全被颠倒.从易理解角度看,`订阅发布模式`的名字可能更好.

所以分析的结果就是:

`一`: 发布者,被观察者
`多`: 订阅者,观察者

### 练习

#### 说明
* 被观察者

被观察者,状态的改变是通过这个对象发布给观察者的,也有可能是作为被观察者和观察者的桥梁,与观察者和被观察者都关联,被观察者把状态的改变传递给这个桥梁,桥梁把这个状态的改变再发布给发布者.

* 观察者

观察者被动的接受被观察者发布信息,所以说`订阅发布模式`更符合这种模式的叫法,观察者通常设计会设计一个成抽象类或者接口,因为观察者往往有很多,这种设计容纳一些顶层设计.

#### 示例
观察者设置为一个抽象类(个人认为不优雅)

* 被观察者基类

通常基类有三个函数`addObserver(Observer observer)`,`removeObserver(Observer observer)`和一个抽象函数`publishMessage()`,`addObserver(Observer observer)`函数用来绑定观察者和被观察者,`removeObserver(Observer observer)`函数用来移除观察者和被观察者的绑定关系(订阅关系),值得注意的是,如果是通过抽象类的方式,那么观察者必须要持有被观察者的引用,绑定关系的添加和移除通常都由观察者(订阅者)进行,这也是我认为这种方式不优雅的原因,因为观察者通常有多个,这种会造成代码维护困难,但好处是把订阅和解除订阅的权利交给了观察者

```java
import java.util.ArrayList;
import java.util.List;

public abstract class Observeable {

    List<Observer> Observers = new ArrayList<>();

    public void addObseriver(Observer observer){
        Observers.add(observer);
    }

    public void removeObserver(Observer observer){
        Observers.remove(observer);
    }

    public abstract void publishMessage();

}
```

* 被观察者

被观察者必须实现`publishMessage()`,这个方法是一个通知订阅的对象的函数,因为要传递的事件或者说参数具体都不同.

```java
public class Observe extends Observeable {

    private String state;

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
        publishMessage();
    }

    @Override
    public void publishMessage() {
        for (Observer observer:Observers){
            observer.receiveMessage();
        }
    }
}
```

* Observer基类

`Observer`拥有一个类似`receive()`的抽象函数,子类实现这个函数用来处理被观察者传递过来的事件或者数据,如果是通过抽象类的方式来实现观察者模式,那个每个`Observer`必须持有一个`Observe`对象

```java
public abstract class Observer {

    public abstract void receiveMessage();

    protected Observe observe;
}

```

* Observer子类

`Observer`子类实现`Observer`基类,拥有订阅和解除订阅的权利,通过实现抽象函数`receiveMessage()`来处理被观察者传递过来的事件或者数据

```java

public class FristObserver extends Observer {

    public FristObserver(Observe observe){
        this.observe = observe;
        observe.addObseriver(this);
    }


    @Override
    public void receiveMessage() {
        System.out.println("frist observer receive message"+observe.getState());
    }
}
```

##### 观察者通过接口的方式实现观察者模式

这是观察者模式非常通用的形式,观察者必须实现这个接口,这种方式下,是一种中央集权的方式:只有被观察者有权力添加和移除观察者对象(其实都是通过被观察对象添加或解除订阅关系,只是位置不同而已)

* `Observe`基类`Observeable`

```java
import java.util.ArrayList;
import java.util.List;

public abstract class Observeable {

    List<Observer> Observers = new ArrayList<>();

    public void addObseriver(Observer observer){
        Observers.add(observer);
    }

    public void removeObserver(Observer observer){
        Observers.remove(observer);
    }

    public abstract void publishMessage();

}
```

* `Observe`类

```java
public class Observe extends Observeable {

    private String state;

    public String getState() {
        return state;
    }

    public void setState(String state) {
        this.state = state;
        publishMessage();
    }

    @Override
    public void publishMessage() {
        for (Observer observer:Observers){
            observer.receiveMessage(state);
        }
    }
}

```

* `Observer`接口

```java
public interface Observer{

    void receiveMessage(String message);
}
```

* `Observer`实现类

```java
public class FristObserver implements Observer {


    @Override
    public void receiveMessage(String message) {
        System.out.println("frist observer receive message"+message);
    }

}
```

##### 通过泛型的方式实现观察者模式(以后再说)
