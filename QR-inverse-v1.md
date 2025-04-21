# Introduction

Matrix inverses are strange to compute.

In the simplest case, a matrix will be 1x1 and will be referred to as a scalar. The inverse of $A$ is denoted $A^{-1}$, and in this instance would simpy be $\frac{1}{A}$. For instance, supposed that A was simply the value $5$. Well, then its inverse would be the value $\frac{1}{5}$. Not very complex.

As our matrix $A$ increases in size the problem gets more complex. Suppose now that $A$ is no longer a 1x1 matrix, but instead 2x2. In that case, one can write $A# as follows:

$$A = \begin{bmatrix} a & b \\ c & d \\ \end{bmatrix}$$

In this specific case, $A^{-1}$ has a nice closed-form solution:

$$A^{-1} = \frac{1}{ad - bc} \begin{bmatrix} d & -b \\ -c & a \\ \end{bmatrix}$$

However, as the matrices get larger and larger, these closed-form solutions become far more complex. To account for this, various algorithms to compute the inverse of a matrix have been proposed, such as Gauss-Jordan elimination and [insert other algorithm]. In this post, I will propose a novel algorithm based on geometric series.

# Geometric Series

A geometric series is a specific class of infinite series of the form 
$a\sum_{k=0}^\infty r^{k}$, where $r$ is restricted to be a scalar between -1 and 1. In this instance, the sum reduces nicely to the following formula.

$$a\sum_{k=0}^{\infty} r^k = \frac{a}{1-r}$$

Notably, this formula provides us with a means of computing the inverse of $1-r$ only by computing positive integer powers of $r$, albeit at the cost of having to add up an infinite number of said powers. The insight provided by this post is that this idea also applies to matrices. With a few modifications, not only will the series converge to the inverse of the desired matrix, but it will also be able to in a finite number of terms.

# Naive algorithm

My initial idea for the algorithm followed these steps:
1. Given the matrix $A$ that one wants to invert, compute a corresponding matris $B = I - A$. I is the identity matrix, and is the linear algebra equivalent of the scalar quantity $1$.
2. Compute the sum $\sum_{k=0}^{l}B^k$ for some arbitrarily large value $l$. This value should approximate the inverse of $I - B$, but by construction this is equal to te inverse of $A$.

This idea worked well for my first few matrices that I selected. However, when I tried to invert the matrix 

$$A = \begin{bmatrix} 
    10 & 0 \\
    0 & 10 \\ 
    \end{bmatrix} $$

I noticed a problem. Since it is a diagonal matrix, all it does is scale the matrix it multiplies (this will be important later). It does not take very much knowledge of linear algebra to correctly guess the inverse of this matrix.

$$A^{-1} = \begin{bmatrix} 
        \frac{1}{10} & 0 \\ 
        0 & \frac{1}{10} \\ 
        \end{bmatrix} $$

It is left as an exercise to the reader to show that successive powers of $-9$ do not sum to $\frac{1}{10}$, and therefore that there are some *minor* issues with the naive algorithm.

# Convergence and why it matters
The astute reader might notice that I specifically required the value of $r$ in our prevously-mentioned geometric series to be between $-1$ and $1$. This is because of the concept of *convergence*, which is where a sequence (or in this case, sequence of partial sums) converges to a particular value. It has a more technical definition, but this will suffice for the understanding of this algorithm. Regardless, this idea of convergence ends up being of incredible importance in the application of the geometric series in computing the matrix inverse. The previous section provides an example. We would like an algorithm that works on any invertible matrix, not just matrices that are of a very specific form.

Since we know that some matrices do not converge under this algorithm, a prudent line of questioning might be to ask what specifically are the conditions for convergence? For that, we have to turn to the core of linear algebra: eigenvalues.

# Eigenvalues and the QR-Decomposition