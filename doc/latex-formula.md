# 基本公式

LATEX公式行内使用两个`$`，例如

`$f(x)=x$`可以生成 $f(x)=x$ 

行内公式有多种写法，例如

```
\begin{equation}

f(x)=ax+b

\end{equation}
```


$$
\begin{equation}

f(x)=ax+b

\end{equation}
$$

显然，LATEX公式会忽略换行和空格，如果需要换行，则使用`\\`来表示，而一个空格则使用`\ `表示，注意这个反斜杠后面需要跟一个空格符号

在MarkDown中，使用两个`$$`表示行间公式，可以忽略`\begin`命令，例如上面的例子

```latex
$$
f(x)=ax+b
$$
```

可以用这个表示

# 多项式的表示方法

多项式的表示方法，需要使用上下标等格式，例如

```
y={a}_{1}{x}_{1}^{3}+{a}_{2}{x}_{2}^{2}+{a}_{3}{x}_{3}+{a}_{4}
```

$$
y={a}_{1}{x}_{1}^{3}+{a}_{2}{x}_{2}^{2}+{a}_{3}{x}_{3}+{a}_{4}
$$

`_`用于表示下标，`^`用于表示上标，左右两边`{}`内的字符分别会显示在相应的位置，如果字符只有一个的话，可以不使用`{}`



# 分式和根号的表示

分式使用`\frac`命令表示，`\`表示转义，可以打印特殊符号（如`_`和`^`这些有特殊含义的符号）或不能直接打出来的符号（如$\ge$这种键盘上没有的）

```latex
f(x)=\frac{1}{x}
```

$$
f(x)=\frac{1}{x}
$$

根式跟分式格式差不多，使用`\sqrt`表示

```latex
f(x)=\sqrt{x^2+1}
```

$$
f(x)=\sqrt{x^2+1}
$$

如果想表示多次根式，可以使用

```latex
f(x)=\sqrt[3]{x^2+1}
```

$$
f(x)=\sqrt[3]{x^2+1}
$$

# 关系式

因为关系式只需要一个符号表示，比较简单，所以一般是直接使用转义，例如大于等于用`\ge`表示



# 带上下限的符号

求和或积分经常是有上下限的，

```latex
y=\sum_{n=1}^{\infty}\frac{1}{n}
```

$$
y=\sum_{n=1}^{\infty}\frac{1}{n}
$$

```
y=\int_{1}^{\infty}\frac{1}{x^2}\mathrm{d}x
```

$$
y=\int_{1}^{\infty}\frac{1}{x^2}\mathrm{d}x
$$

可以自定义算符，`\DeclareMathOperator*`表示定义带上下限的自定义符号，没有`*`就是不带

```
\begin{equation}
\DeclareMathOperator*{\what}{P}
y=\what_{x=1}^nx
\end{equation}
```

$$
\begin{equation}
\DeclareMathOperator*{\what}{P}
y=\what_{x=1}^nx
\end{equation}
$$

有时候上下限可能不在符号的正上下方或者斜上下方，可以使用`\limits`或`\nolimits`控制

例如，行内公式`\sum_{n=1}^{100}`显示为$\sum_{n=1}^{100}n$

用`\limits`强制让他在正上下方，`\sum\limits_{n=1}^{100}n`显示为$\sum\limits_{n=1}^{100}n$

# 矩阵

```
\begin{matrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
7 & 8 & 9
\end{matrix}
```

$$
\begin{matrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
7 & 8 & 9
\end{matrix}
$$

`&`用于分隔各个数字，如果想带方括号的矩阵，就使用`{bmatrix}`

```
\begin{bmatrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
7 & 8 & 9
\end{bmatrix}
```

$$
\begin{bmatrix}
1 & 2 & 3 \\
4 & 5 & 6 \\
7 & 8 & 9
\end{bmatrix}
$$

还有不同的括号类型

- `{Bmatrix}` $\begin{Bmatrix} 1 &2 \\ 3 & 4 \end{Bmatrix}$

- `{pmatrix}`  $\begin{pmatrix} 1 &2 \\ 3 & 4 \end{pmatrix}$

- `{vmatrix}` $\begin{vmatrix} 1 &2 \\ 3 & 4 \end{vmatrix}$

- `{Vmatrix}` $\begin{Vmatrix} 1 &2 \\ 3 & 4 \end{Vmatrix}$

# 选择符号

可以使用矩阵和括号的形式来描述

```
|x|=
\left\{ 
\begin{matrix}
x & \text{if } x\ge 0 \\
-x & \text{others}
\end{matrix}
\right.
```


$$
|x|=
\left\{ 
\begin{matrix}
x & \text{if } x\ge 0 \\
-x & \text{others}
\end{matrix}
\right.
$$
`\left\{`  `\right.`可以使他们之间的内容被括起来，`\right.`表示不显示内容

另一种方式是采用`{cases}`


$$
|x| = 
\begin{cases}
x & \text{if }x \ge 0 \\
-x & \text{others}
\end{cases}
$$

# 多行公式

`{multline}`可以用于书写多行公式，如

```latex
\begin{multline}
a+b+c+d+e+f=\\
1+2+3+4+5+6=\\
21
\end{multline}
```

$$
\begin{multline}
a+b+c+d+e+f=\\
1+2+3+4+5+6=\\
21
\end{multline}
$$

如果需要以某个地方对齐，可以使用`{align}`，通过`&`符号来判定对齐哪个符号

```latex
\begin{align}
a+b+c+d+e+f & = \\
1+2+3+4+5+6 & = 21\\
\end{align}
```

$$
\begin{align}
a+b+c+d+e+f & = \\
1+2+3+4+5+6 & = 21\\
\end{align}
$$

上述例子就是对齐 `=` 

如果不想对齐某个符号，只是想列举各个公式，可以使用`{gather}`

```latex
\begin{gather}
a^2+b^2=c^2 \\
1+2+3+4=10 \\
\Delta=b^2-4ac
\end{gather}
```

$$
\begin{gather}
a^2+b^2=c^2 \\
1+2+3+4=10 \\
\Delta=b^2-4ac
\end{gather}
$$

# 公式编号

例如`{align*}`，带有`*`的都表示不带编号，不带`*`如果不想使用编号，可以使用`\notag`

