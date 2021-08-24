---
title: 剑指 Offer 13. 机器人的运动范围
tags:
  - 刷题
category: Leetcode
index: true
info: true
date: 2021-08-23 11:17:16
---

<!-- more -->

## 题目

![image-20210823111742559](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210823111742.png)

## 分析

- 直接就能想到回溯

## 题解

```java
class Solution {
    public int movingCount(int m, int n, int k) {
         boolean[][] visited = new boolean[m][n];
        return dfs(m,n,0,0,k,visited);
    }

    int dfs(int m,int n,int row,int col,int k,boolean[][] visited){
        int count = 0;
       
        //不满足就回溯
        if(checkIndex(m,n,row,col,k,visited)){
            visited[row][col] = true;
            count = 1+
            dfs(m,n,row-1,col,k,visited)+
            dfs(m,n,row,col+1,k,visited)+
            dfs(m,n,row+1,col,k,visited)+
            dfs(m,n,row,col-1,k,visited);
        }
        return count;
    }

    boolean checkIndex(int m,int n,int row,int col,int k,boolean[][] visited){
        return  row>=0&&col>=0
            && row<m&&col<n
            && sum(row)+sum(col)<=k
            && !visited[row][col];
    }

    int sum(int num){
        int res = 0;
        while(num>0){
            res += num%10;
            num/=10;
        }
        return res;
    }


}
```

