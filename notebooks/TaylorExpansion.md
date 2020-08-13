---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.5.2
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

# Introduction

## Philosophy of the course
Numerous scientific problems can only be addressed through modeling and computer analysis. This stems from the fact that a lot of these problems are complex and the corresponding models cannot be solved using only pen-and-paper. 

Many models appearing in engineering or physical applications are mathematically described by partial differential equations (PDE) and the aim of this course is to provide a practical introduction to the relevant tools to solve them. By practical we mean that we will not develop the theory behind PDEs and also avoid complex mathematical derivations of the numerical methods we describe below. Although these are beautiful subjects, they are often characterized by a steep learning curve and our objective is to get our hands on solving real problems as quickly as possible. In some places we nevertheless provide some mathematical background about the techniques we describe because they cannot be properly understood otherwise. In each case, we try to keep things simple and written in such a way that the corresponding section may be skipped at first reading.

## Outline
To be written

## Python and why python

All the pieces of code written in this course are written in Python. However, we try to make the required prior knowledge of Python as little as possible and the reader is only expected to have a basic knowledge of any programming language and be familiar with concepts like variables, loops, conditional statements, functions etc.

# Approximations and Taylor expansion

In general, the resolution of numerical problems require some approximations.

The first one is related to the fact that most real numbers need an infinite numbers of digits after the decimal point to be properly represented. To store these numbers in the memory of a computer one therefore needs to cut their representation at some point beyond the decimal point. The number of digits kept is called the precision of the representation. For example, in *single precision* and *double precision*, $\pi$ is given by the following approximations:

\begin{align}
\pi &= 3.1415927 \quad\quad &\text{(single precision)}\\
\pi &= 3.141592653589793 \quad\quad &\text{(double precision)}\\
\end{align}

In this course, we always use double precision for real numbers as this is the default precision used by Python. Such a precision is large enough for the kind of numerical problems we consider, but the reader should still be aware that rounding off errors can cause some difficulties as they can get amplified when certain operations are performed or when some iterative procedures are used. Two good references to get started on the subject are:

- https://docs.python.org/3/tutorial/floatingpoint.html
- https://floating-point-gui.de

In the context of the numerical discretisation of ordinary or partial diffential equations, the more significant limitation in precision usually comes from the limited computer resources available to solve a problem or the time needed to get the solution. Indeed, from the physical point of view, both time and space are continuous variables...

## Taylor's theorem

In order to estimate the accuracy of discretized differential operators or time integration schemes, Taylor's theorem provides a valuable tool. Let $x$ be any point in the interval $[a\ b]$ and $\Delta x$ a small positive real number. Schematically we have:

<img src="figures/taylor.png">

Any *well behaved* function in $[a\ b]$ can then be approximated using the following expression (Taylor's expansion) $\cite{Arfken}$:

\begin{align}
f(x+\Delta x)=f(x)+f'(x)\Delta x+\frac{f''(x)}{2!}\Delta x^2+\dots + \frac{f^{(k)}(x)}{k!}\Delta x^k + R_{k+1}
\label{eq:taylorExpansion}
\end{align}

with,

\begin{align}
R_{k+1} = \frac{f^{k+1}(\xi)}{(k+1)!}\Delta x^{k+1}, \quad\quad x\leq \xi \leq x+\Delta x
\end{align}

In the above formula, $f^{(k)}$ denotes the $k$-th derivative of $f$. Note that at this stage, no approximations have been made. Assuming that $\vert f^{(k)}\vert$ is bounded by a constant $C$ in $[a\ b]$, we can then write:

\begin{align}
\vert R_{k+1}\vert \leq \frac{C}{(k+1)!}\Delta x^{k+1}\label{eq:remainder}
\end{align}

Equation $\ref{eq:remainder}$ gives a bound on the error made by dropping terms beyond $\frac{f^{(k)}(x)}{k!}\Delta x^k$ in $\ref{eq:taylorExpansion}$. One can then write $R_{k+1}$ using the so-called big O notation:

\begin{align}
R_{k+1} = O(\Delta x^{k+1})
\end{align}

The above notation means that $\vert R_{k+1}\vert$ goes to $0$ at least as fast as $\Delta x^{k+1}$ for $\Delta x \rightarrow 0$. We then say that the approximation,

\begin{align}
f(x+\Delta x)\approx f(x)+f'(x)\Delta x+\frac{f''(x)}{2!}\Delta x^2+\dots + \frac{f^{(k)}(x)}{k!}\Delta x^k
\end{align}

is of order $k+1$. Importantly, this implies that the remainder is at least reduced by a factor of $2^{k+1}$ if $\Delta x$ is divided by $2$. This is a very important concept that will be discussed numerous times in this course.

## Expansion of $e^x$

To make things more concrete and to write our first python code of the course, let us consider the Taylor expansion of the exponential function $e^x$ around $x=0$. According to \ref{eq:taylorExpansion}, one has:

\begin{align}
e^{\Delta x} = 1 + \Delta x + \frac{(\Delta x)^2}{2} + R_3,\quad\quad R_3=e^{\xi} \frac{(\Delta x)^3}{3!}, \quad\quad 0\leq \xi \leq \Delta x.
\end{align}

As $e^x$ is monotonously inscreasing, we certainly can bound $e^{\xi}$ by $e$ when $\Delta x \rightarrow 0$. Therefore, $\vert R_3 \vert \leq e \frac{(\Delta x)^3}{3!} = O(\Delta x)^3$. Let's check using python that this is indeed the case.

We first import two packages that... 
```python
import numpy as np

import matplotlib.pyplot as plt
%matplotlib inline
plt.style.use('./mainstyle.use')

delta_list = [2**(-k) for k in range(1, 10)]

delta = np.asarray(delta_list)
print(delta)
```

```python
R3 = np.exp(delta) - (1 + delta + delta**2 / 2)
slope = delta**3
```

```python
fig, ax = plt.subplots()
ax.loglog(delta, R3, '*', label=r'$R_3$')
ax.loglog(delta, slope, color='green', label=r'$\Delta^{-3}$')
ax.set_xlabel(r'$\Delta x$')
ax.set_ylabel(r'$R_3$')
ax.set_title(r'Accuracy')
ax.legend()
fig.savefig('sample.png', dpi=300)
```