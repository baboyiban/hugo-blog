---
title: 기호 모음
description: 기호 모음
---

```latex
$$
... % 여러줄
$$
$...$ % 한 줄
```

```latex
$x \notin A$ % 한 줄
```

$x \notin A$

```latex
$\therefore$ % 결론적으로
```

$\therefore$

```latex
$\because$ % 왜냐하면
```

$\because$

```latex
$\quad$ % 공백
```

$\quad$

```latex
$\left{ \cdots \right}$ % 중괄호
$\left[ \cdots \right]$ % 대괄호
$\left\vert \cdots \right\vert$ % 바, \vert, \mid, \lvert, \rvert
```

$\left\{ \cdots \right\}$
$\left[ \cdots \right]$
$\left\vert \cdots \right\vert$

```latex
$\cdots$ % 수평 ...
$\vdots$ % 수직 ...
```

$\cdots$
$\vdots$

```latex
% 여러 줄 수식 정렬
$$
\begin{array}{l} % 왼쪽 정렬
\end{array}
$$
$$
\begin{array}{r} % 오른쪽 정렬
\end{array}
$$
$$
\begin{array}{c} % 중앙 정렬
\end{array}
$$
$$
\begin{aligned} % 오른쪽 정렬, 기호 부등호 정렬 시 유용
\end{aligned}
$$
```

```latex
% 사용 예
$$
\left\{
\begin{array}{l}
a_{11} X_{1} + a_{12} X_{2} + \cdots + a_{1n} X_{n} = b_{1} \\
a_{21} X_{1} + a_{22} X_{2} + \cdots + a_{2n} X_{n} = b_{2} \\
\vdots \\
a_{n1} X_{1} + a_{n2} X_{2} + \cdots + a_{nn} X_{n} = b_{n}
\end{array}
\right\}
$$
```

$$
\left\{
\begin{array}{c}
a_{11} X_{1} + a_{12} X_{2} + \cdots + a_{1n} X_{n} = b_{1} \\
a_{21} X_{1} + a_{22} X_{2} + \cdots + a_{2n} X_{n} = b_{2} \\
\vdots \\
a_{n1} X_{1} + a_{n2} X_{2} + \cdots + a_{nn} X_{n} = b_{n}
\end{array}
\right\}
$$

```latex
% 사용 예
$$
x_{j} =
\left\vert
\begin{array}{c}
a_{11} \quad a_{12} \quad \cdots \quad a_{1j-1} \quad b_{1} \quad a_{1j+1} \cdots \quad a_{1n} \\
a_{21} \quad a_{22} \quad \cdots \quad a_{2j-1} \quad b_{2} \quad a_{2j+1} \cdots \quad a_{2n} \\
\vdots \\
a_{n1} \quad a_{n2} \quad \cdots \quad a_{nj-1} \quad b_{n} \quad a_{nj+1} \cdots \quad a_{nn} \\
\end{array}
\right\vert
$$
```

$$
x_{j} =
\left\vert
\begin{array}{c}
a_{11} \quad a_{12} \quad \cdots \quad a_{1j-1} \quad b_{1} \quad a_{1j+1} \cdots \quad a_{1n} \\
a_{21} \quad a_{22} \quad \cdots \quad a_{2j-1} \quad b_{2} \quad a_{2j+1} \cdots \quad a_{2n} \\
\vdots \\
a_{n1} \quad a_{n2} \quad \cdots \quad a_{nj-1} \quad b_{n} \quad a_{nj+1} \cdots \quad a_{nn} \\
\end{array}
\right\vert
(단, j = 1,2, \cdots, n)
$$

```latex
$\epsilon$
```

$\epsilon$

```latex
$|\alpha - \alpha'|$
```

$|\alpha - \alpha'|$

```latex
$\frac{a}{b}$
```

$\frac{a}{b}$

서로 다른 두 값 $\alpha, \alpha'$이 수열 $\{a_n\}$의 극한값이라고 가정하자. 그렇다면 임의의 양수 $\epsilon$에 대하여 $|\alpha - \alpha'| > \epsilon$이고 극한값의 정의에 따라 $n > N_1$에 대해 $|a_n - \alpha| < \frac{\epsilon}{2}$을 만족하는 $N_1$이 존재하고, $n > N_2$에 대해 $|a_n - \alpha'| < \frac{\epsilon}{2}$을 만족하는 $N_2$가 존재한다.
그렇다면 $n > max\{N1, N2\}$에 대해 $|\alpha - \alpha'| \leq |\alpha - \alpha_n| + |a_n - \alpha'| < \epsilon$이 되어 $|\alpha - \alpha'| > \epsilon$과 모순이다. 따라서 수렴하는 수열의 극한값은 유일하다.

$\to$ $\rightarrow$