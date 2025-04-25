# A Deep Dive into Geometric Series

## Introduction

(If you already are familiar with the concept of a geometric series, feel free to skip this section). A *geometric series* is kind of sum in math. I find that I tend to learn best by example, so I am going to start by showing a classic example of a geometric series.

$$\sum_{n=1}^{\infty}2^{-n}=1$$

If you have no idea what that means, that's okay. In my experience the most complex part of many topics are their notations. It's going to be easiest to break this down symbol-by-symbol.

### Symbol breakdown
1. $$\sum$$: This denotes that we are going to be *summing* many different terms.
2. $$\sum_{n=0}^{\infty}$$: This says that whatever we are summing will take in a variety of *indices*, which are selected by a variable $n$. Then, we are going to sum that variable from n = 0 to n = $\infty$
3. $2^{-n}$: This says we are going to raise $2$ to the $-n$ power. In other words, we are going to raise $\frac{1}{2}$ to the $n$ power. An example of this is $2^-3$, which is equal to $\frac{1}{2}\times\frac{1}{2}\times\frac{1}{2}=\frac{1}{8}$

This specific notation for a sum is called *sigma notation*, and it helps condense a lot of problems. It also can be confusing, so it might help to start with an example.

$$\sum_{n=0}^4 1 = 1_{n=0} + 1_{n=1} + 1_{n=2} + 1_{n=3} + 1_{n=4} = 5$$

In this example we see that each $1$ corresponds to a specific value of n, so this sum evaluates to 5. Here is another example:

$$\sum_{n=0}^4(n+1) = 1_{n=0} + 2_{n=1} + 3_{n=2} + 4_{n=3} + 5_{n=4} = 15$$

In this example we are adding many different integers. This time, the value $n$ changes the value of the term "under" the sum. For this final sum, let us instead look at one where the number of terms is some variable. For conssistency, let's call this variable $m$ and let's keep the index variable as $n$. Also, for fun, let's make the term under the sum depend on $x$.

$$\sum_{n=0}^{m}x^{n} = 1 + x + x^2 + x^3 + \dotsi + x^m$$

This looks really hard to deal with. Suppose we want to calculate this sum for $x=0.5$ and $m=10$. I don't know about you, but I don't want to have to do the entire computation of $1 + 0.5^1 + 0.5^2 + 0.5^3 + 0.5^4 + 0.5^5 + 0.5^6 + 0.5^7 + 0.5^8 + 0.5^9 + 0.5^{10}$. Luckily, we don't have to do this! To show you, I will start with something simpler.

### How to simplify geometric series

The astute reader may have noticed that the title for this section referred to a geometric series. In fact, the sum that we just looked at is a geometric series! It simplies into something that can look a little scary, but don't worry. We'll work our way up to it in a way that I hope will make sense.

$$\sum_{n=0}^{m}x^{n} = 1 + x + x^2 + x^3 + \dotsi + x^m = \frac{1 - x^{m+1}}{1 - x}$$

To derive this formula we are going to multiply the series by the value $\frac{1-x}{1-x}$. After that, we will look for some sort of simplification that can happen in the numerator. Since $\frac{1-x}{1-x}$ will almost always cancel to a value of 1, it is equivalent to multiplying the whole sum by 1. (The only exception for this is at x=1, but we don't need to worry about that right now. Just assume that $x \neq 1$.)

$$\sum_{n=0}^{m}x^{n} = \frac{1-x}{1-x}\times \sum_{n=0}^{m}x^{n} = \frac{1-x}{1-x} \times (1 + x + x^2 + x^3 + \dotsi + x^m)$$

The above formula is just multiplying the series by the fraction we picked. Next, we are going to distribute the $(1-x)$ across the series. This is one of the most important steps, so make sure this makes sense to you.

$$\frac{1-x}{1-x} \times (1 + x + x^2 + x^3 + \dotsi + x^m) = \frac{(1 + x + x^2 + x^3 + \dotsi + x^m) - x\times(1 + x + x^2 + x^3 + \dotsi + x^m)}{1-x}$$

Next, distribute the $x$ into the parentheses on the right. After doing this, we can notice that the left set of parentheses in the numerator contains a value of $x$, while the right set contains $-x$. Likewise for $x^2$, $x^3$, and every single power of $x$ up to and including $x^m$. These will cancel out, leaving just the $1$ in the left set and $-x^{m+1}$ in the right. 

$$\frac{(1 + x + x^2 + x^3 + \dotsi + x^m) - x\times(1 + x + x^2 + x^3 + \dotsi + x^m)}{1-x} = \frac{1 + x - x + x^2 - x^2 \dotsi + x^m - x^m - x^{m+1}}{1-x}$$

Since all of these intermediate terms cancel out, we can see that 

$$\frac{1 + x - x + x^2 - x^2 \dotsi + x^m - x^m - x^{m+1}}{1-x} = \frac{1-x^{m+1}}{1-x}$$

This is exactly the formula we hoped to recover!

### The introduction of infinity

Before we replaced the many terms of the series with a simple rational function, adding up a large number of terms in this series was horrendously impractical. Now that it can be done quite simply, it can be fun to play with the sum for a variety of different values. However, what happens if you were to sum up an *infinite* number of terms? 

This question has a few caveats. Realistically, adding up an infinite number of terms does not really make sense. Instead, when people discuss an "infinite" series what they are really referring to is the *limit* of a series as more terms are added. As an example let us look at an example of a geometric series:

[insert an image of the square]

As you can see, in the above image there are a series of rectangles added every step. Each rectangle is exactly half of the width of the previous rectangle, and visually it is apparent that the sum of the area of all infinity of the rectangles will be 2. So, what happens if we plug infinity into our geometric series formula.

$$\sum_{n=0}^{\infty}x^n=\frac{1-x^\infty}{1-x}$$.

If $x$ is between -1 and 1 then $x^n$ approaches zero as $n$ gets big, so we can say that the *limit* of $x^n$ as $n\arrow\infty$ is $0$.

