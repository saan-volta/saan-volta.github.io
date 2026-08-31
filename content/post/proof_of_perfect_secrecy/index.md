---
title: Proof of Correctness of MEC Steganography
date: 2026-08-30
categories:
- Algorithms
- Probability
---



### Abstract
This is the proof of correctness of the stenographic encoding algorithm described in [^1]. The paper provides empirical results to demonstrate the effectiveness, but not a full theoretical argument. I derived this to convince myself that it is indeed correct.

---
### Introduction
The message is randomized with a key and partitioned into $n$ blocks: $X_{1}, \ldots, X_{n}$. Each block is uniform $X_{i}\sim\text{Unif}[2^{b}]$ where $b$ is the parameter block size. The covertext generator is a distribution $\mathcal{C}(c\mid c_{1},...,c_{m}):=P(C_{m+1}=c\mid C_{1}=c_{1},...,C_{m}=c_{m})$ specified autoregressively, where the random vector $(C_{1},...,C_{m})$ is the "context" in the LLM sense and $C_{m+1}$ is the next generated "token".

The algorithm proceeds as follows:
0. For $i\in 1\ldots n$, initialize $\mu_{i}$ to uniform distributions $\set{0,1}^{b}\rightarrow [0,1]$.
1. For $j\in 1\ldots m$:
	1. $i^{*}:=\arg\max_{i}H(\mu_{i})$
	2. $\gamma_{j}:=$ MEC of $\mu_{i^{*}}$ and $\mathcal{C}(C_{j}\mid C_{1:j-1}=S_{1:j-1})$, the autoregressive distribution of next token
	3. $S_{j}\sim \gamma_{j}(C_{j}\mid X_{i^{*}}=x_{i^{*}})$, the distribution of next token conditional on the $i^{*}$th block of ciphertext
	4. $\mu_{i^{*}}\leftarrow \gamma_{j}(X_{i^{*}}\mid C_{j}=S_{j})$ 

The objective is to show that $(C_{1},...,C_{m})\overset{D}{=}(S_{1},\ldots S_{m})$, that is, the random vectors of $m$-sequences of tokens produced naturally and those produced by the algorithm are equivalent in distribution, i.e., the encoding does not introduce any statistical bias.

As a shorthand, I will write events $\set{S_{j}=s_{j}}$ as $\set{s_{j}}$ and $\set{S_{1:j-1}=s_{j-1}}$ as $\set{s_{1:j-1}}$. The notation $[n]$ denotes the set $\set{1,...,n}$.

---
### Proof
The proof proceeds in two steps. First, we must show that at the beginning of each iteration $j$, for all $i\in[n]$ and all values of $x$, we have
$$P(X_{i}=x\mid s_{1:j-1} )=\mu_{i}(x)$$
that is, the $\mu_{i}$ distributions maintained by the algorithm are the accurate distributions of $X_{i}$ conditional on the previously produced tokens. This is not entirely trivial, since the $\mu_{i}$ are updated manually. Secondly, we show the equality in distributions between natural and encoded sequences of tokens.

{{< lemma >}}
For each iteration $j$, for all $i\in[n]$ and values of $x$, the following hold:
$$\newcommand{\indep}{\mathrel{\perp\!\!\!\perp}}
\begin{align*}
&(i)\qquad P(X_{i}=x\mid s_{1:j-1})=\mu_{i}(x) \\
&(ii)\qquad X_{1} \indep X_{2} \indep \ldots \indep X_{n}\;\mid s_{1:j-1}
\end{align*}$$
{{< /lemma >}}

The second claim is that $\set{X_{i}}_{n}$ are mutually conditionally independent given $s_{1:j-1}$. We will use this statement as a sort of inventor's paradox to establish the first.

*Proof.* Proceed by induction on $j$. At the start of the first iteration, $X_{i}\sim\text{Unif}[2^{b}]=\mu_{i}$ by definition. All $X_{i}$ are mutually independent, so the claims hold trivially.
In the inductive case, we assume the claims hold at the start of step $j$. The maximum entropy block $i^{*}$ is chosen. 
We prove $(i)$ first; the aim is to show that for all $i$, $\mu'_{i}$, defined to be the adjusted $\mu$ at the end of the step, satisfies
$$\mu'_{i}(x)=P(X_{i}=x\mid s_{1:j})\quad \forall x.$$
Note that only for $i=i^{*}$ does the distribution actually change; the rest are left untouched. We therefore consider two cases:

<u>Case 1:</u> $i=i^{*}$
Breaking down the conditioning as $P(\ldots \mid s_{1:j-1}\cap s_{j})$ and rewriting, we get
$$\begin{align*}
P(X_{i^{*}}=x\mid s_{1:j})&=\frac{P(X_{i^{*}}=x\mid s_{1:j-1})P(s_{j}\mid X_{i^{*}}=x,\; s_{1:j-1})}{P(s_{j}\mid s_{j-1})}\\
&= \frac{\mu_{i^{*}}(x)\gamma_{j}(s_{j}\mid x)}{\mathcal{C}(s_{j}\mid s_{j-1})}\\
&= \frac{\gamma_{j}(x,s_{j})}{\mathcal{C}(s_{j}\mid s_{j-1})}\\
&=\gamma_{j}(x\mid s_{j})= \mu'_{i^{*}}(x).
\end{align*}$$
In the above derivation, the coupling $\gamma_{j}$ twice allows us to change conditioning via chain rule with its marginal (respectively $\mu_{i^{*}}$ and $\mathcal{C}$).

<u>Case 2:</u> $i\neq i^{*}$
Similarly, we write
$$P(X_{i}=x\mid s_{1:j})=\frac{P(X_{i}=x\mid s_{1:j-1})P(S_{j}=s_{j}\mid X_{i}=x,\; s_{1:j-1})}{P(s_{j}\mid s_{1:j-1})}$$
Since $S_{j}\sim\gamma_{j}(C_{j}\mid X_{i^{*}}=x_{i^{*}})$, where the next token $C_{j}\sim\mathcal{C}(\cdot \mid s_{1:j-1})$ is independent of $X_{i}$ trivially and $X_{i^{*}}$ is conditionally independent of $X_{i}$ by the inductive hypothesis, $S_{j}$ is also conditionally independent of $X_{i}$. Thus the second term in the numerator can drop the conditioning on $X_{i}=x$, simplifying to
$$P(X_{i}=x\mid s_{1:j}) = \frac{\mu_{i}(x)\mathcal{C}(s_{j}\mid s_{j-1})}{\mathcal{C}(s_{j}\mid s_{j-1})}=\mu_{i}(x)=\mu'_{i}(x).$$

Lastly, we show that $(ii)$ holds at the end of each step. Using the same identity and the conditional independence given $s_{1:j-1}$:
$$\begin{align*}
P\bigg(\bigcap_{i\in[n]}X_{i}=x_{i}\mid s_{1:j}\bigg) &=\frac{P(s_{j}\mid s_{1:j-1}\cap  \bigcap_{i}X_{i}=x_{i})}{\mathcal{C}(s_{j}\mid s_{j-1})} \prod_{i\in[n]} P(X_{i}=x_{i}\mid s_{1:j-1})
\end{align*}$$
Once again the independence of $S_{j}$ allows us to drop the conditioning on all $X_{i}$ except $X_{i^{*}}$ and simplify:
$$\begin{align*}
\ldots \;&= \frac{\gamma_{j}(s_{j}\mid x_{i^{*}})}{\mathcal{C}(s_{j}\mid s_{j-1})}\prod_{i\in[n]} P(X_{i}=x_{i}\mid s_{1:j-1})\\
&= \frac{\gamma_{j}(x_{i^{*}}\mid s_{j})}{\mu_{i^{*}}(x_{i^{*}})}\prod_{i\in[n]}\mu_{i}(x_{i})\\
&= \mu'_{i^{*}}(x_{i^{*}})\prod_{i\in[n]\setminus\set{i^{*}}} \mu'_{i}(x_{i})\\
&= \prod_{i\in[n]} \mu'_{i}(x_{i}) \\
&=\prod_{i\in[n]}P(X_{i}=x_{i}\mid s_{1:j}).
\end{align*}$$
This concludes the proof of $(i)$ and $(ii)$ at the beginning of step $j+1$.


{{< theorem >}}
Let $(S_{1},...,S_{m})$ be the random sequence of tokens produced by the algorithm (stegotext), and let $(C_{1},...,C_{m})$ be the random sequence of tokens generated naturally (covertext) in an autoregressive manner, i.e. $C_{j}\sim\mathcal{C}(\cdot \mid C_{1:j-1}=c_{1:j-1})$. Then:
$$(S_{1},...,S_{m})\overset{D}{=}(C_{1},...,C_{m}).$$
{{< /theorem >}}

*Proof.* 
Let $j$ be fixed and consider some value $s$ of the next token $S_{j}$.
$$P(S_{j}=s\mid s_{1:j-1})=\sum\limits_{x}\gamma_{j}(X_{i^{*}}=x,s)=\sum\limits_{x}\gamma_{j}(s\mid x)\mu_{i^{*}}(x)=\mathcal{C}(s\mid s_{1:j-1})$$
The first equality averages over all possible values of $X_{i^{*}}$ and the rest follows by definition of coupling. Now, taking the product over all $j$ with the chain rule:
$$\begin{align*}
P(S_{1:m}=s_{1:m})&= \prod_{j\in[m]}P(S_{j}=s_{j}\mid s_{1:j-1}) \\
&= \prod_{j\in[m]}\mathcal{C}(s_{j}\mid s_{1:j-1})\\
&= P(C_{1:m}=s_{1:m}).
\end{align*}$$
Thus, the random vectors have equal distribution.



>[!corollary] 
> The algorithm constructs a coupling between a factorable uniform distribution and the autoregressive conditional $\mathcal{C}(\cdot\mid\cdot)$.

Let $\text{A}(\mathbf{x}, \mathbf{s})$ be the joint distribution induced by the algorithm between the space of $n$-block ciphertexts and $m$-token text samples, writing $\text{A}(\mathbf{x},\mathbf{s}):=P(\mathbf{X}=\mathbf{x},\;\mathbf{S}=\mathbf{s})$ and $\text{A}(\mathbf{s}\mid \mathbf{x})$ for probability of outputting stegotext $\mathbf{s}$ given input ciphertext $\mathbf{x}$, where $\mathbf{X}=(X_{1},...,X_{n})$  and $\mathbf{S}=(S_{1},...,S_{m})$.

Observe that for any $\mathbf{x}$ and $\mathbf{s}$, we have $\text{A}(\mathbf{x},\mathbf{s})=\text{A}(\mathbf{s}\mid \mathbf{x})P(\mathbf{X}=\mathbf{x})=\mathcal{C}(\mathbf{s})P(\mathbf{X}=\mathbf{x})$ by above. Now fixing $\mathbf{x}$ and summing over $\mathbf{s}$ yields $P(\mathbf{X}=\mathbf{x})$, and likewise fixing $\mathbf{s}$ and summing over $\mathbf{x}$ gives $\mathcal{C}(\mathbf{s})$, which are the exact marginals, showing $\text{A}(\cdot\mid \cdot)$ is a coupling.


---
### Appendix
The proof makes repeated use of this basic identity:
$$P(A\mid B\cap C)=\frac{P(A\mid B)P(C\mid A\cap  B)}{P(C\mid B)}$$

[^1]: https://arxiv.org/abs/2210.14889
