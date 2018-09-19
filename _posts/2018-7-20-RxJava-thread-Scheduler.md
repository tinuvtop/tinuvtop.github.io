---
layout: post
title: 'RxJava线程调度'
subtitle: ''
date: 2018-07-20
categories: RxJava
cover: ''
tags: RxJava Java Android Thread
---

#### 介绍
RxJava中除了操作符外,线程调度也是其一大特点,也可以说是RxJava中最重要的一部份了.

#### Rxjava线程调度的使用很简单
`subscribeOn()`指定订阅的线程,也就是事件处理的线程

`observeOn()`指定观察的线程,就是事件处理结果返回的线程

<table>
<thead>
<tr>
  <th>调度器类型</th>
  <th>效果</th>
</tr>
</thead>
<tbody><tr>
  <td>Schedulers.computation( )</td>
  <td>用于计算任务，如事件循环或和回调处理，不要用于IO操作(IO操作请使用Schedulers.io())；默认线程数等于处理器的数量</td>
</tr>
<tr>
  <td>Schedulers.from(executor)</td>
  <td>使用指定的Executor作为调度器</td>
</tr>
<tr>
  <td>Schedulers.immediate( )</td>
  <td>在当前线程立即开始执行任务</td>
</tr>
<tr>
  <td>Schedulers.io( )</td>
  <td>用于IO密集型任务，如异步阻塞IO操作，这个调度器的线程池会根据需要增长；对于普通的计算任务，请使用Schedulers.computation()；Schedulers.io( )默认是一个CachedThreadScheduler，很像一个有线程缓存的新线程调度器</td>
</tr>
<tr>
  <td>Schedulers.newThread( )</td>
  <td>为每个任务创建一个新线程</td>
</tr>
<tr>
  <td>Schedulers.trampoline( )</td>
  <td>当其它排队的任务完成后，在当前线程排队开始执行</td>
</tr>
</tbody></table>
