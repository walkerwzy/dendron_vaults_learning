---
id: ad1cc9cd-6785-43c7-b606-36705fdcce93
title: Root
desc: '数学公式'
updated: 1626272282477
created: 1606933268302
---

###矩阵
手动添加括号的方式，写在矩阵外 [^1]

$$
 \left[
     \begin{matrix}
 1 & 2 & \cdots & 4 \\
 5 & 6 & \cdots & 8 \\
 \vdots & \vdots & \ddots & \vdots \\
 13 & 14 & \cdots & 16
 \end{matrix}
 \right] \tag{1}
$$

(奇怪，加了前面四4个$下面的6个矩阵才能显示，不然就报语法错误)
$$$$

自动添加括号的方式：
$$ \begin{matrix} 1 & 2 \\ 3 & 4 \\ \end{matrix} \tag 2 $$
$$ \begin{pmatrix} 1 & 2 \\ 3 & 4 \\ \end{pmatrix} \tag 3 $$
$$ \begin{bmatrix} 1 & 2 \\ 3 & 4 \\ \end{bmatrix} \tag{4} $$
$$ \begin{Bmatrix} 1 & 2 \\ 3 & 4 \\ \end{Bmatrix} \tag{5} $$
$$ \begin{vmatrix} 1 & 2 \\ 3 & 4 \\ \end{vmatrix} \tag{6} $$
$$ \begin{Vmatrix} 1 & 2 \\ 3 & 4 \\ \end{Vmatrix} \tag{7} $$

带分割符号的矩阵好像就用不着矩阵了，直接用array
$$
\left[
    \begin{array}{cc|c}
        1 & 2 & 3 \\
        4 & 5 & 6 \\
    \end{array}
\right]
$$

若想在一行内显示矩阵，
使用\bigl(\begin{smallmatrix} ... \end{smallmatrix}\bigr)。挺长的。
这是一个行中矩阵的示例 $\bigl(\begin{smallmatrix} a & b \\ c & d \end{smallmatrix}\bigr)$ 。

###上标、下标与组合
上标符号，符号：^，如: $x^4$
下标符号，符号：_，如：$x_1$
组合符号，符号：{}，如：${16}_{8}O{2+}_{2}$

###汉字、字体与格式
汉字形式，符号：\mbox{}，如：$V_{\mbox{初始}}$
字体控制，符号：\displaystyle，如：$\displaystyle \frac{x+y}{y+z}$
下划线符号，符号：\underline，如：$\underline{x+y}$
标签，符号\tag{数字}，如: \tag{11} 见矩阵示例
上大括号，符号：\overbrace{算式}，如：$\overbrace{a+b+c+d}^{2.0}$
下大括号，符号：\underbrace{算式}，如：$a+\underbrace{b+c}_{1.0}+d$
上位符号，符号：\stackrel{上位符号}{基位符号}，如：$\vec{x}\stackrel{\mathrm{def}}{=}{x_1,\dots,x_n}$

\stackrel已被废弃，用\underset或\overset

###占位符
两个quad空格，符号：\qquad，如：$x \qquad y$
quad空格，符号：\quad，如：$x \quad y$
大空格，符号\，如：$x \ y$
中空格，符号\:，如：$x : y$
小空格，符号\,，如：$x , y$
没有空格，符号``，如：$xy$
紧贴，符号\!，如：$x ! y$

###定界符与组合
括号，符号：（）\big(\big) \Big(\Big) \bigg(\bigg) \Bigg(\Bigg)，如：$（）\big(\big) \Big(\Big) \bigg(\bigg) \Bigg(\Bigg)$
中括号，符号：[]，如：$[x+y]$
大括号，符号：\{ \}，如：$\{x+y\}$
自适应括号，符号：\left \right，如：$\left(x\right)$，$\left(x{yz}\right)$
组合公式，符号：{上位公式 \choose 下位公式}，如：${n+1 \choose k}={n \choose k}+{n \choose k-1}$
组合公式，符号：{上位公式 \atop 下位公式}，如：$\sum_{k_0,k_1,\ldots>0 \atop k_0+k_1+\cdots=n}A_{k_0}A_{k_1}\cdots$

###四则运算
加法运算，符号：+，如：$x+y=z$
减法运算，符号：-，如：$x-y=z$
加减运算，符号：\pm，如：$x \pm y=z$
减甲运算，符号：\mp，如：$x \mp y=z$
乘法运算，符号：\times，如：$x \times y=z$
点乘运算，符号：\cdot，如：$x \cdot y=z$
星乘运算，符号：\ast，如：$x \ast y=z$
除法运算，符号：\div，如：$x \div y=z$
斜法运算，符号：/，如：$x/y=z$
分式表示，符号：\frac{分子}{分母}，如：$\frac{x+y}{y+z}$
分式表示，符号：{分子} \voer {分母}，如：${x+y} \over {y+z}$
绝对值表示，符号：||，如：$|x+y|$

###高级运算
平均数运算，符号：\overline{算式}，如：$\overline{xyz}$
开二次方运算，符号：\sqrt，如：$\sqrt x$
开方运算，符号：\sqrt[开方数]{被开方数}，如：$\sqrt[3]{x+y}$
对数运算，符号：\log，如：$\log(x)$
极限运算，符号：\lim，如：$\lim^{x \to \infty}_{y \to 0}{\frac{x}{y}}$
极限运算，符号：\displaystyle \lim，如：$\displaystyle \lim^{x \to \infty}_{y \to 0}{\frac{x}{y}}$
求和运算，符号：\sum，如：$\sum^{x \to \infty}_{y \to 0}{\frac{x}{y}}$
求和运算，符号：\displaystyle \sum，如：$\displaystyle \sum^{x \to \infty}_{y \to 0}{\frac{x}{y}}$
积分运算，符号：\int，如：$\int^{\infty}_{0}{xdx}$
积分运算，符号：\displaystyle \int，如：$\displaystyle \int^{\infty}_{0}{xdx}$
微分运算，符号：\partial，如：$\frac{\partial x}{\partial y}$
矩阵表示，见上

###逻辑运算
等于运算，符号：=，如：$x+y=z$
大于运算，符号：>，如：$x+y>z$
小于运算，符号：<，如：$x+y<z$
大于等于运算，符号：\geq，如：$x+y \geq z$
小于等于运算，符号：\leq，如：$x+y \leq z$
不等于运算，符号：\neq，如：$x+y \neq z$
不大于等于运算，符号：\ngeq，如：$x+y \ngeq z$
不大于等于运算，符号：\not\geq，如：$x+y \not\geq z$
不小于等于运算，符号：\nleq，如：$x+y \nleq z$
不小于等于运算，符号：\not\leq，如：$x+y \not\leq z$
约等于运算，符号：\approx，如：$x+y \approx z$
恒定等于运算，符号：\equiv，如：$x+y \equiv z$

###集合运算
属于运算，符号：\in，如：$x \in y$
不属于运算，符号：\notin，如：$x \notin y$
不属于运算，符号：\not\in，如：$x \not\in y$
子集运算，符号：\subset，如：$x \subset y$
子集运算，符号：\supset，如：$x \supset y$
真子集运算，符号：\subseteq，如：$x \subseteq y$
非真子集运算，符号：\subsetneq，如：$x \subsetneq y$
真子集运算，符号：\supseteq，如：$x \supseteq y$
非真子集运算，符号：\supsetneq，如：$x \supsetneq y$
非子集运算，符号：\not\subset，如：$x \not\subset y$
非子集运算，符号：\not\supset，如：$x \not\supset y$
并集运算，符号：\cup，如：$x \cup y$
交集运算，符号：\cap，如：$x \cap y$
差集运算，符号：\setminus，如：$x \setminus y$
同或运算，符号：\bigodot，如：$x \bigodot y$
同与运算，符号：\bigotimes，如：$x \bigotimes y$
实数集合，符号：\mathbb{R}，如：\mathbb{R}
自然数集合，符号：\mathbb{Z}，如：\mathbb{Z}
空集，符号：\emptyset，如：$\emptyset$

###数学符号
无穷，符号：\infty，如：$\infty$
虚数，符号：\imath，如：$\imath$
虚数，符号：\jmath，如：$\jmath$
数学符号，符号\hat{a}，如：$\hat{a}$
数学符号，符号\check{a}，如：$\check{a}$
数学符号，符号\breve{a}，如：$\breve{a}$
数学符号，符号\tilde{a}，如：$\tilde{a}$
数学符号，符号\bar{a}，如：$\bar{a}$
矢量符号，符号\vec{a}，如：$\vec{a}$
数学符号，符号\acute{a}，如：$\acute{a}$
数学符号，符号\grave{a}，如：$\grave{a}$
数学符号，符号\mathring{a}，如：$\mathring{a}$
一阶导数符号，符号\dot{a}，如：$\dot{a}$
二阶导数符号，符号\ddot{a}，如：$\ddot{a}$
上箭头，符号：\uparrow，如：$\uparrow$
上箭头，符号：\Uparrow，如：$\Uparrow$
下箭头，符号：\downarrow，如：$\downarrow$
下箭头，符号：\Downarrow，如：$\Downarrow$
左箭头，符号：\leftarrow，如：$\leftarrow$
左箭头，符号：\Leftarrow，如：$\Leftarrow$
右箭头，符号：\rightarrow，如：$\rightarrow$
右箭头，符号：\Rightarrow，如：$\Rightarrow$
底端对齐的省略号，符号：\ldots，如：$1,2,\ldots,n$
中线对齐的省略号，符号：\cdots，如：$x_1^2 + x_2^2 + \cdots + x_n^2$
竖直对齐的省略号，符号：\vdots，如：$\vdots$
斜对齐的省略号，符号：\ddots，如：$\ddots$

###希腊字母
|字母|实现|字母|实现|
|-|-|-|-|
|A|A|α|\alpha|
|B|B|β|\beta|
|Γ|\Gamma|γ|\gamma|
|Δ|\Delta|δ|\delta|
|E|E|ϵ|\epsilon|
|Z|Z|ζ|\zeta|
|H|H|η|\eta|
|Θ|\Theta|θ|\theta|
|I|I|ι|\iota|
|K|K|κ|\kappa|
|Λ|\Lambda|λ|\lambda|
|M|M|μ|\mu|
|N|N|ν|\nu|
|Ξ|\Xi|ξ|\xi|
|O|O|ο|\omicron|
|Π|\Pi|π|\pi|
|P|P|ρ|\rho|
|Σ|\Sigma|σ|\sigma|
|T|T|τ|\tau|
|Υ|\Upsilon|υ|\upsilon|
|Φ|\Phi|ϕ|\phi|
|X|X|χ|\chi|
|Ψ|\Psi|ψ|\psi|
|Ω|\v|ω|\omega|

### 示例

$ J_\alpha(x) = \sum_{m=0}^\infty \frac{(-1)^m}{m! \Gamma (m + \alpha + 1)} {\left({ \frac{x}{2} }\right)}^{2m + \alpha} \text {，行内公式示例} $

$ x^{y^z}=(1+{\rm e}^x)^{-2xy^w} $

\rm好像是改变了字体？是的，罗马体
a$\rm a$a A$\rm A$A

数学公式中常见的省略号有两种，\ldots 表示与 文本底线 对齐的省略号，\cdots 表示与 文本中线 对齐的省略号。
例子：
$$ 
f(x_1,x_2,\underbrace{\ldots}_{\rm ldots} ,x_n) = x_1^2 + x_2^2 + \underbrace{\cdots}_{\rm cdots} + x_n^2 
$$

$$
% 此处使用 0.5 倍行高作为边距，附加 2 像素的实线边框（Ctrl+Alt+Y 可见）
    \color{#f1c296}{e^x=\lim_{n\to\infty} \left( 1+\frac{x}{n} \right)^n \qquad (1)}
$$

$$
% 用\middle把分隔符|和||变高了
\left\langle  
    q \; \middle|
        \frac{\frac xy}{\frac uv}
    \middle\| p 
\right\rangle
$$

$$
% 演示方程组，并顺便演示适配行高
f(n) = 
    \begin{cases}
        \frac{n}{2}, & \text{if $n$ is even} \\[2ex]
        3n+1,        & \text{if $n$ is odd} \\
    \end{cases}
$$

$$
\begin{cases}
z & z < 0 \\
az & z \leq 0
\end{cases}
$$

$$
% r,c,l表示横向对齐方式
\begin{array}{ccc|cc}
 & \text{title} & & \ & \mathrm value \\
\hline
a & a & a & a & a \\
a & a & a & a & a \\
\hline
\end{array}
$$

左侧对齐的方程式是用array来拼出来的，而==没有原生的语法==，如begin{case}之类的
$$
    \left.
    % \left.是虚拟括号
        \begin{array}{l}
            \text{if $n$ is even:} & \frac{n}{2} \\[2ex]
            \text{if $n$ is odd:} & 3n+1 \\
        \end{array}
    \right\}
    =f(n)
$$

KaTex不支持align和align*,但支持aligned
$$
\begin{aligned}
    a=&\left(1+2+3+ \cdots \right. \\
      &\cdots+\left. \infty-2+\infty-1+\infty\right)
\end{aligned}
$$
gathered也可以聚合，但是好像是居中的
$$
\begin{gathered}
    a=\left(1+2+3+ \cdots \right. \\
      \cdots+\left. \infty-2+\infty-1+\infty\right)
\end{gathered}
$$


[^1]: Hi! This is a footnote
