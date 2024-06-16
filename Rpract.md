---
title: "R practice for stat test"
author: "ZHAI XINGYU"
date: "2024-06-16"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_depth: 3
    number_section: true
    latex_engine: xelatex
editor_options:
  markdown:
    wrap: 72
header-includes:
  - \usepackage{amsmath}
---



# ノンパラメトリック検定

## 符号検定

中央値がある値m_0に等しいかどうかを検定すること.

$H_0: m = m_0$
$H_1: m > m_0 \space (片側検定) \space or \space m \neq m_0 \space(両側検定)$

母集団から抽出した標本：$X_1, \dots, X_n$

検定統計量T：$T=\sum{I(X_i > m_0)}, \space T \sim Bin(n, \frac{1}{2})$


``` r
> # n=7
> # the pmf plot
> dbinom(0:7,7,0.5)
FALSE [1] 0.0078125 0.0546875 0.1640625 0.2734375 0.2734375 0.1640625 0.0546875
FALSE [8] 0.0078125
```

``` r
> plot(0:7, dbinom(0:7,7,0.5),ylab = "prob", xlab="T")
```

![](Rpract_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

``` r
> # alpha=0.05, one side
> qbinom(0.05,7,0.5,lower.tail = F)
FALSE [1] 6
```

## 符号付き順位検定

検定統計量T：$T=\sum{i *I(X_i > 0)}, I \sim Ber(1/2)$
$E(iI)=i*1/2, V(iI)=i^2*1/4, E(T)=1/2\sum{i}=\frac{(1+n)n}{4}, v(T)=1/4 \sum{i^2}=\frac{n(n+1)(2n+1)}{6*4}$


``` r
> # n=7(sequence)
```



# マルコフ連鎖

## マルコフ連鎖


推移図:１つ前の状態が決まると，次の状態へ移る確率が決まる

-  初期分布/状態確率ベクトル

-  推移確率行列: どの時点でも，次のステップで状態iから状態jに移る確率$q_{ij}$は一定(斉時的),
このような$q_{ij}$を(i，j)成分にもつような行列が推移確率行列Qです。


$状態確率ベクトル=(初期分布)[推移確率行列]^n$

-  推移確率行列が対角化可能な行列であれば，固有値でQのn乗を手計算でも求めることができる

-  状態空間:とりうる状態の全体, $\{1,2,3, \dots, N\}$


<block>


``` r
# create a dataframe
df1 <- data.frame(
  時点 = c(0,1,2,3,4,'...', 'n', 'n+1'),
  観測状態 = c(1,2,1,1,2,'...', 'i', 'j'),
  確率変数列 = c('X0=1', 'X1=2', 'X2=1', 'X3=1', 'X4=2', '...', 'Xn=i', 'Xn+1=j')
)

kable(df1, caption = 'マルコフ連鎖')
```



Table: マルコフ連鎖

|時点 |観測状態 |確率変数列 |
|:----|:--------|:----------|
|0    |1        |X0=1       |
|1    |2        |X1=2       |
|2    |1        |X2=1       |
|3    |1        |X3=1       |
|4    |2        |X4=2       |
|...  |...      |...        |
|n    |i        |Xn=i       |
|n+1  |j        |Xn+1=j     |

**NOTE:１つ前の状態が決まると，次の状態へ移る確率が決まる**

$$
P(X_4 = 2 | X_3 =2, X_2 = 1, X_1 = 2, X_0 = 1) = P(X_4 = 2 | X_3 =2) 
\rightarrow
１つ前の時点に関する条件だけで表せる
$$
**次の条件を満たすような確率変数列を離散時間のマルコフ連鎖と言います。**

${X_0, X_1, \dots, X_i}$

$$
P(X_{n+1} = j | X_{n} =i, X_{n-1} = i_{n-1}, X_{n-2} = i_{n-2}, \dots,  X_0 = i_0) = P(X_{n+1} = j | X_{n} =i)
$$
**斉時的: 条件付き確率が時点に依存しない**

$$
P(X_{n+1} = j | X_{n} =i) = q_{ij}
$$


**全確率の公式:時点nで1からNのどの状態にあったのか**

$$
P(X_{n+1} = j)= \sum_{1}^{N} {P(X_{n+1}=j|X_{n}=i)P(X_n=i)}
$$


略記

$$
P(X_n=i)=p_n(i)
$$
全確率の公式を次のように書き直すことができます。

$$
P(X_{n+1} = j)= \sum_{1}^{N} p_n(i)q_{ij}
$$


jは1からNまでの自然数のいずれかなので，そのすべてを列挙すると，次のように表せます。

$$
(p_{n+1}(1), p_{n+1}(2), \dots, p_{n+1}(j), \dots,  p_{n+1}(N))
=
(p_{n}(1), p_{n}(2), \dots, p_{n}(i), \dots,  p_{n}(N))
\begin{bmatrix}
q_{11} & q_{12} & \dots  & q_{1j} & \dots  & q_{1N}\\
q_{21} & q_{22} & \dots  & q_{2j} & \dots  & q_{2N}\\
\vdots & \vdots & \ddots & \vdots & \ddots & \vdots\\
q_{i1} & q_{i2} & \dots  & q_{ij} & \dots  & q_{iN}\\
\vdots & \vdots & \ddots & \vdots & \ddots & \vdots\\
q_{N1} & q_{N2} & \dots  & q_{Nj} & \dots  & q_{NN}\\
\end{bmatrix}
$$
</block>





## 定常分布

nが十分に大きければ，
n日後の状態確率ベクトルと(n＋１)日後の状態確率ベクトルはほぼ等しくなるので，
最終的な*状態確率ベクトル*をπとして，次の式が成り立つことになりそうです。

$\pi = \pi A$

このようなπを*定常分布*と言います。

*推移確率行列A*をかけても変化しないような*状態確率ベクトル$\pi$*です。


<block>
推移確率行列A

$$
A=
\begin{bmatrix}
a_1 & a_2 \\
b_1 & b_2
\end{bmatrix}
$$
定常分布$\pi = (a,b)$を求めます

$$
(a,b)
=
(a,b)
\begin{bmatrix}
a_{1} & a_{2} \\
b_{1} & b_{2}
\end{bmatrix}
$$
</block>


## 定常分布の存在と一意性

既約: すべての状態の間で有限回の移動で移り合うことができるマルコフ連鎖

一般に，既約な有限マルコフ連鎖は*ただ１つの定常分布をもつ*ことが知られています。
