---
title: Stirling学习
top: 1
mathjax: true
date: 2018-07-24 21:28:44
tags:
	- 组合数学
cover_picture: images/stl.jpg
---
昨天学习了一发Stirling数~~哒哒哒~~，感觉很有用

首先，Stirling数~~哒哒哒~~分两类

## 第一类Stirling数
$$\begin{bmatrix} n \\ k \end{bmatrix}$$

这表示第一类Stirling数~~哒哒哒~~

### 意义

由 $$n$$ 个数构成置换的方案数

### 递推式

$$\begin{bmatrix} n \\ k \end{bmatrix} = \begin{bmatrix} n-1 \\ k-1 \end{bmatrix} + (n-1)\begin{bmatrix} n-1 \\ k \end{bmatrix} $$

解释很简单，对于新加的$$n$$,要么单独成一个置换，要么放在某个的后面

### 用途

$$x^{\overline{n}} = \sum_{k}\begin{bmatrix} n \\ k \end{bmatrix}x^{k}$$

$$x^{\underline{n}} = \sum_{k}\begin{bmatrix} n \\ k \end{bmatrix}(-1)^{n-k}x^{k}$$

## 第二类Stirling数
$$\begin{Bmatrix} n \\ k \end{Bmatrix}$$

这表示第二类Stirling数~~哒哒哒~~

### 意义

由 $$n$$ 个数构成集合的方案数

### 递推式

$$\begin{Bmatrix} n \\ k \end{Bmatrix} = \begin{Bmatrix} n-1 \\ k-1 \end{Bmatrix} + k\begin{Bmatrix} n-1 \\ k \end{Bmatrix} $$

解释很简单，对于新加的$$n$$,要么单独成一个置换，要么放在某个的后面

### 用途

$$x^{n} = \sum_{k}\begin{Bmatrix} n \\ k \end{Bmatrix}x^{\underline{k}}$$

$$x^{n} = \sum_{k}\begin{Bmatrix} n \\ k \end{Bmatrix}(-1)^{n-k}x^{\overline{k}}$$
