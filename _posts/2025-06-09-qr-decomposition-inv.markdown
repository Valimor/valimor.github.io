---
layout: post
title:  "A Novel Algorithm For Matrix Inversion"
date:   2025-06-09 12:51:16 -0500
categories:
---
# Introduction

Matrix inverses are strange to compute.

In the simplest case, a matrix will be 1x1 and will be referred to as a scalar. The inverse of $$A$$ is denoted $$A^{-1}$$, and in this instance would simpy be $$\frac{1}{A}$$. For instance, supposed that A was simply the value $$5$$. Well, then its inverse would be the value $$\frac{1}{5}$$. Not very complex.

As our matrix $$A$$ increases in size the problem gets more complex. Suppose now that $$A$$ is no longer a 1x1 matrix, but instead 2x2. In that case, one can write $$A$$ as follows:

$$A = \begin{bmatrix} 
    a & b \\ 
    c & d \\ 
    \end{bmatrix}$$

In this specific case, $$A^{-1}$$ has a nice closed-form solution:

$$A^{-1} = \frac{1}{ad - bc} \begin{bmatrix} 
    d & -b \\ 
    -c & a \\ 
    \end{bmatrix}$$

However, as the matrices get larger and larger, these closed-form solutions become far more complex. To account for this, various algorithms to compute the inverse of a matrix have been proposed, such as [Gauss-Jordan elimination](https://en.wikipedia.org/wiki/Gaussian_elimination) and the [adjoint method](https://en.wikipedia.org/wiki/Adjugate_matrix). In this post, I will propose a novel algorithm based on geometric series.

# Geometric Series

A geometric series is a specific class of infinite series of the form 
$$a\sum_{k=0}^\infty r^{k}$$, where $$r$$ is restricted to be a scalar between -1 and 1. In this instance, the sum reduces nicely to the following formula.

$$a\sum_{k=0}^{\infty} r^k = \frac{a}{1-r}$$

Notably, this formula provides us with a means of computing the inverse of $$1-r$$ only by computing positive integer powers of $$r$$, albeit at the cost of having to add up an infinite number of said powers. One neat result is that this series also applies to matrices, and can be used in calculating the inverse of a matrix. Additionally, not only will the series converge to something that can be converted into the inverse of any desired matrix, but it will do so in a finite number of terms.

# Naive algorithm

My initial idea for the algorithm followed these steps:
1. Given the matrix $$A$$ that one wants to invert, compute a corresponding matrix $$B = I - A$$. $$I$$ is the identity matrix, and is the linear algebra equivalent of the scalar quantity $$1$$.
2. Compute the sum $$\sum_{k=0}^{l}B^k$$ for some arbitrarily large value $$l$$. This value should approximate the inverse of $$I - B$$, and by construction approximates the inverse of $$A$$.

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

It is left as an exercise to the reader to show that successive powers of $$-9$$ do not converge to a sum of $$\frac{1}{10}$$, and therefore that there are some *minor* issues with the naive algorithm.

# Convergence and why it matters
The astute reader might notice that I specifically required the value of $$r$$ in our prevously-mentioned geometric series to be between $$-1$$ and $$1$$. This is because of the concept of *convergence*, which is where a sequence (or in this case, sequence of partial sums) converges to a particular value. It has a more technical definition, but this will suffice for the understanding of this algorithm. Regardless, this idea of convergence ends up being of incredible importance in the application of the geometric series in computing the matrix inverse. The previous section provides an example. We would like an algorithm that works on any invertible matrix, not just matrices that are of a very specific form.

Since we know that some matrices do not converge under this algorithm, a prudent line of questioning might be to ask what specifically are the conditions for convergence? For that, we have to turn to the core of linear algebra: eigenvalues.

# The spectral decomposition and convergence

One property of matrices is they can be decomposed into a product of multiple matrices with constrained properties. The constraints can be used to simplify problem solving, and are used in a variety of different contexts. Some help solve least-squares problems faster, some help solve linear systems faster, and the one that we will be looking here at decomposes a matrix into its eigenmodes.

The spectral decomposition, which also sometimes goes by the much less cool name of eigendecomposition, is a matrix decomposition that represents the matrix in terms of its eigenvalues and eigenvectors. It looks like this.

$$ A = P\Lambda P^{-1} $$

P is a matrix whose columns are all of the eigenvectors of A and $$\Lambda$$ is a diagonal matrix whose diagonal elements are the eigenvalues of $$A$$. 

If a matrix is diagonalizable, then a very nice property appears. For any function $$f(x)$$ that admits a taylor series, the following statement is true.

$$f(A) = \sum_{n=0}^\infty c_nA^n = \sum_{n=0}^\infty c_n(P\Lambda P^{-1})^n = \sum_{n=0}^\infty c_nP\Lambda^nP^{-1} = P\left(\sum_{n=0}^\infty c_n\Lambda^n\right)P^{-1}$$

Since a diagonal matrix raised to a power is equivalent to raising all of its elements to that same power, this reduces to a series of independent taylor polynomials evaluated along the diagonals of $$\Lambda$$, which in turn reduces to the original function being applied element-wise to $$\Lambda$$.

We use this result to return to our question of convergence. If the scalar case required $$r$$ to be strictly between 1 and -1 for convergence, then we can trivially generalize to a matrix case by requiring the eigenvalues of our matrix $$B$$ to be between -1 and 1. However, what does this imply about the eigenvalues of $$A$$, which is the matrix we ultimately want to invert?

It is left an an exercise to the reader to show from the defintion of eigenvalues that the addition of any diagonal matrix to another matrix will shift the eigenvalues of the original matrix by exactly the values contained in the diagonal matrix.

# QR Decomposition

We have meandered pretty far, so let's recap what we have discovered so far.

1. Matrix inverses are hard to compute, so we want to come up with a way to find them.
2. The geometric series provides a nice way to invert a scalar quantity, provided it is between certain values.
3. Some matrices can have their inverses approximated arbitrarily well by constructing a different version of them to be passed through a geometric series.
4. The strong majority of the matrices that one would try to put through a geometric series would diverge, giving a result that is not the inverse that we want to compute.
5. A geometric series of matrices will converge as long as all of the eigenvalues are between -1 and 1.
6. Due to the rescaling and shifting applied to $$A$$ when calculating $$B = I - A$$, the eigenvalues of $$A$$ need to be between 0 and 2 
   1. Note: this is one of the results derived from the exercise to the reader at the end of the last section. If you don't quite understand it, don't worry about it for now.

From this list, it seems we are left with two pretty big questions:
1. How can we construct a modified version of ay arbitrary matrix $$A$$ that fulfills the criteria for convergence?
2. How can we recover the inverse of the original matrix $$A$$ from the inverse of the modified matrix?

To do this, let us once again return to the idea of a matrix decomposition. Specifically, we are going to make use of the QR decomposition, but then add an extra step. The hope with this is that we will construct a "preprocessed" version of $$A$$ whose inverse we can compute with a geometric series, then we will "undo" the processing to get the inverse of the original $$A$$ matrix. In practice, these "processing" steps are called transformations, and this seeming hack is actually a crucial part of linear algebra.

In the QR decomposition, we decompose $$A$$ into the product of two matrices $$QR$$. In this case, $$Q$$ is an orthonormal matrix (think a rotation matrix) and $$R$$ is a triangular matrix. To compute the inverse of $$A$$ it is much cheaper to compute the inverse of $$R$$ then multiply it by the inverse of $$Q$$. As an added bonus, because $$Q$$ is orthonormal, $$Q^T=Q^{-1}$$, so we have already computed one of the required inverses.

Additionally, from the definition of eigenvalues it is fairly trivial to show that all of the diagonal elements of $$R$$ are the eigenvalues of $$A$$. To keep track of this, we will introduce another matrix that we will call $$\Lambda^{-1}$$. $$\Lambda^{-1}$$ is a diagonal matrix and all of its elements are the reciprocals of the eigenvalues of $$R$$. When $$\Lambda^{-1}$$ is multiplied by $$R$$, the resulting matrix has eigenvalues of $$1$$. This has the unexpected effect of making $$I - A$$ a nilpotent matrix, which will bring this algorithm from an interesting showcase into a technique that can calculate matrix inverses exactly in finite time.

However, before we get into why nilpotence saves this algorithm, lets recap the steps we have come up with:
1. Compute the QR decomposition for a matrix $$A$$
2. Construct the matrix $$\Lambda^{-1}$$ from the reciprocals of the diagonal of $$R$$
3. Set $$B = I - \Lambda^{-1}R$$
4. Compute the geometric series $$S = \sum_{k=0}^{\infty}B^k$$
5. Transform $$S$$ back to $$A^{-1}$$ through the equation $$A^{-1} = S\Lambda^{-1} Q^T$$

Every step listed is computationally simple, with the exception of step 4. However, even that is actually acheivable in finite time due to the $$B$$ matrix being *nilpotent*.

# Nilpotence: the strange property that saves this algorithm

A *nilpotent* matrix is a non-zero matrix $$M$$ where there exists some integer $$k$$ such that $$M^k$$ is the zero matrix. It can be equivalently defined by saying that all of its eigenvalues are zero.  Through linear algebra properties that are beyond the scope of this article, it can be shown that if $$M$$ is an $$n\times n$$ nilpotent matrix then $$M^n$$ is guaranteeed to be the zero matrix.

Let us go back to the geometric series we constructed in step 4. of our recap:

$$S = \sum_{k=0}^{\infty}B^k$$

Suppose $$S$$ is an $$n\times n$$ matrix. Then, let us split the sum into two parts: one where the power of $$B$$ is less than $$n$$ and one where the power is $$B$$ is greater than or equal to $$n$$.

$$S = \sum_{k=0}^{n - 1}B^k + \sum_{k=n}^{\infty}B^k$$

Because $$A$$ has eigenvalues that are the diagonals of $$R$$, it follows that the matrix $$\Lambda^{-1}R$$ defined in step 3 will have eigenvalues that are all 1. Furthermore, if the eigenvalues of that matrix are all 1, then $$B$$ will have eigenvalues that are all 0 by construction. This means that $$B$$ is nilpotent, so this splitting of the sum becomes incredibly relevant.

Since $$B^n=0$$ and anything times $$0$$ is still $$0$$, it follows that the rightmost grouping of terms converges to zero.

$$\sum_{k=n}^{\infty}B^k = 0$$

Therefore, evaluating the infinite series is equivalent to evaluating a finite matrix polynomial:

$$S = \sum_{k=0}^{n - 1}B^k$$

For those of you who have a background in discrete-time control theory, this is analogous to the idea of a deadbeat controller. For those of you who do not, I would highly recommend looking into deadbeat control. It's a fascinating result from control theory where discrete control can outperform continuous-time control.

# Theoretical Performance

The algorithm has two parts that are computationally intensive: the QR decomposition and the evaluation of the polynomial. Since QR decomposition is a field that has already been optimized, this section will focus on the time complexity of the evaluation of the polynomial.

# Actual Peformance

Below is a graph showing the performance of the geometric inverse algorithm vs. numpy's native matrix inverse algorithm.

![Geometric Inverse Vs. Numpy Inverse]({{ site.baseurl }}/images/geometric_inv_vs_numpy.png)

Code: 
```python
def geometric_inv(A):
  Q, R = np.linalg.qr(A)
  diag = np.zeros_like(R)
  for i in range(Q.shape[0]):
    val = R[i,i]
    if val == 0:
      print("Matrix inverse does not exist")
    diag[i,i] = 1/val

  A_preprocessed = diag @ R
  I = np.eye(*Q.shape)
  B = I - A_preprocessed

  sum = np.zeros_like(A)
  curr_term = I
  for i in range(A.shape[0]):
    sum += curr_term
    curr_term = curr_term @ B

  return sum @ diag @ Q.T
```

# Discussion

As evidenced by the performance comparison to numpy, this algorithm is not terribly efficient. However, it converges to the virtually the same value as numpy every time. Additionally, for small matrices with $$n\lesssim 130$$ it outperforms numpy's native inverse algorithm.

For me, the main point of surprise is that this algorithm is even functional. Upon first thought it felt like a novelty that would work in specific cases or through analytic continuation, much like computing the Laplace transform of $$e^{at}$$ for $$s < a$$. I am still surprised that by making use of the QR decomposition these matrices can be transformed into a form that plays nicely with inversion.

The main result that was shocking to me was the nilpotence of the transformed matrix. I originally anticipated this to be an interesting showcase that could approximate the inverse of some matrices, but would never converge to the real value. After all, even an approximation can be interesting. It was only once I started digging into the weeds of convergence that I realized that the eigenvalues of the $$B$$ matrix could be turned into whatever I want through a QR-decomposition.

One important point of note is that even though this technically outperforms numpy it still lacks practicality. Computing matrix inverses is slow and generally not advisable when solving linear systems. Instead, other methods exist that can solve the system directly and generally provide much less roundoff error.

# Future work

The actual novelty of the proposed algorithm here is not contingent on using the QR decomposition. Instead, what was truly discovered was a means of raising a triangular matrix to an arbitrary power, which is further discussed in [this post.](/2025/06/15/computing-triangular-powers.html)