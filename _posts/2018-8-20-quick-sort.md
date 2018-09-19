---
layout: post
title: '快速排序'
subtitle: ''
date: 2018-08-20
categories: 算法
cover: ''
tags: 算法 排序
---
# 介绍
快速排序与合并排序也一样使用了分治的策略,快速排序也会对元素进行划分,不过合并排序是根据位置来划分,而快速排序是根据元素的大小进行划分.它的大概思路是:比如对于数组$$A[0,n-1]$$,从中挑选一个数设为$$A[s]$$将小于等于$$A[s]$$的数存放在数组的左边,将大于$$A[s]$$的元素存放在数组的右边,那么显然$$A[s]$$的位置就确定了,只要这个操作递归的对两个子数组进行操作,那么就可以排序完成.

# 划分算法的设计

## 霍尔划分算法
从上面的介绍可以知道,划分算法是很重要的,事实上有很多种划分算法,下面来说一下`Hoare`划分.划分算法需要选取一个值作为对比的对象,选择这个数也有很多种策略,这里用最简单的选取策略,选取数组的第一个元素作为对比的对象,**也就是所谓的中轴**,这个数不一定是数组的平均值,虽然我们希望他是数组的平均值,但因为数组是乱序的,所以什么可能都有.

霍尔划分算法使用了双指针的策略,一个指针从数组的左边开始扫描,一个指针从数组的第二个元素扫描(第一个元素被选定作为中轴),因为我们希望小于中轴的元素放在中轴的左边,大于种轴的元素放在中轴的右边,所以左边的指针一旦遇到大于等于中轴的元素,我们就暂停扫描,右边的指针一旦遇到小于等于中轴的元素**(为什么两个指针遇到等于中轴的元素都会停止,因为这样可以使得数组划分得更加平均)**,我们也立即暂停扫描.当左边的指针暂停而且右边的指针暂停了那么我们就交换这两个元素,这样较小的元素就会到中轴的左边,较大的元素就会到中轴的右边.

一直扫描会遇到两种情况然后跳出循环,一个是两个指针相交,设左指针为i,右指针为j,左指针停止的位置是大于中轴的,而右指针停止的位置是小于中轴的,这时候只要将右指针指向的位置,也就是第一个元素交换就可以了,因为右指针指向的元素是小于中轴的,而中轴恰好在数组的最左边.第二种情况是左指针指向的元素和右指针指向的元素相同,也就是两个指针重合了,这时这个元素一定等于中轴元素,这时只要将这个位置作为分裂点就可以了.

划分算法可以这样设计(这样设计会有i越界的问题)
```Java
    private static int hoarePartition(int array[]) {
        int mid = array[0];
        int i = 0;
        int j = array.length;
        do {
            do {
                i++;
            } while (array[i] < mid);
            do {
                j--;
            } while (array[j] > mid);
            int temp = array[i];
            array[i] = array[j];
            array[j] = temp;
        } while (i < j);
        int temp1 = array[i];
        array[i] = array[j];
        array[j] = temp1;
        int temp2 = array[0];
        array[0] = array[j];
        array[j] = temp2;
        return j;
    }
```

更精简的霍尔划分算法,不会有越界问题,思想是差不多的,代码更优雅
```Java
    private static int rHoarePartition(int array[], int left, int right) {
        int mid = array[right];
        while(left < right)
        {
            while(left < right && array[left] <= mid)
            {
                ++left;
            }
            array[right] = array[left];
            while(left < right && array[right] >= mid)
            {
                --right;
            }
            array[left] = array[right];
        }
        array[right] = mid;
        return right;
    }
```

## Lomuto划分算法
Lomuto划分算法将待划分的数组分为三个段,这三个段按顺序排在中轴p(也就是第一个元素)的后面,一段为**已知**的小于中轴的元素,一段为**已知**的大于等于中轴的元素,一段为未比较过的**未知**段,显然一开始的时候,第一段和第二段的元素为0,只有第三段未知段,算法的过程就是把未知段变为已知段直到划分完成.

算法从数组第二个元素开始扫描,每一次迭代,它都会将未知段的一个元素与中轴进行对比,如果未知的元素大于中轴,就将其放在第二段,小于则放在第一段,这样未知段将会缩小.而前面两端将会扩大.

这个算法可以像这样设计
```Java
    private static int lomutoPartition(int array[]) {
        int mid = array[0];
        int s = 0;
        for (int i = 1; i < array.length; i++) {
            if (array[i] < mid) {
                s++;
                int temp = array[s];
                array[s] = array[i];
                array[i] = temp;
            }
        }
        int temp1 = array[s];
        array[s] = array[0];
        array[0] = temp1;
        return s;
    }
```

# 快速排序算法
```Java
public class QuickSort {
    public static void main(String[] args) {
        int array[] = {10, 7, 0, 2, 1, 23, 90, 12};
        int mid = rHoarePartition(array, 0, array.length - 1);
        System.out.println(mid);
        quickSort(array, 0, array.length - 1);
        for (int i = 0; i < array.length; i++) {
            System.out.println(array[i]);
        }
    }

    private static void quickSort(int array[], int left, int right) {
        if (left < right) {
            int s = rHoarePartition(array, left, right);
            quickSort(array, left, s - 1);
            quickSort(array, s + 1, right);
            return;
        } else {
            return;
        }
    }


    private static int hoarePartition(int array[], int left, int right) {

        int mid = array[left];
        int i = left;
        int j = right;
        do {
            do {
                i++;
            } while (array[i] < mid);
            do {
                j--;
            } while (array[j] > mid);
            int temp = array[i];
            array[i] = array[j];
            array[j] = temp;
        } while (i < j);
        int temp1 = array[i];
        array[i] = array[j];
        array[j] = temp1;
        int temp2 = array[left];
        array[left] = array[j];
        array[j] = temp2;
        return j;
    }

    private static int rHoarePartition(int array[], int left, int right) {
        int mid = array[right];
        while (left < right) {
            while (left < right && array[left] <= mid) {
                ++left;
            }
            array[right] = array[left];
            while (left < right && array[right] >= mid) {
                --right;
            }
            array[left] = array[right];
        }
        array[right] = mid;
        return right;
    }

    private static int lomutoPartition(int array[]) {
        int mid = array[0];
        int s = 0;
        for (int i = 1; i < array.length; i++) {
            if (array[i] < mid) {
                s++;
                int temp = array[s];
                array[s] = array[i];
                array[i] = temp;
            }
        }
        int temp1 = array[s];
        array[s] = array[0];
        array[0] = temp1;
        return s;
    }

}
```
# 参考
算法设计与分析基础---Anany Levitin著,潘彦译