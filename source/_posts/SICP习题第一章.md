---
title: SICP习题第一章
date: 2021-06-12 23:03:20
tags:
- SICP
category: 书
---

<!-- more -->

### 1.1

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210612232100.png" alt="image-20210612231515213" style="zoom:50%;" />





### 1.2

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210612232057.png" alt="image-20210612231313369" style="zoom:50%;" />

```lisp
(/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5))))) (* 3 (- 6 2) (- 2 7)))
```



### 1.3

```lisp
(define (maxSum x y z)
(cond ((and (< x y) (< x z)) (+ y z))
((and (< z y) (< z x)) (+ x y))
(else (+ x z))))
```

### 1.4

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210612232051.png" alt="image-20210612231930958" style="zoom:50%;" />

`定义了一个过程关联到名称“a-plus-abs-b”,有两个形式参数，过程体是返回|a-b|`

### 1.5

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210613000810.png" alt="image-20210613000810654" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210613000827.png" alt="image-20210613000827856" style="zoom:50%;" />

`如果是「应用序求值」，在计算（p）的值进行代入时会进入无限递归，表现为终端卡住；`

`如果是「正则序求值」，先将test对应的参数替换为复合过程，if特殊形式会因为（= x 0) 为#t而求值得0`

### 1.6





