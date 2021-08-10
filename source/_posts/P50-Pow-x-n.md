---
title: 'P50. Pow(x, n)'
tags:
  - 刷题
category: Leetcode
index: true
info: true
date: 2021-08-10 09:53:33
---

<!-- more -->

## 题目

![image-20210810095351462](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210810095351.png)

## 分析

- 分治，将指数分为两半直到1或者-1，对于奇数需要记录并再*x
- 递推

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210810101212.png" alt="image-20210810101212668" style="zoom: 33%;" />

## 题解

- 分治

```java
class Solution {
    public double myPow(double x, int n) {
		if (n == 0) { return 1; }
		if (n == -1) { return 1 / x; }
		int odd = (n & 1);
		double half = myPow(x,n >> 1);
		half *= half;
		return odd == 1 ? half * x : half;
    }
}
```

- 递推

```java
public double myPow2(double x, int n) {
		boolean neg = n < 0;
		double res = 1.0;
  //陷阱
		long y =  n;
		if(neg){
			y  = -y;
		}
		while (y > 0){
			if((y&1)==1){
				res *= x;
			}
			x *= x;
			y >>= 1;
		}
		return neg ? (1 / res) : res;
	}
```



## 一点屁话

- 快
