---
layout: post
title: 'Sing Threaded Execution'
subtitle: ''
date: 2018-07-20
categories: 设计模式
cover: ''
tags: Thread 设计模式
---
Single Threaded Execution其实跟前面那个银行取款的例子是差不多的,就是一个对象可能被多个线程访问,但不能让多个线程并行访问,因为一旦并行访问就有可能导致错误,具体原因跟前面的分析是一样的,解决的方法同样是加关键词synchronized.

这里呢我也会使用书上的例子.书上的例子模拟呢多人过同一个桥的场景,下面来说明这三个类.

#### Gate门类.
门类有三个字段:`counter`,`name`和`address`,counter表示通过的总人数,name表示最后一个通过的人的名字,因为是不断通过的,因此name可以这么理解:当前计数器counter的最后一个名字,address就是这个人的地址.门类还有一个check方法,这个方法检测name字段的首字母和地址的首地址是否相同(为了方便,我们将一个人的名字和地址都设置成同样的首地址),这个在微观上也就是在内存的层面来看就是检查这两个字段是否是不同的线程传递过来的,如果这是一个单线程程序,那么check程序永远不会报错,因为同一线程按顺序修改后它们一定是一样的,而多线程程序并行运行是有可能争夺时间片的,导致修改热潮一个字段后,紧接着另一个字段被另一条线程修改,又有可能第三条线程有又执行呢check方法,就会很混乱
```java
class Gate{
    private int counter = 0;
    private String name = "nobody";
    private String address = "nowhere";

    public void pass(String name,String address){
        this.counter++;
        this.name = name;
        this.address = address;
        check();
    }

    @Override
    public String toString() {
        return "No."+counter+"name:"+name+"address"+address;
    }

    private void check(){
        if (name.charAt(0)!=address.charAt(0)){
            System.out.println("***broken***"+toString());
        }
    }
}
```

#### UserThread类
这个类是通用用户类,传入不同的人表示不同的人,人不断通过门.
```java
class UserThread extends Thread{
    private final Gate gate;
    private final String name;
    private final String address;

    public UserThread(Gate gate,String name,String address){
        this.gate = gate;
        this.name = name;
        this.address = address;
    }

    @Override
    public void run() {
        System.out.println(name+"BEGIN!");
        while (true){
            gate.pass(name,address);
        }
    }
}
```

#### 运行的舞台
```java
public class ThreadDemo {

    public static void main(String[] args) {
        Gate gate = new Gate();
        UserThread aliceThread = new UserThread(gate,"Alice","Alaska");
        UserThread boddyThread = new UserThread(gate,"Boddy","Braze");
        UserThread chrisThread = new UserThread(gate,"Chris","Canada");
        aliceThread.start();
        boddyThread.start();
        chrisThread.start();
    }

}
```

#### 分析
通过这个代码我觉得可以很容易的理解的这种singer threaded execution.Gate类被称为是非线程安全的,因为它在多线程中没有达到我们的设计要求,另外,由于线程竞争的随机性,甚至调试信息都有可能找不到错误的点,为什么会出错呢?原因是多条线程是并行的,修改一个字段是一个线程,修改另一个字段又是另一个线程,执行check方法又是一个线程,种种的可能性导致运行可能出错


#### 解决办法
解决办法还是按照以前的那种方法,使用synchronized关键词,加入synchronized关键词就能是这个对象的一些方法只能被一个线程访问,自然就不会出错了

```java
class Gate{
    private int counter = 0;
    private String name = "nobody";
    private String address = "nowhere";

    public synchronized void pass(String name,String address){
        this.counter++;
        this.name = name;
        this.address = address;
        check();
    }

    @Override
    public synchronized String toString() {
        return "No."+counter+"name:"+name+"address"+address;
    }

    private void check(){
        if (name.charAt(0)!=address.charAt(0)){
            System.out.println("***broken***"+toString());
        }
    }
}

```


#### Single Threaded Executing的角色
这种模式中有一个角色叫做共享资源`SharedResource`,在上述例子中Gate类就扮演的这个角色,它的资源(在内存中的值)可以被多个线程访问,这个角色又很多方法,可以分为两类,一类是线程安全的,一类是线程不安全的,线程不安全的就需要保护.


#### 小知识
Java中提供了下列方法,这些方法可以确保集合类为线程安全的
* synchronizedCollection
* synchronizedList
* synchronizedMap
* synchronizedSet
* synchronizedSortedMap
* synchronizedSortSet

#### 死锁
##### 定义
两个线程分别持有这锁,并相互等待对方释放锁的现象.发生死锁的线程都无法再继续运行.
##### 比喻和例子
Alice和Boddy一起吃意大利面,要想吃意大利面需要一把勺子和一把叉子,而且两者缺一不可,此时桌面上只有一把勺子和一把叉子,如果Alice持有一把勺子Boddy持有一把叉子,那么Alice会等待Boddy的叉子而Boddy又会等待Alice的叉子,就这样它们坚持下去,谁也没又办法吃意大利面.

在Single Threaded Execution模式中如果出现下面的情况就有可能出现死锁.
1. 存在多个sharedResource角色,也就是叉子和勺子
2. 在持有某个SharedResource的同时还去获取其他SharedResource的锁,也就是拿着一把叉子还想着一把勺子
3. 获取SharedResource角色的锁的顺序并不固定(SharedResource角色是对称的),相当于"拿勺子-->拿叉子"和"拿叉子-->拿勺子"是不分顺序的