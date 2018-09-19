---
layout: post
title: '合并排序'
subtitle: ''
date: 2018-08-19
categories: 算法
cover: ''
tags: 算法 Java
---

# 介绍
合并排序分为两个部分,**首先是拆,然后才是合并**,比如说对于数组$$A[0,n-1]$$,合并排序算法首先把它拆为两个部分$$A[0,\lfloor n/2\rfloor-1]$$和$$[\lfloor n/2\rfloor,n-1]$$,然后递归的再次拆分,递归的两个重要条件,参数就是要拆分的数组,返回条件是$$n>1$$.

# 算法分析
## 拆分算法
拆分算法是一个递归算法,既然是递归算法,那么最重要的就是参数和返回条件,参数就是待拆分的数组,返回条件就是$$n>1$$,拆分可以像下面这样设计.
```Java
    private static void copy(int[] array, int[] newArray, int start, int end) {
        int j = 0;
        for (int i = start; i < end; i++) {
            newArray[j] = array[i];
            j++;
        }
    }

    public static void split(int[] array) {
        if (array.length > 1) {
            int mid = (int) Math.floor(array.length / 2);
            int bArray[] = new int[mid];
            int cArray[] = new int[array.length - mid];
            copy(array, bArray, 0, mid);
            copy(array, cArray, mid, array.length);
            split(bArray);
            split(cArray);
            return;
        }else {
            for (int i=0;i<array.length;i++){
                System.out.println(array[i]);
                System.out.println("----");
            }
            return;
        }
    }
```
其实我的基础程序设计真的很菜,我复制函数真的设计了很久的时间,我觉得这是一个很基础的东西,即使我承认我是为了寻找更加优雅的复制函数,这个复制函数真的很暴力,或许我是调包习惯了,很不习惯这么基础的暴力算法,还有Java的Math.floor()方法,我是真的很无语,明明就是向下取**整**方法,他居然返回一个Double类型的数据,不知道Java强转策略的人(比如我)很容易就被坑.其实基础的算法设计也很考验逻辑能力和一些基础知识.

当递归函数最终返回的时候(就是到达返回条件的时候),返回的都是只有一个元素的数组.下面来看合并算法.

## 合并算法
合并算法是在返回的时候才起作用,也就是在拆分完毕后回溯才起作用,因为它在递归调用的最后调用,它的作用是把拆分的数组又组合起来,因为是在递归函数内,所以合并算法也会被调用多次,每递归函数每返回一次,合并算法就被调用一次.合并的每两个数组其实都是有序的,当要合并的数组只有一个元素时,显然是有序的,这就是有序的源头.
合并算法可以这样设计
```Java
    private static void merge(int[] array, int[] bArray, int[] cArray) {
        int i = 0;
        int j = 0;
        int k = 0;
        while (i < bArray.length && j < cArray.length) {
            if (bArray[i] <= cArray[j]) {
                array[k] = bArray[i];
                i++;
            } else {
                array[k] = cArray[j];
                j++;
            }
            k++;
        }
        if (i == bArray.length) {
            for (int m = k; m < array.length; m++) {
                array[m] = cArray[j];
                j++;
            }
        } else {
            for (int m = k; m < array.length; m++) {
                array[m] = bArray[i];
                i++;
            }
        }
    }
```
## 完整合并排序
```Java
public class MergeSort {
    public static void main(String[] args) {
        int array[] = {8,3,2,9,7,1,5,4};
        split(array);
        for (int i=0;i<array.length;i++){
            System.out.println(array[i]);
        }
    }

    private static void copy(int[] array, int[] newArray, int start, int end) {
        int j = 0;
        for (int i = start; i < end; i++) {
            newArray[j] = array[i];
            j++;
        }
    }

    public static void split(int[] array) {
        if (array.length > 1) {
            int mid = (int) Math.floor(array.length / 2);
            int bArray[] = new int[mid];
            int cArray[] = new int[array.length - mid];
            copy(array, bArray, 0, mid);
            copy(array, cArray, mid, array.length);
            split(bArray);
            split(cArray);
            merge(array,bArray,cArray);
        } else {
            for (int i = 0; i < array.length; i++) {
                System.out.println(array[i]);
                System.out.println("----");
            }
        }
    }

    private static void merge(int[] array, int[] bArray, int[] cArray) {
        int i = 0;
        int j = 0;
        int k = 0;
        while (i < bArray.length && j < cArray.length) {
            if (bArray[i] <= cArray[j]) {
                array[k] = bArray[i];
                i++;
            } else {
                array[k] = cArray[j];
                j++;
            }
            k++;
        }
        if (i == bArray.length) {
            for (int m = k; m < array.length; m++) {
                array[m] = cArray[j];
                j++;
            }
        } else {
            for (int m = k; m < array.length; m++) {
                array[m] = bArray[i];
                i++;
            }
        }
    }
}
```



