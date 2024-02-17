author: mwsht, sshwy, ouuan, Ir1d, Henry-ZHR, hsfzLZH1

## 引入

悬线法的适用范围是单调栈的子集。具体来说，悬线法可以应用于满足以下条件的题目：

-   需要在扫描序列时维护单调的信息；
-   可以使用单调栈解决；
-   不需要在单调栈上二分。

看起来悬线法可以被替代，用处不大，但是悬线法概念比单调栈简单，更适合初学 OI 的选手理解并解决最大子矩阵等问题。

## 例题

???+ note "[SP1805 HISTOGRA - Largest Rectangle in a Histogram](https://www.luogu.com.cn/problem/SP1805)"
    大意：在一条水平线上有 $n$ 个宽为 $1$ 的矩形，求包含于这些矩形的最大子矩形面积。

悬线，就是一条竖线，这条竖线有初始位置和高度两个性质，可以在其上端点不超过当前位置的矩形高度的情况下左右移动。

对于一条悬线，我们在这条上端点不超过当前位置的矩形高度且不移出边界的前提下，将这条悬线左右移动，求出其最多能向左和向右扩展到何处，此时这条悬线扫过的面积就是包含这条悬线的尽可能大的矩形。容易发现，最大子矩形必定是包含一条初始位置为 $i$，高度为 $h_i$ 的悬线。枚举实现这个过程的时间复杂度为 $O(n ^ 2)$，但是我们可以用悬线法将其优化到 $O(n)$。

我们考虑如何快速找到悬线可以到达的最左边的位置。

### 过程

定义 $l_i$ 为当前找到的 $i$ 位置的悬线能扩展到的最左边的位置，容易得到 $l_i$ 初始为 $i$，我们需要进一步判断还能不能进一步往左扩展。

-   如果当前 $l_i = 1$，则已经扩展到了边界，不可以。
-   如果当前 $a_i > a_{l_i - 1}$，则从当前悬线扩展到的位置不能再往左扩展了。
-   如果当前 $a_i \le a_{l_i - 1}$，则从当前悬线还可以往左扩展，并且 $l_i - 1$ 位置的悬线能向左扩展到的位置，$i$ 位置的悬线一定也可以扩展到，于是我们将 $l_i$ 更新为 $l_{l_i - 1}$，并继续执行判断。

通过摊还分析，可以证明每个 $l_i$ 最多会被其他的 $l_j$ 遍历到一次，因此时间复杂度为 $O(n)$。

### 实现

??? 参考代码
    ```cpp
    --8<-- "docs/misc/code/hoverline/hoverline_1.cpp"
    ```

???+ note "[UVa1619 感觉不错 Feel Good](https://www.luogu.com.cn/problem/UVA1619)"
    对于一个长度为 $n$ 的数列，找出一个子区间，使子区间内的最小值与子区间长度的乘积最大，要求在满足舒适值最大的情况下最小化长度，最小化长度的情况下最小化左端点序号。

本题中我们可以考虑枚举最小值，将每个位置的数 $a_i$ 当作最小值，并考虑从 $i$ 向左右扩展，找到满足 $\min\limits _ {j = l} ^ r a_j = a_i$ 的尽可能向左右扩展的区间 $[l, r]$。这样本题就被转化成了悬线法模型。

??? 参考代码
    ```cpp
    --8<-- "docs/misc/code/hoverline/hoverline_2.cpp"
    ```

## 最大子矩形

???+ note "[P4147 玉蟾宫](https://www.luogu.com.cn/problem/P4147)"
    给定一个 $n \times m$ 的包含 `'F'` 和 `'R'` 的矩阵，求其面积最大的子矩阵的面积 $\times 3$，使得这个子矩阵中的每一位的值都为 `'F'`。

我们会发现本题的模型和第一题的模型很像。仔细分析，发现如果我们每次只考虑某一行的所有元素，将位置 $(x, y)$ 的元素尽可能向上扩展的距离作为该位置的悬线长度，那最大子矩阵一定是这些悬线向左右扩展得到的尽可能大的矩形中的一个。

??? 参考代码
    ```cpp
    --8<-- "docs/misc/code/hoverline/hoverline_3.cpp"
    ```

这里还有一种看似复杂的最大子矩形问题专用的悬线法实现方式。

设 $\textit{up}_{i,j}$ 表示位置 $(i,j)$ 向上的悬线长度，即 $(i,j)$ 向上最多走 $u_{i,j}$ 步就会到达第一个不可选的位置，可以得到转移方式为：

- 若 $(i,j)$ 不可选，悬线无法向上延伸，$\textit{up}_{i,j}=0$；
- 反之若可选，则可以继承位置 $(i-1,j)$ 的悬线长度并增加一格的长度，$\textit{up}_{i,j}=\textit{up}_{i-1,j}+1$。

同理，可以求出 $l_{i,j},r_{i,j}$ 分别表示位置 $(i,j)$ 最多向左，向右能走多少步到达第一个不可选的位置：

$$
l_{i,j}=
\begin{cases}
	l_{i,j-1}+1 & \text{if $(i,j)$ can be selected}\\
	0 & \text{otherwise}\\
\end{cases}\\
r_{i,j}=
\begin{cases}
	r_{i,j+1}+1 & \text{if $(i,j)$ can be selected}\\
	0 & \text{otherwise}\\
\end{cases}\\
$$

注意到 $l_{i,j},r_{i,j}$ 并非悬线能向左右扩展的距离，于是考虑通过 $l_{i,j},r_{i,j}$ 推出后者，即悬线能向左右扩展的距离分别为 $\textit{left}_{i,j},\textit{right}_{i,j}$。

先求 $\textit{left}_{i,j}$。

- 假设 $\textit{up}_{i,j}>1$，即 $(i-1,j)$ 可选，我们可以将悬线切成一上一下的两段，使悬线上半部分长度为 $\textit{up}_{i,j}-1=\textit{up}_{i-1,j}$（通过递推式移项可以得到），那么上半部分即为 $(i-1,j)$ 位置上的悬线，能扩展 $\textit{left}_{i-1,j}$ 格；下半部分长度为 $1$，刚好能扩展 $l_{i,j}$ 格。当整条悬线移动到一定位置时，必须满足上下两部分都能移动到该位置，于是取上下两部分答案的 $\min$。
- 当 $\textit{up}_{i,j}=1$ 时，悬线长度为 $1$，不用切悬线，$\textit{left}_{i,j}=l_{i,j}$。
- 当 $\textit{up}_{i,j}=0$ 时，怎么左右移动悬线，最终面积都为 $0$，可以舍去，赋值为 $0$ 即可。

转移方程：

$$
\textit{left}_{i,j}=
\begin{cases}
	\min(\textit{left}_{i-1,j},l_{i,j}) & \text{if $\textit{up}_{i,j}>1$}\\
	l_{i,j} & \text{if $\textit{up}_{i,j}=1$}\\
	0 & \text{if $\textit{up}_{i,j}=0$}\\
\end{cases}\\
$$

$\textit{right}_{i,j}$ 同理：

$$
\textit{right}_{i,j}=
\begin{cases}
	\min(\textit{right}_{i-1,j},r_{i,j}) & \text{if $\textit{up}_{i,j}>1$}\\
	r_{i,j} & \text{if $\textit{up}_{i,j}=1$}\\
	0 & \text{if $\textit{up}_{i,j}=0$}\\
\end{cases}\\
$$

根据上文，最终答案即为 $\max\{(\textit{right}_{i,j}+\textit{left}_{i,j}-1)\times \textit{up}_{i,j}\}$。

??? 参考代码
    ```cpp
    --8<-- "docs/misc/code/hoverline/hoverline_4.cpp"
    ```

## 习题

-   [P1169「ZJOI2007」棋盘制作](https://www.luogu.com.cn/problem/P1169)
