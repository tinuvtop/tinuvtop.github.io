---
layout: post
title: '动态规划算法入门'
subtitle: '算法虐我千百遍,我待算法如初恋'
date: 2018-08-22
categories: 动态规划
cover: ''
tags: 算法 动态规划
---

# 介绍
动态规划策略是算法设计里面很重要的策略,他也是用空间换时间的一种策略,他把一个递归算法转化为非递归算法,并把中间结果记录在一个表格里,使得一些计算可以省略从下面斐波那契数列(斐波那契数列是提到递归必定要举的例子)可以看出.

# 斐波那契数列的例子
斐波那契数列的数学表达式是这样的



$$ 

f(n)=\begin{cases}
1 &0\leq n\leq 1\\
f(n-1)+f(n-2) &n>1
\end{cases}

$$

## 普通斐波那契数列
变成程序是这样的
```java
    public static int fib(int n) {
        if (n <= 1) {
            return 1;
        } else {
            return fib(n - 1) + fib(n - 2);
        }
    }
```
这个程序看起来很简洁,但是他会多余的计算.首先他会首先递归的计算fib(n-1)然后递归的计算fib(n-2),它们的计算过程中很多的中间结果是相同的,计算机可不会像人一样计算fib(n-2)的时候利用计算fib(n-1)的中间结果,他会严格的先计算fib(n-1),计算得出结果后中间结果随之丢失,然后计算fib(n-2).动态规划就是希望解决这个问题,合理的利用中间结果来减少不必要的计算.

看一下它的计算过程


$$
\begin{gathered}
f(n) = f(n-1)+f(n-2)\\
f(n-1) = f(n-2)+f(n-3)\\
f(n-2) = f(n-3)+f(n-4)\\
f(n-3) = f(n-4)+f(n-5)\\
...
\end{gathered}
$$
 
 
计算f(n)的时候需要计算f(n-1)和f(n-2),计算f(n-1)和f(n-2)的时候多余的计算了f(n-3),这种多余计算代价是庞大的,因为每一个多余计算都意味着递归.

## 使用动态规划的斐波那契数列
使用了动态规划算法的斐波那契数列是这样的
```Java
    private static int fibonacci(int n) {
        if (n <= 1) {
            return 1;
        }
        int last = 1;
        int nextToLast = 1;
        int answer = 1;

        for (int i = 2; i <= n; i++) {
            answer = last + nextToLast;
            nextToLast = last;
            last = answer;
        }
        
        return answer;
    }
```
这个算法是一个自底向上的一个算法,他使用两个变量`last`和`nextToLast`来记录中间变量,斐波那契数列算法很简单,计算时,它只与倒数第一个数和倒数第二数有关,所以没有必要用表格来储存中间变量,一般的采用动态规划算法都比较复杂,会使用一维表,二维表甚至三维表来储存中间变量.可以看到,采用动态规划算法来计算斐波那契数列合理的利用了中间结果使得计算过程简单了很多.

# 第二个例子
看一下下面这个递推式


 $$
f(n)=\begin{cases}
1 &n=0 \\
\frac 2 n\sum_{i=0}^{n-1}f(i)+n &n>0
\end{cases}
 $$
 
 
## 普通的递归程序
这个递推式的递归算法如下所示
```Java
   private static double eval(int n) {
        if (n == 0) {
            return 1.0;
        } else {
            double sum = 0.0;
            for (int i = 0; i < n; i++) {
                sum = sum + eval(i);
            }
            return 2.0 * sum / n + n;
        }
    }
```
仔细分析这个算法,发现它也有冗余计算它的计算过程是这样依赖中间结果的

省略已知变量,只看依赖关系


$$
\begin{gathered}
f(n) = f(n-1)+f(n-2)+...+f(0)\\
f(n-1) = f(n-2)+f(n-3)+...+f(0)\\
f(n-2) = f(n-3)+f(n-4)+...+f(0)\\
\end{gathered}
$$
 
 
这个与斐波那契数列不同,它的计算不仅依赖倒数两个,它依赖更多的中间结果,所以用一个一维表来储存中间结果是合理的.


## 使用动态规划算法
```Java
    private static double eval_dy(int n) {
        double[] c = new double[n + 1];
        c[0] = 1.0;
        for (int i = 1; i <= n; i++) {
            double sum = 0.0;
            for (int j = 0; j < i; j++) {
                sum = sum + c[j];
            }
            c[i] = 2.0 * sum / i + i;
        }
        return c[n];
    }
```
这个算法使用了一个一维表来储存中间变量,但是它还可以优化,原因是计算过程依赖的中间变量是累加,这是一个非常简单的关系,我们可以用一个变量储存sum,不至于每一次都遍历计算

## 优化的动规算法
```Java
    private static double eval_my(int n) {
        double[] c = new double[n + 1];
        c[0] = 1.0;
        double sum = c[0];
        for (int i = 1; i <= n; i++) {
            c[i] = 2.0 * sum / i + i;
            sum = sum + c[i];
        }
        return c[n];
    }
```
经过我实际测试,下面两种动规算法的效率远远高于递归算法.

