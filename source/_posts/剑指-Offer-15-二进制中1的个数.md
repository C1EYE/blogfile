---
title: 剑指 Offer 15. 二进制中1的个数
tags: Java
category: LeetCode每日一题
date: 2021-06-23 09:08:39
---

<!-- more -->

## 题目

![image-20210623090938938](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210623090939.png)

## 分析

- 力扣里给的输入是整数，也不知道谁错了
- 还是位运算技巧，n&(n-1)或者其他的n&(-n)之类的每次消掉1，然后返回计数

## 题解

```java
 public int hammingWeight(int n) {
        int result = 0;
        while(n!=0){
            n&=(n-1);
            result++;
        }
        return result;
    }
```

- 当然Java里也有统计二进制位数的方法,Integer.bitCount()
- 实现有点看不懂...

```java
  public static int bitCount(int i) {
        // HD, Figure 5-2
        i = i - ((i >>> 1) & 0x55555555);
        i = (i & 0x33333333) + ((i >>> 2) & 0x33333333);
        i = (i + (i >>> 4)) & 0x0f0f0f0f;
        i = i + (i >>> 8);
        i = i + (i >>> 16);
        return i & 0x3f;
    }
```



| 时间复杂度 | 空间复杂度 |
| :--------: | :--------: |
|    O(logn)    |    O(1)    |



## 一点屁话

- 简单题真好啊
