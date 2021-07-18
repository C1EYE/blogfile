---
title: 剑指 Offer 53 - I. 在排序数组中查找数字 I
tags:
  - Java
category: LeetCode每日一题
index: true
info: true
date: 2021-07-16 08:07:13
---

<!-- more -->

## 题目

![image-20210716080734645](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210716080734.png)

## 分析

- 二分查找上下界即可
- 也可以找到上界或下届之后遍历

## 题解

```java
class Solution {
    public int search(int[] nums, int target) {
        if(nums==null||nums.length==0)return 0;
        int begin = 0;int end = nums.length;
        while(begin<end){
            int mid = (begin+end)>>1;
            if(nums[mid]<target){
                begin = mid+1;
            }else {
                end = mid;
            }
        }
      //也可以再找一次上界
      //while(begin<end){
      //   int mid = (begin+end)>>1;
      //  if(nums[mid]>target){
			//		end  = mid;
      //    }else {
      //        begin = mid+1;
      //    }
      //}
        int count = 0;
        while(begin<nums.length&&nums[begin++]==target){
            count++;
        }
        return count;
    }
}
```

![image-20210716080844685](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210716080844.png)

| 时间复杂度 | 空间复杂度 |
| :--------: | :--------: |
|    O(logn)    |    O(1)    |

## 一点屁话

- 面试真的会有这种题？
