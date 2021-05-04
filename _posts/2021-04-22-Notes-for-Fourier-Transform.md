---
layout:     post   				    # 使用的布局（不需要改）
title:      傅里叶变换＆图像处理 			# 标题 
subtitle:   图像处理笔记  #副标题
date:       2021-04-22 				# 时间
author:     dwb 						# 作者
header-img: https://i.loli.net/2021/05/04/V91JEusn7xcmpRY.jpg  #这篇文章标题背景图片
catalog: false 						# 是否归档
tags:								#标签
    - Image Process 
    - Code
    - Math
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>



#  信号、函数与傅里叶变换
##  *一维连续傅里叶变换*
<pre>
library(plot3D)
</pre>

<span style="font-family:楷体;font-size:19px;">　　首先，所有的周期函数均可以表示为一系列不同频率的正弦信号加和。比如有这样一个形状的函数：</span>

![1.jpg](https://i.loli.net/2021/05/04/rct9hzVoYMgXGHa.jpg)

<span style="font-family:楷体;font-size:19px;">　　显然，这是一个周期函数，可以分解为一系列正弦函数的加和。事实上，该信号由如下两个正弦信号组合得到：</span>


$$ \left\{
\begin{aligned}
f_1(x) & = 3sin(3x + 0.1) \\
f_2(x) & = 5sin(6x + 3)
\end{aligned}
\right.$$

<span style="font-family:楷体;font-size:19px;">　　如下所示,红色信号为$f_1(x)$，蓝色信号为$f_2(x)$，黑色（原函数）为红蓝两色信号合成得到的信号。</span>

<pre>x <- seq(-pi, pi,length = 500)
f1 <- 3*sin(3*x + 0.1)　　
f2 <- 5*sin(6*x + 3)　　
plot(x, f1, type = "l", col=2, xlim = c(-4, 4), ylim = c(-9, 8))　　
lines(x, f2, col=4)　　
lines(x, f1+f2, lwd=3)</pre>

<pre>
x <- seq(-pi, pi,length = 500)
f1 <- 3*sin(3*x + 0.1)　　
f2 <- 5*sin(6*x + 3)　　
plot(x, f1, type = "l", col=2, xlim = c(-4, 4), ylim = c(-9, 8))　　
lines(x, f2, col=4)　　
lines(x, f1+f2, lwd=3)
</pre>
![2.jpg](https://i.loli.net/2021/05/04/XpmfVvOsbyS5J8Q.jpg)

<span style="font-family:楷体;font-size:19px;">　　正弦信号由三个定值：振幅$A$, 频率$w$, 和初始相位$\phi$决定；有如下表示形式：</span>

$$f_i(t) = A\cdot sin(wt+\phi)$$

<span style="font-family:楷体;font-size:19px;">　　而傅里叶变换，就是想要知道**一个周期信号是怎样由那些正弦信号组合而成的**，可以这样理解，傅里叶变换得到的频域映射$F(u)$，其实就是**频率为$u$的正弦信号，在原始信号中的权重（或者说，强度）**。让我们回顾一下一维连续信号的傅里叶变换公式：</span>

$$F(u) = \mathcal{F}\left\{f(t)\right\} = \int_{-\infty}^{+\infty}f(t)\cdot e^{-j\cdot (2\pi  u)t}dt$$

<span style="font-family:楷体;font-size:19px;">　　示例中，该信号的傅里叶变换的结果如下所示：</span>


<pre>plot(seq(0, 10, 1), abs(fft(f1+f2)[1:11]), type="l", xlim=c(0,10))
</pre>
![3.jpg](https://i.loli.net/2021/05/04/jhlfWKTxzsbyFNH.jpg)
<span style="font-family:楷体;font-size:19px;">　　可以很明显地看到，在频域表达式中，仅有频率为3和6的两种正弦信号的权重较大。而事实上，该信号确实由这两种频率的正弦信号组合而成。**傅里叶变换本身最有用的地方，就是可以很直观地告诉我们，输入信号是由哪几个频率的正弦波所构成的，并且可以得到各个正弦波的振幅比例**。</span>


<span style="font-family:楷体;font-size:19px;">　　由一张图总结如下：</span>

<center> <img src="https://i.loli.net/2021/05/04/sL9RwuhN5Y4Tyed.jpg" ></a> </center>

<span style="font-family:楷体;font-size:19px;">　　现实中的信号往往由很多种频率的正弦信号组合而成，此时傅里叶变换得到的频域映射其实就是各个频率信号的权重（含量）表示。</span>


##  *二维连续傅里叶变换*
<span style="font-family:楷体;font-size:19px;">　　二变量函数的图像可以在三维坐标轴中可视化得到。其数学定义如下所示：</span>

$$F(u, v) = \mathcal{F}\left\{f(t,z)\right\} = \int_{-\infty}^{+\infty} \int_{-\infty}^{+\infty}f(t,z)\cdot e^{-j\cdot 2\pi  (ut+vz)}dtdz$$

<span style="font-family:楷体;font-size:19px;">　　同时，对于二维正弦函数而言，其由四个值决定。除去振幅, 频率, 和初始相位之外，还引入了传播方向这一向量。可以由下所表达（方向信息蕴含在$w_1$和$w_2$的比例之中）：</span>

$$f_i(t,z) = A\cdot sin(w_1t + w_2z+\phi)$$

<span style="font-family:楷体;font-size:19px;">　　我们用一个可视化的例子示意如下。从同样的角度去观察如下两个函数的图像：</span>

$$ \left\{
\begin{aligned}
f_1(x) & = sin(0.5x + y) \\
f_2(x) & = sin(-x + 0.5 y)
\end{aligned}
\right.$$

<pre>f1 <- function(x, y) {
  u <- 0.5
  v <- 1
  sin(u*x + v*y)
}

f2 <- function(x, y) {
  u <- -1
  v <- 0.5
  sin(u*x + v*y)
}
</pre>

<pre>
x <- seq(-10,5,length = 200)
y <- x
z1 <- outer(x, y, f1)
nrz <- nrow(z1)
ncz <- ncol(z1)
jet.colors <- colorRampPalette(c("gray80", "gray10"))
nbcol <- 100
color <- jet.colors(nbcol)
zfacet <- z1[-1, -1] + z1[-1, -ncz] + z1[-nrz, -1] + z1[-nrz, -ncz]
facetcol <- cut(zfacet, nbcol)
persp(x, y, z1,
        theta = 60, phi = 30,
        expand = 0.5, col = color[facetcol])
</pre>
![4.jpg](https://i.loli.net/2021/05/04/Y1CyuoEsJ9cR43e.jpg)
<pre>
z2 <- outer(x, y, f2)
nrz <- nrow(z2)
ncz <- ncol(z2)
jet.colors <- colorRampPalette(c("gray80", "gray10"))
nbcol <- 100
color <- jet.colors(nbcol)
zfacet <- z2[-1, -1] + z2[-1, -ncz] + z2[-nrz, -1] + z2[-nrz, -ncz]
facetcol <- cut(zfacet, nbcol)
persp(x, y, z2,
        theta = 60, phi = 30,
        expand = 0.5, col = color[facetcol])
</pre>
![5.jpg](https://i.loli.net/2021/05/04/5FRH6CMvqU2NEOz.jpg)

<span style="font-family:楷体;font-size:19px;">　　可以看到，这是两列互相垂直的正弦波。其原因是两个正弦波的传播法向量$(1,2)$和$(-2,1)$之间互相垂直。再来看看这两列正弦波给上不同振幅后的组合：</span>

<pre>  
z1 <- 3*outer(x, y, f1)
z2 <- 5*outer(x, y, f2)
z <- z1+z2
z[is.na(z)] <- 1
op <- par(bg = "white")
nrz <- nrow(z)
ncz <- ncol(z)
jet.colors <- colorRampPalette(c("gray80", "gray10"))
nbcol <- 100
color <- jet.colors(nbcol)
zfacet <- z[-1, -1] + z[-1, -ncz] + z[-nrz, -1] + z[-nrz, -ncz]
facetcol <- cut(zfacet, nbcol)
persp3D(x, y, z,
      theta = 60, phi = 30,
        expand = 0.5)
</pre>
![6.jpg](https://i.loli.net/2021/05/04/DQzm8sMKtobgl9C.jpg)
<span style="font-family:楷体;font-size:19px;">　　组合得到了一个二维周期函数的部分图像！自然而然地，我们可以由一维推广到二维：一个周期性的二维函数，其可以分解为多个二维正弦信号的加和形式。</span>

<span style="font-family:楷体;font-size:19px;">　　这里还有一点需要注意的地方。我们知道对于二维的正弦信号</span>

$$f_i(t,z) = A\cdot sin(w_1t + w_2z+\phi)$$

<span style="font-family:楷体;font-size:19px;">；方向信息包含在$\frac{w_1}{w_2}$之中。但是有同样的方向并不一定代表有同样的频率。若考虑$x = w_1t + w_2z+\phi$，则在三维欧氏坐标系$xzt$下，这是一个平行于$x$轴的平面。这也是为什么二维正弦函数长得如上例所示的原因。而这个平面等高线的疏密程度，可以用来衡量在这个法向量方向上，正弦频率的大小。这个时候相似于一位情况。**也就是说，$\frac{w_1}{w_2}$仅决定二维正弦函数的传播方向，而具体的大小则决定传播的频率**。</span>

##  *二维离散傅里叶变换与图像*
<span style="font-family:楷体;font-size:19px;">　　二维图像是一个天然的二维（周期性）离散函数。以灰度图为例，把各个像素点的灰度认为是该位置的函数大小。那么二维图像就可以看作是一个等间距采样某函数的采样信号。是将一个二维冲击窜作用在一个二维函数的某个周期上得到的结果。</span>

<span style="font-family:楷体;font-size:19px;">　　有了这样的保障，我们就可以利用前述二维傅里叶变换的性质来讨论图像性质了。</span>

###  *为什么高频代表边界？*
<span style="font-family:楷体;font-size:19px;">　　这个问题可以这样子很直观地理解。看一下这个例子：</span>

$$ \left\{
\begin{aligned}
f_1(x) & = sin(0.5x + y) \\
f_2(x) & = sin(x + 2 y)
\end{aligned}
\right.$$

<pre>
f1 <- function(x, y) {
  u <- 0.5
  v <- 1
  sin(u*x + v*y)
}

f2 <- function(x, y) {
  u <- 1
  v <- 2
  sin(u*x + v*y)
}
</pre>

<pre>
x <- seq(-5,5,length = 200)
y <- x
z1 <- outer(x, y, f1)
nrz <- nrow(z1)
ncz <- ncol(z1)
jet.colors <- colorRampPalette(c("gray80", "gray10"))
nbcol <- 100
color <- jet.colors(nbcol)
zfacet <- z1[-1, -1] + z1[-1, -ncz] + z1[-nrz, -1] + z1[-nrz, -ncz]
facetcol <- cut(zfacet, nbcol)
persp(x, y, z1,
        theta = 60, phi = 30,
        expand = 0.5, col = color[facetcol])
</pre>

![7.jpg](https://i.loli.net/2021/05/04/kBAW8jVCdXHRsLG.jpg)


<pre>
z2 <- outer(x, y, f2)
nrz <- nrow(z2)
ncz <- ncol(z2)
jet.colors <- colorRampPalette(c("gray80", "gray10"))
nbcol <- 100
color <- jet.colors(nbcol)
zfacet <- z2[-1, -1] + z2[-1, -ncz] + z2[-nrz, -1] + z2[-nrz, -ncz]
facetcol <- cut(zfacet, nbcol)
persp(x, y, z2,
        theta = 60, phi = 30,
        expand = 0.5, col = color[facetcol])
</pre>

![8.jpg](https://i.loli.net/2021/05/04/dGYIDCV8aO3zZWu.jpg)

<span style="font-family:楷体;font-size:19px;">　　在这个例子中，频率越大的信号，它的变化速度就越快，在形状上就越“陡峭”；而频率小的信号在形状上较为“平缓”。</span>

<span style="font-family:楷体;font-size:19px;">　　边界意味着“突变”。而频率越大的信号，它从波谷跳跃到波峰或从波峰跌到波谷就越“迅速”，也就是它的斜率越大。因为边界意味着“突变”，高频信号恰好有“突变”的属性，所以可以得出结论，频域的高频信号往往对应图像的色泽边界信息。</span>

###  *为什么沿着频域的亮线方向，会有图像的边界变化*
<span style="font-family:楷体;font-size:19px;">　　这个问题也可以直观地理解。首先，写出二维离散傅里叶变换地表达形式如下：</span>

$$F(u, v) = \mathcal{F}\left\{f(t,z)\right\} = \sum_{t=0}^{M-1} \sum_{z=0}^{N-1}f(t,z)\cdot e^{-j\cdot 2\pi  (\frac{ut}{M}+\frac{vz}{N})}$$

<span style="font-family:楷体;font-size:19px;">　　首先，频域图中过中心点（因为已经做了中心平移操作）的所有直线，都可以写成$\frac{u}{v}=k$的形式。而值得注意的是，频域图中的两个坐标$u,v$，其实就代表二维正弦函数：</span>

$$f_i(t,z) = A\cdot sin(w_1t + w_2z+\phi)$$

<span style="font-family:楷体;font-size:19px;">中$w_1$和$w_2$的取值。</span>

<span style="font-family:楷体;font-size:19px;">　　在上文中已经讨论过，$\frac{w_1}{w_2}$表示信号的传播方向，而不同的$w_1，{w_2}$大小仅代表在该方向上的频率大小。因此，**频域图中过中心点的亮线，表示在该方向有着一连串不同频率的正弦信号的组合**。体现在几何意义上，因为该方向有“浪花”一样的信号的传播（像之前的例子那样），因此，这也代表相较于其他方向，在这个方向上有着较为明显的数值波动。也就是有灰度值的明显变动，即存在边界。</span>
