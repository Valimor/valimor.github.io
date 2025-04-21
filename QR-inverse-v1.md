# Introduction

Matrix inverses are strange to compute.

In the simplest case, a matrix will be 1x1 and will be referred to as a scalar. The inverse of $A$ is denoted $A^{-1}$, and in this instance would simpy be $\frac{1}{A}$. For instance, supposed that A was simply the value $5$. Well, then its inverse would be the value $\frac{1}{5}$. Not very complex.

As our matrix $A$ increases in size the problem gets more complex. Suppose now that $A$ is no longer a 1x1 matrix, but instead 2x2. In that case, one can write $A# as follows:

$$A = \begin{bmatrix} 
    a & b \\ 
    c & d \\ 
    \end{bmatrix}$$

In this specific case, $A^{-1}$ has a nice closed-form solution:

$$A^{-1} = \frac{1}{ad - bc} \begin{bmatrix} 
    d & -b \\ 
    -c & a \\ 
    \end{bmatrix}$$

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

# The spectral decomposition and convergence

One property of matrices is they can be decomposed into a product of multiple matrices with constrainted properties. One example of such a decomposition is an LU-decomposition, where the matrix $A$ can be expressed as $A=LU$, where $L$ is a lower-triangular matrix and $U$ is an upper-triangular matrix. In this case, the operations that one might want to perform on the matrix $A$ can be dramatically simplified via the properties of $L$ and $U$. However, here we want to look at a different decomposition.

The spectral decomposition, which also sometimes goes by the much less cool name of eigendecomposition, is a matrix decomposition that represents the matrix in terms of its eigenvalues and eigenvectors. It looks like this.

$$ A = P\Lambda P^{-1} $$

P is a matrix whose columns are all of the eigenvectors of A and $\Lambda$ is a diagonal matrix whose diagonal elements are the eigenvalues of $A$. 

If a matrix is diagonalizable, then a very nice property appears. For any function $f(x)$ that admits a taylor series, the following statement is true.

$$f(A) = \sum_{n=0}^\infty c_nA^n = \sum_{n=0}^\infty c_n(P\Lambda P^{-1})^n = \sum_{n=0}^\infty c_nP\Lambda^nP^{-1} = P\left(\sum_{n=0}^\infty c_n\Lambda^n\right)P^{-1}$$

Since a diagonal matrix raised to a power is equivalent to raising all of its elements to that same power, this reduces to a series of independent taylor polynomials evaluated along the diagonals of $\Lambda$, which in turn reduces to the original function being applied element-wise to $\Lambda$.

We use this result to return to our question of convergence. If the scalar case required $r$ to be strictly between 1 and -1 for convergence, then we can trivially generalize to a matrix case by requiring the eigenvalues of our matrix $B$ to be between -1 and 1. However, what does this imply about the eigenvalues of $A$, which is the matrix we ultimately want to invert?

It is left an an exercise to the reader to show from the defintion of eigenvalues that the addition of any diagonal matrix to another matrix will shift the eigenvalues of the original matrix by exactly the values contained in the diagonal matrix.

# QR Decomposition and the unexpected blessings of nilpotence

We have meandered pretty far, so let's recap what we have discovered so far.

1. Matrix inverses are hard to compute, so we want to come up with a way to find them.
2. The geometric series provides a nice way to invert a scalar quantity, provided it is between certain values.
3. Some matrices can have their inverses approximated arbitrarily well by constructing a different version of them to be passed through a geometric series.
4. The strong majority of the matrices that one would try to put through a geometric series would diverge, giving a result that is not the inverse that we want to compute.
5. A geometric series of matrices will converge as long as all of the eigenvalues are between -1 and 1.
6. Due to the rescaling and shifting applied to $A$ when calculating $B = I - A$, the eigenvalues of $A$ need to be between 0 and 2 
   1. Note: this is one of the results derived from the exercise to the reader at the end of the last section. If you don't quite understand it, don't worry about it for now.

From this list, it seems we are left with a pretty big question: how can we change the eigenvalues of $A$ without changing its inverse?

To do this, let us once again return to the idea of a matrix decomposition. Specifically, we are going to make use of the QR decomposition, but then add an extra step. The hope with this is that we will construct a "preprocessed" version of A whose inverse we can compute, then we will "undo" the processing to get the inverse of the original $A$ matrix. In practice, these "processing" steps are called transformations, and this seeming hack is actually a crucial part of linear algebra.

In the QR decomposition, we decompose $A$ into the product of two matrices $QR$. In this case, $Q$ is an orthonormal matrix (think a rotation matrix) and $R$ is a triangular matrix. To compute the inverse of $A$, it is much cheaper to compute the inverse of $R$, then multiply it by the inverse of $Q$. As an added bonus, because $Q$ is orthonormal, $Q^T=Q^{-1}$, so we have already computed one of the required inverses.

Additionally, from the definition of eigenvalues it is fairly trivial to show that all of the diagonal elements of $R$ are the eigenvalues of $A$. To keep track of this, we will introduce another matrix that we will call $\Lambda^{-1}$. $\Lambda^{-1}$ is a diagonal matrix, and all of its elements are the reciprocals of the eigenvalues of $R$. When $\Lambda^{-1}$ is multiplied by $R$, the resulting matrix has eigenvalues of $1$. This has the unexpected effect of making $I - A$ a nilpotent matrix, which will bring this algorithm from an interesting showcase into a technique that can calculate matrix inverses exactly in finite time.