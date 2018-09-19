---
layout: post
title: 'n皇后问题'
subtitle: ''
date: 2018-09-05
categories: 算法
cover: ''
tags: 算法 回溯 n皇后问题
---
# 介绍
n皇后问题是算法领域很有名的问题,它采用的一种很暴力也是很有用的算法:回溯算法.

n皇后问题是这样的,皇后可以攻击同一行,同一列和对角线上的任意其他皇后,现在需要找到一个算法将皇后摆在棋盘上,使得任何一个皇后不能攻击任意一个皇后.它的大概思路是这样的:首先摆好第一个皇后,然后摆好第二个元素,直至摆好第八个皇后,任何一个皇后若是无位置可摆,则依次回溯,直至摆好为止.这个过程是很麻烦的.

# 思路
使用一个nxn的二维数组代表棋盘,这个数组被初始化为1,当每一个皇后放在一个位置(r,c)时,这个位置就被置为0,与此同时,这个皇后可以攻击的位置,同一行第r行,同一列第c列,对角线(r+n,c+n)都被置为0,表示他们不可用.回溯时,这些位置有需要被复位为1,代表他们可用.

因为规则的约束,从另一个方向思考,我们要做的只不过是为每一个皇后分配一个列(当然也可以倒这想,为每一个皇后分配一个行,他们必须不同行不同列),这样我们的思考的维度就从二维变为了一维.

## 对角线的判定
同行好判定,同列也好判定,对角线是这样判定的
<center>
<table>
<tr>
<td>0,0</td>
<td>0,1</td>
<td>0,2</td>
<td>0,3</td>
</tr>
<tr>
<td>1,0</td>
<td>1,1</td>
<td>1,2</td>
<td>1,3</td>
</tr>
<tr>
<td>2,0</td>
<td>2,1</td>
<td>2,2</td>
<td>2,3</td>
</tr>
<tr>
<td>3,0</td>
<td>3,1</td>
<td>3,2</td>
<td>3,3</td>
</tr>
</table>
</center>
左对角线行和列相加依次为0,1,2,3,4,5,6

右对角线行和列相减依次为-3,-2,-1,0,1,2,3

我们把这些值作为一个索引代表每一个对角线,因为负数不可作为索引,我们可以将右对角线加个3.

# 代码实现
```Java
public class NQueens {
    // cell是否可用,由于规则的约束,只要放下一个皇后,那么同行,同列和对角线都不可用
    static final boolean available = true;
    // 棋盘的行列数
    static final int squares = 4;
    // 对角线索引需要加的值
    static final int norm = squares - 1;
    static int[] positionInRow = new int[squares];
    // 行是否可用
    static boolean[] columu = new boolean[squares];
    // 右对角线是否可用,4个格子右的对角线一共7条
    static boolean[] rightDiagonal = new boolean[squares * 2 - 1];
    // 左对角线是否可用,4个格子左的对角线一共7条
    static boolean[] leftDiagonal = new boolean[squares * 2 - 1];
    static int howMany = 0;

    // 初始化
    // 行列都可用
    NQueens() {
        for (int i = 0; i < squares; i++) {
            positionInRow[i] = -1;
            columu[i] = available;
        }

        for (int i = 0; i < squares * 2 - 1; i++) {
            leftDiagonal[i] = available;
            rightDiagonal[i] = available;
        }
    }

    // 从0行开始放置皇后
    static void putQueen(int row) {
        for (int col = 0; col < squares; col++) {
            if (columu[col] == available && leftDiagonal[row + col] == available && rightDiagonal[row - col + norm] == available) {
                // 这一行选定的列,代表已经找到了一个列使得放置的皇后满足约束条件
                positionInRow[row] = col;
                columu[col] = !available;
                leftDiagonal[row + col] = !available;
                rightDiagonal[row - col + norm] = !available;
                if (row < squares - 1) {
                    putQueen(row + 1);
                } else {
                    System.out.println("成功");
                }
                columu[col] = available;
                leftDiagonal[col] = available;
                rightDiagonal[col] = available;
            }
        }
    }

    static public void main(String[] args) {
        NQueens nQueens = new NQueens();
        nQueens.putQueen(0);
    }
}
```