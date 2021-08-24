---
title: 剑指 Offer 14- I. 剪绳子
tags:
  - 刷题
category: Leetcode
index: true
info: true
date: 2021-08-23 12:01:19
---

<!-- more -->

## 题目

![image-20210823120133883](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210823120133.png)

## 分析

- dp
- 贪心

## 题解

```java
class Solution {
    public int cuttingRope(int n) {
        if(n<2)return 0;
        if(n==2)return 1;
        if(n==3)return 2;
        int[] dp = new int[n+1];
        dp[1] = 1;
        dp[2] = 2;
        dp[3] = 3;
        int max;
        for(int i = 4;i<=n;++i){
            max = 0;
            for(int j = 1;j<=i>>1;++j){
                int cut = dp[j]*dp[i-j];
                max = Math.max(max,cut);
            }
            dp[i] = max;
        }

        return dp[n];
    }
}
```

```Java
class Solution {
    public int cuttingRope(int n) {
        if(n<2)return 0;
        if(n==2)return 1;
        if(n==3)return 2;
       
        //多剪3
        int timesOf3 = n/3;

        //剩下4的时候22开
        if(n - timesOf3*3 == 1){
            timesOf3-=1;
        }

      //剪2的数量
        int timesOf2 = (n - timesOf3*3)/2;

        return (int)(Math.pow(3,timesOf3)*Math.pow(2,timesOf2));
    }
}
```





## 一点屁话
