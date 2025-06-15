---
layout: post
title:  "Efficient Computation of Powers of Triangular Matrices"
date:   2025-06-15
categories:
---

# Introduction

This post is a direct sequel to my previous post [A Novel Algorithm For Matrix Inversion](/2025/06/09/qr-decomposition-inv.html). I would highly recommend reading it before this one, as it will provide some of the motivation and background for the contents of this post.

# Generalization with Hypergeometric Series

As I was working on the previous post I realized that what I had  found was a specific case of a more general algorithm. The traditional geometric series generalizes to the *hypergeometric* series, a mathematical construct famously used by Gauss.

$$_2F_1(a,b;c;z) := \sum_{n=0}^{\infty}\frac{(a)_n(b)_n}{(c)_n}\frac{z^n}{n!}$$

In this case $$(q)_n$$ is the *rising Pochammer symbol*, which is defined as follows:

$$
(q)_n = \begin{cases} 
      1 & n = 0 \\
      q(q+1)\cdots(q + n - 1) & n > 0\\
   \end{cases}
$$

This information and notation has been sourced from Wikipedia. Additionally, Wikipedia lists one very helpful relation that we will take advantage of, which is known as the *generalized binomial theorem*.

$$_2F_1(a,b;b;z) = (1 - z)^{-a}$$

If one substitutes $$a = 1$$ into this formula then the geometric series is recovered. Going through the aforementioned process to generate our $$B$$ matrix, we can use this series to find $$A^{-1}$$. However, what if we were to use a different value of $$a$$ when computing the polynomial?

# Why the inverse is a special case

Unfortunately, the algorithm to compute the inverse of an arbitrary matrix does not generalize to any power of an arbitrary matrix. To start, let us observe one of the critical results we obtained last time.

$$ A = Q\Lambda R \implies A^{-1} = (Q\Lambda R)^{-1} = R^{-1}\Lambda^{-1}Q^{-1} = R^{-1}\Lambda^{-1}Q^T $$

We then used the special case of the hypergeometric series to computes $$R^{-1}$$. Since the other two matrices were computable in $$O(1)$$ and $$O(n)$$ time for $$Q^{T}$$ and $$\Lambda^{-1}$$ respectively, it follows that once we have $$R^{-1}$$ we can cheaply compute $$A^{-1}$$.

This does not generalize well for two reasons:
1. We would need to find a way to compute $$Q$$ raised to any arbitrary power.
2. Matrix multiplication is non-commutative.