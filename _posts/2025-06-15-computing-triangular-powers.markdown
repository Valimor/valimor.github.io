---
layout: post
title:  "Efficient Computation of Powers of Triangular Matrices"
date:   2025-06-15
categories:
---
# Generalization with Hypergeometric Series

As I was working on this post I realized that what I had  found was a specific case of a more general algorithm. The traditional geometric series generalizes to the *hypergeometric* series, a mathematical construct famously used by Gauss.

$$_2F_1(a,b;c;z) := \sum_{n=0}^{\infty}\frac{(a)_n(b)_n}{(c)_n}\frac{z^n}{n!}$$

In this case $$(q)_n$$ is the *rising Pochammer symbol*, which is defined as follows:

$$
(q)_n = \begin{cases} 
      1 & n = 0 \\
      q(q+1)\cdots(q + n - 1) & n > 0\\
   \end{cases}
$$

This information and notation has been sourced from Wikipedia. Additionally, Wikipedia lists one very helpful relation that we will take advantage of.

$$_2F_1(a,b;b;z) = (1 - z)^{-a}$$

If one substitutes $$a = 1$$ into this formula then the geometric series is recovered. Going through the aforementioned process to generate our $$B$$ matrix, we can use this series to find $$A^-1$$. However, what if we were to use a different value of $$a$$ when computing the polynomial?