---
title: SICP
tags:
  - null
category:
  - 读书笔记
index: true
info: true
date: 2021-07-21 23:08:12
---
<!-- more -->

# 第一章

## 1.2

### 1.2.5 最大公约数

- if r is the remainder when a is divided by b, then the common divisors of a and b are precisely the same as the common divisors of b and r.
- GCD(a,b) = GCD(b,r)

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210721234632.png" alt="image-20210721234632688" style="zoom: 50%;" />

### 练习1.20

### 1.2.6 素数检测

- 费马小定理

> If n is a prime number and a is any positive integer less than n, then a raised to the nth  power is congruent to a modulo n.

```lisp
(define (expmod base exp m)

(cond ((= exp 0) 1)

((even? exp) (remainder

(square (expmod base (/ exp 2) m))

m)) (else (remainder

(* base (expmod base (- exp 1) m))

m))))
```

### 练习1.21

