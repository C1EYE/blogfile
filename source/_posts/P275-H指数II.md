---
title: P275 H指数II
tags:
  - Java
category: LeetCode每日一题
index: true
info: true
date: 2021-07-12 08:57:15
---

<!-- more -->

## 题目

![image-20210712085746329](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210712085746.png)

## 分析

- 和昨天一样的题，要求logn级别时间复杂度
- 显然就是二分
- 只要找出最后一个比身后元素个数小的元素就找到了h

## 题解

```java
class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
       int left = 0;int right = n;
       while(left<right) {
           int mid = (left+right)>>1;
           if(citations[mid]>=n-mid){
               right = mid;
           }else{
               left = mid+1;
           }
       }
       return n-left;
    }
}
```



| 时间复杂度 | 空间复杂度 |
| :--------: | :--------: |
|    O(logn)    |    O(1)    |



## 一点屁话

- 好耶
