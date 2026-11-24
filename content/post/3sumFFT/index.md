---
title: 3SUM in O(n logn) - A Simple Application of Fast Fourier Transform 
date: 2026-11-23
categories:
    - Algorithms
---
Fourier transform originates from signal processing, but in CS/algorithms context, a key application of it is for multiplying polynomials[^1][^2]. However, I think it might be hard to initially see where that might be useful, since most people don't often find themselves yearning for a faster way to multiply their polynomials. I like this example for grounding it in a classical computational problem:

{{< problem name="3SUM" >}}
Given integer array $S=[s_{1},...,s_{n}]$ and a target integer $X$, determine if there exists a triplet of distinct elements that sum to $X$. Additionally, count the number of such triplets.
{{< /problem >}}

A simple solution you might be expected to produce at a coding interview is iterating over all pairs $(s_{i},s_{j})$ for $i\neq j$ and storing the remainder $X-s_{i}-s_{j}$ in a dictionary as key. Then you'd iterate again over the array and check if any element with index $\notin \set{i,j}$ is an existing key in the dictionary. The double-iteration step requires $O(n^{2})$ time. Another, more space-optimal solution, is sorting the array and running a two-pointer sweep between $i$ to $n$ for each $i$; the time is still $O(n^{2})$ in the worst case. Can we make this faster? Well, it's in the title.

### A Quick Primer
The Fast Fourier Transform (FFT) is an algorithm to compute the D(iscrete)FT and the inverse DFT of two sequences. A key application of it is computing convolution of the sequences, since [convolution in the time domain corresponds to point-wise multiplication in the Fourier/frequency domain](https://en.wikipedia.org/wiki/Convolution_theorem). We can imagine it in terms of polynomials: view the sequences as lists of coefficients of two polynomials, then the coefficients of their polynomials' product are the convolution of the two original sequences. In this sense, DFT allows us to multiply two polynomials $p$ and $q$ using the identity
$$\text{DFT}(pq) = \text{DFT}(p)\cdot \text{DFT}(q),$$
where $\cdot$ is element-wise multiplication. Computing the right side and applying $\text{InvDFT}$ to it yields the product. While naive polynomial multiplication is $O(n^{2})$, FFT is a recursive algorithm that performs the transform in $O(n\log n)$, so running it 3 times plus the linear element-wise product is much more efficient asymptotically.

### Solution
Consider two polynomials $p(x)=\sum_{i=0}^{n}a_{i}x^{i}$ and $q(x)=\sum_{i=0}^{m}b_{i}x^{i}$. Their product, by definition, has the form
$$(pq)(x)=\sum\limits_{i=0}^{n+m}\left(\sum\limits_{j=0}^{i}a_{j}b_{i-j}\right)x^{i}$$
The coefficient of $x^{i}$ (the inner sum) is equivalently
$$\sum_{k,l \;\geq \;0 \;\text{ s.t. }\; k+l=i}a_{k}b_{l}$$
This is the discrete convolution of the coefficients. For our application, it gives us exactly what we need - counting the ways to pair up elements adding to a particular sum.

Here's the concrete representation: for each $i\geq 0$, take $a_{i}$ as the number of times $i$ appears in $S$ (zero if it is absent):
$$a_{i}:=\#\set{j\in 1,...,n\mid s_{j}=i}. $$
Our polynomial is $p(x)=\sum_{i\geq0}a_{i}x^{i}$. The square of it is
$$p(x)^{2}=\sum\limits_{i\geq0} \left(\sum\limits_{k,l\geq 0\;:\;k+l=i}a_{k}a_{l}\right)x^{i}$$
As we observed above, the coefficient of each $x^{i}$ is the sum over every pair of elements adding up to $i$, scaled by their frequencies in the array - in other words, it's literally the number of ways to choose two elements that sum to $i$. Continuing this, observe that coefficients of the third power
$$p(x)^{3}=\sum\limits_{i\geq0} \left(\sum\limits_{k,l,m \geq 0 \; : \; k+l+m=i} a_{k}\cdot a_{l}\cdot a_{m}\right)x^{i}$$
count the number of triples adding up to each $i$. This is very nearly the 3SUM solution! It's clean, but there's a catch: the sum is taken over *ordered* triples $(k,l,m)$, rather than unordered as we want, and furthermore imposes no restriction of the three chosen elements being distinct, so we are not quite done yet. How can we account for these constraints in our polynomial multiplication representation?

We will attempt to eliminate triples of duplicates first. Once that is done, and our counts only include distinct *ordered* triples, we will simply need to divide by $3! =6$, since for each distinct triple there are that many ways to order it.

A non-distinct triple $(k,l,m)$ satisfies $k+l+m=i$ with two (or more) of them being equal; wlog suppose $l=m$. We can rewrite this triple in a way that uniquely identifies it with $k$:
$$(k, \;j/2, \;j/2),$$
where $j:=l+m=i-k$. So counting triples summing to $i$ with two of the same element is equivalent to counting pairs $j+k=i$ where $j$ is even. This is actually conceptually another instance of 2SUM, but where we scale our values by the coefficient of $x^{j/2}$ rather than $x^{j}$. Consider what happens to the polynomial as we square $x$:
$$p(x^{2})=\sum\limits_{i\geq0}a_{i}(x^{2})^{i}=\sum\limits_{i\geq0,\; i\in 2\mathbb{Z}} a_{i/2}x^{i}$$
The second summation is a re-indexing to even $i$. After this shift, we solve the aforementioned 2SUM with the standard multiplication:
$$p(x^{2})p(x)=\sum\limits_{i\geq0}\left(\sum\limits_{j,k\geq 0 \;: \; j+k=i} a_{j/2}\cdot a_{k}\right)x^{i}.$$
For each choice of $j/2$ and $k$, there're 3 ways of arranging them in an ordered triple. However, $3p(x^{2})p(x)$ overcounts triples with all three elements equal $(i/3, \;i/3, \;i/3)$, so we have to subtract two copies. For each $i$, the number of all-equal triplets with that value is just $a_{i}$, which can be expressed as a polynomial with the same shifting trick:
$$p(x^{3})=\sum\limits_{i\geq0, \; i\in 3\mathbb{Z}} a_{i/3}x^{i}.$$
Finally, putting it all together, the answer is
$$p_{\text{3SUM}}(x)=\frac{p(x)^{3}-3p(x^{2})p(x)+2p(x^{3})}{6}.$$
In this resultant polynomial, the coefficient of each $x^{i}$ is the number of distinct unordered triples of array elements summing to $i$. Importantly, it solves 3SUM not just for the given target $X$, but for all possible values. This requires 3 polynomial multiplications, which are log-linear (see below about that) with FFT plus several linear scaling and addition operations.

### Example
{{< example >}}
Take $S=[1, 2, 2, 2, 3, 3]$.
Let's count the numbers of triples that sum to each $i$ to check our solution later:
- $i \leq 4\quad \rightarrow 0$
- $i=5\quad \rightarrow 3$ (two 2s + one 1)
- $i=6\quad \rightarrow 7$ (three 2s, one 1 + any 2 + any 3)
- $i=7\quad \rightarrow 7$ (two 3s + one 1, any two 2s + any 3)
- $i=8\quad\rightarrow 3$ (two 3s + any 2)
- $i\geq 9\quad\rightarrow 0$
{{< /example >}}

Build the polynomial with frequency coefficients
$$p(x)=1x^{1}+3x^{2}+2x^{3}$$
and proceed with the steps above to compute
$$p_\text{3SUM}(x)=3x^{5}+7x^{6}+7x^{7}+3x^{8}.$$

### Strictly speaking, I clickbaited you
The thing is, polynomial multiplication with FFT is indeed log-linear, but thus far I've neglected to mention that it's log-linear with respect to the degree (maximum exponent) of the polynomial, rather than $n$ (length of input array). In our case, the degree is the maximum element of the array. So this solution requires a bound $B\geq \max S$, and for the solution to be log-linear with respect to $n$, $B$ should grow (at worst) linearly in $n$. This requirement makes it not a truly general 3SUM solution - indeed, no $O(n\log n)$ algorithm for 3SUM without any restrictions is known to exist, and only very recently a refutation was published to the conjecture that no truly subquadratic solution is possible.


[^1]: Cormen, Leiserson, Rivest, Stein. Introduction to Algorithms (3rd ed). The FFT method for 3SUM is in particular inspired by ex. 1-7 of chapter 30.
[^2]: https://cp-algorithms.com/algebra/fft.html

