---
layout: post
title: '单向链表'
subtitle: ''
date: 2018-09-15
categories: 数据结构
cover: ''
tags: 数据结构 算法 链表
---
# 介绍
单链表是为后面的双向链表和循环链表做基础的,虽然以前的时候学过,但是已经忘得差不多了,在者,Java是面向对象的语言,内存的结构与c语言有着很大的不同,所以会重新学习一遍.

# 插入
## 从头插入
从表头插入要完成四个步骤
* 创建一个空节点
* 给数据域赋值
* 因为是从表头插入,所以新创建的节点的next域将指向表头,同时自己也成为了表头
* 更新表头

当在一个空链表插入节点时,尾节点和头节点是同一个节点,当再次插入的时候,头节点会更新,那么尾节点也会跟着头节点更新吗?答案是不会,因为head指向的只是内存里的一个引用,赋值的时候,head只是将引用赋给了tail,所以tail指向的也是这个引用,当更新的时候,head指向了不同的引用,当tail还是会指向原来的引用.
```Java
        if (tail == null) {
            tail = head;
        }
```

## 从尾插入
从尾插入需要五个步骤
* 创建一个空的节点
* 给数据域赋值
* 因为新建立的节点会成为尾节点,所以需要把新建立的next域设为null
* 将尾节点的next域设为新建立的节点并更新
* 将尾节点更新

要注意的特殊情况是这个链表为空的时候不能直接将tail.next赋值为node,会出现空指针

## 删除
## 从头删除
从头删除是删除头节点并返回被删除的节点,它有这么几个动作
* 将原来头节点暂存起来,并将其next域置为空,方便垃圾回收器回收这个节点
* 更新头节点

还有一些特殊情况,就是一个节点或者链表为空

## 从尾删除
从尾删除并不容易,因为头节点和第二节点都容易找到,但是倒数第二个节点却并不容易找到,可以有这样几个步骤
* 如果链表为空,则返回空
* 如果只有一个节点也容易处理
* 如果有多个情况,则遍历寻找到倒数第二个节点,将其next域置为空,并更新tail


# 代码
```Java
public class SLList<anyType> {
    // 头节点
    private SLNode<anyType> head;
    // 尾节点
    private SLNode<anyType> tail;

    public SLList() {
        head = tail = null;
    }

    public boolean isEmpty() {
        return tail == null;
    }

    public void insertToHead(anyType data) {
        SLNode<anyType> node = new SLNode<>(data);
        node.next = head;
        head = node;
        if (tail == null) {
            tail = head;
        }
    }

    public void insertToTail(anyType data) {
        SLNode<anyType> node = new SLNode<>(data);
        if (!isEmpty()) {
            node.next = null;
            tail.next = node;
            tail = node;
        } else {
            tail = node;
            head = node;
        }
    }


    public anyType deleteFromHead() {
        if (isEmpty()) {
            return null;
        } else if (head == tail) {   // 特殊情况,只有一个节点
            anyType data = head.data;
            head = tail = null;
            return data;
        } else {
            anyType data = head.data;
            head = head.next;
            return data;
        }
    }


    public anyType deleteFormTail() {
        if (isEmpty()) {
            return null;
        } else if (head == tail) {
            anyType data = head.data;
            head = tail = null;
            return data;
        } else {
            SLNode<anyType> temp = head;
            while (temp.next != tail) {
                temp = temp.next;
            }
            anyType data = tail.data;
            temp.next = null;
            tail = temp;
            return data;
        }
    }

    public void printAll() {
        SLNode<anyType> temp = head;
        if (isEmpty()) {
            System.out.println("空表");
        } else if (head == tail) {
            System.out.println(head.data);
        } else {
            while (temp.next != null) {
                System.out.println(temp.data);
                temp = temp.next;
            }
            System.out.println(temp.data);
        }

    }
}

class SLNode<anyType> {
    public anyType data;
    public SLNode<anyType> next;

    public SLNode(anyType data) {
        this(data, null);
    }

    public SLNode(anyType data, SLNode<anyType> next) {
        this.data = data;
        this.next = next;
    }
}


class Main {
    public static void main(String[] args) {
        SLList<Integer> list = new SLList<>();
        list.insertToTail(10);
        list.insertToTail(20);
        list.insertToTail(45);
        list.insertToTail(89);
        list.printAll();
    }
}
```