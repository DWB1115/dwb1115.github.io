---
layout:     post   				    # 使用的布局（不需要改）
title:      傅里叶变换＆图像处理 			# 标题 
subtitle:   图像处理笔记  #副标题
date:       2021-04-22 				# 时间
author:     dwb 						# 作者
header-img: https://sm.ms/image/b7u8Kh513NWVyqz 	#这篇文章标题背景图片
catalog: false 						# 是否归档
tags:								#标签
    - Image Process 
    - Code
    - Math
---

## $\textbf{·}$ 信号、函数与傅里叶变换
### $\textbf{·}$ 一维连续傅里叶变换
<span style="font-family:楷体;font-size:21px;">　　首先，所有的周期函数均可以表示为一系列不同频率的正弦信号加和。比如有这样一个形状的函数：</span>

```{r echo=FALSE, message=FALSE, warning=FALSE}
x <- seq(-2*pi, 2*pi,length = 500)
f1 <- 3*sin(3*x + 0.1)
f2 <- 5*sin(6*x + 3)
plot(x, f1+f2, lwd=3, type = "l", ylim = c(-9, 8), ylab="f(x)")
```

<span style="font-family:楷体;font-size:21px;">　　显然，这是一个周期函数，可以分解为一系列正弦函数的加和。事实上，该信号由如下两个正弦信号组合得到：</span>

$$ \left\{
\begin{aligned}
f_1(x) & = 3sin(3x + 0.1) \\
f_2(x) & = 5sin(6x + 3)
\end{aligned}
\right.$$

<span style="font-family:楷体;font-size:21px;">　　如下所示,红色信号为$f_1(x)$，蓝色信号为$f_2(x)$，黑色（原函数）为红蓝两色信号合成得到的信号。</span>
```{r message=FALSE, warning=FALSE}
x <- seq(-2*pi, 2*pi,length = 500)
f1 <- 3*sin(3*x + 0.1)
f2 <- 5*sin(6*x + 3)
plot(x, f1, type = "l", col=2, xlim = c(-4, 4), ylim = c(-9, 8))
lines(x, f2, col=4)
lines(x, f1+f2, lwd=3)
```

<span style="font-family:楷体;font-size:21px;">　　正弦信号由三个定值：振幅$A$, 频率$w$, 和初始相位$\phi$决定；有如下表示形式：</span>
$$f_i(t) = A\cdot sin(wt+\phi)$$

<span style="font-family:楷体;font-size:21px;">　　而傅里叶变换，就是想要知道**一个周期信号是怎样由那些正弦信号组合而成的**，可以这样理解，傅里叶变换得到的频域映射$F(u)$，其实就是**频率为$u$的正弦信号，在原始信号中的权重/强度**。让我们回顾一下一维连续信号的傅里叶变换公式：</span>
$$F(u) = \mathcal{F}\left\{f(t)\right\} = \int_{-\infty}^{+\infty}f(t)\cdot e^{-j\cdot (2\pi  u) t}dt$$


---
