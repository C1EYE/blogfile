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

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210613010115.png" alt="image-20210613010115758" style="zoom:50%;" />

`LISP的解释器使用「应用序求值」，使用cond会试图求值new-if的三个参数，对于第三个参数会导致无限求值下去`

`if则会先求值谓词，然后再决定求值哪个情况`

### 1.7

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210614004139.png" alt="image-20210614004139064" style="zoom:50%;" />

`如果数字非常大，猜测值和这个数的和很可能会超出最大表示范围而溢出，对于很小的数，也会有溢出和精度损失

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210614010038.png" alt="image-20210614010038531" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210614010241.png" alt="image-20210614010241799" style="zoom:50%;" />

```lisp
(define (good-enough? guess x)
	(< (abs (- (improve guess x) guess)) 0.001))
```

### 1.8

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210614094148.png" alt="image-20210614094148327" style="zoom:50%;" />

```lisp

(define (cube x) (* x x x))

(define (improve guess x) 
	(/ (+ (/ x (* guess guess)) (* 2 guess)) 3))

(define (good-enough? guess x)
	(<  (/ (abs (- (improve guess x) guess)) guess) 0.1))

(define (cube-iter guess x)
	(if (good-enough? guess x) guess 
		(cube-iter (improve guess x) x)))

(define (cube-root x) (cube-iter 1 x))

(cube-root 16)
```

> 得出的结果根本无从验证啊...

### 1.9

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210614111201.png" alt="image-20210614111201729" style="zoom:50%;" />

- 第一种

```lisp
(+ 4 5)
(inc (+ 3 5))
(inc (inc (+ 2 5)))
(inc (inc (inc (+ 1 5))))
(inc (inc (inc (inc (+ 0 5)))))
(inc (inc (inc (inc 5))))
(inc (inc (inc 6)))
(inc (inc 7))
(inc 8)
9
```

`这是一个递归的计算过程"recursive process"`

- 第二种

```lisp
(+ 4 5)
(+ 3 6)
(+ 2 7)
(+ 1 8)
(+ 0 9)
9
```

`这是一个迭代的计算过程"iterative preocess"`

### 1.10

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210614113556.png" alt="image-20210614113556470" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210614113541.png" alt="image-20210614113541499" style="zoom:50%;" />

```
(A 2 4)
(A 1 (A 2 3))
(A 1 (A 1 (A 2 2)))
(A 1 (A 1 (A 1 (A 2 1))))
(A 1 (A 1 (A 1 2)))
(A 1 (A 1 (A 0 (A 1 1))))
(A 1 (A 1 (A 0 2)))
(A 1 (A 1 4))
(A 1 (A 0 (A 1 3)))
(A 1 (A 0 (A 0 (A 1 2))))
(A 1 (A 0 (A 0 (A 0 (A 1 1)))))
(A 1 (A 0 (A 0 (A 0 2))))
(A 1 (A 0 (A 0 4)))
(A 1 (A 0 8))
(A 1 16)
```

`(f n)计算的是2n ,(g n)计算的是2^n,(h n)计算的是什么鬼啊`

### 1.11

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210614151815.png" alt="image-20210614151704319" style="zoom:50%;" />

```lisp
(define (f n)
	(if (< n 3)
		n
		(+ (f (- n 1)) 
			(* 2 (f (- n 2)))
			(* 3 (f (- n 3)))
	)))
```

迭代版本的暂时没弄懂...先放着

```lisp
(define (f n)
	(define (f-i a b c count)
		(cond ((< n 3) n)
			((<= count 0) a)
			(else (f-i (+ a (* 2 b) (* 3 c)) a b (- count 1))))
		)
	(f-i 2 1 0 (- n 2))
	)
```

### 1.12

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210616185736.png" alt="image-20210616185736137" style="zoom: 33%;" />

```lisp
(define (passcal row col)
	(if (or (= col 1) (= row col))
		1
		(+ (passcal (- row 1) (- col 1)) (passcal (- row 1) col))
		)
	)

```

### 1.13

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210616190408.png" alt="image-20210616190408249" style="zoom:50%;" />

### 1.14

### 1.15

![image-20210616214356155](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210616214356.png)

![image-20210616214413923](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210616214413.png)

```lisp
(sine 12.15) 
  
 (p (sine 4.05)) 
  
 (p (p (sine 1.35))) 
  
 (p (p (p (sine 0.45)))) 
  
 (p (p (p (p (sine 0.15))))) 
  
 (p (p (p (p (p (sine 0.05)))))) 
  
 (p (p (p (p (p 0.05))))) 
```

`空间log(n)`

### 1.16

