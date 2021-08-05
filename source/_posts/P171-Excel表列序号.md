---
title: P171. Excel表列序号
tags:
  - Java
category: LeetCode每日一题
index: true
info: true
date: 2021-07-30 08:48:22
---

<!-- more -->

## 题目

![image-20210730084841731](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210730084841.png)

## 分析

- 26进制转换
- 模拟就好

## 题解

```java
class Solution {
    public int titleToNumber(String columnTitle) {
        int add = 1;
		int result = 0;
		for (int i = columnTitle.length()-1; i >= 0; i--) {
			result += add * (columnTitle.charAt(i) - 'A' + 1);
			add *= 26;
		}
		return result;
    }
}
```

![image-20210730084918549](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210730084918.png)

| 时间复杂度 | 空间复杂度 |
| :--------: | :--------: |
|    O(n)    |    O(1)    |



## 一点屁话

- 愉快的周五
