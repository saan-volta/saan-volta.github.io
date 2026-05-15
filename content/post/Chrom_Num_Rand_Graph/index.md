---
title: Chromatic Number of a Random Graph
date: 2026-03-14
categories:
  - Math
  - Probability
---
### Introduction
This is a really clean result I quite like about bounding the deviation of the chromatic number of a random graph. It seems like something that would be incredibly difficult to even argue about, but it only takes a mildly clever random process construction combined with a generic deviation bound inequality, and the answer comes out in two lines.

---
### The Tricks
{{< definition name="Doob martingale" >}}
Let $X$ be a random variable with $E(X)<\infty$ and let $(\mathcal{F}_{n})$ be any filtration. 
Define $(Y_{n})$ by $Y_{n}=E(X|\mathcal{F}_{n})$.
{{< /definition >}}
- $(Y_{n})$ is a martingale: $$E(Y_{n+1}|\mathcal{F}_{n})=E(E(X|\mathcal{F}_{n+1})|\mathcal{F}_{n})\overset{(*)}{=}E(X|\mathcal{F}_{n})=Y_{n}$$$(*)$ follows from projection rule, since $\mathcal{F}_{n}\subset \mathcal{F}_{n+1}$.
- This is also called an exposure martingale, since the filtration "exposes" information about $X$ sequentially.

In this particular case, we will examine two examples below. Let $G\sim\mathcal{G}(n,p)$ be an Erdos random graph, that is, a graph on $n$ vertices where each edge has independent probability of appearing $p$. 
{{< definition name="Edge exposure martingale" >}}
Let $m=\binom{n}{2}$, and fix an arbitrary ordering of possible edges $e_{1},e_{2},...,e_{m}$. Define events $A_{i}=\mathbb{1}_{\set{e_{i}\in G}}$ and let $f$ be a real-valued function defined on $n$-vertex graphs, then:
$$X_{i}=E[f(G)\mid \sigma(A_{1},...,A_{i})]\quad\text{ for }i\in[0,m]$$
is the edge exposure martingale.
{{< /definition >}}

Note that at $i=0$, there is no information, $X_{0}=E[f(G)]$, and at $i=m$, every edge is revealed, $\sigma(A_{1},...,A_{m})=\sigma(G)$, so $X_{m}=f(G)$.

{{< definition name="Vertex exposure martingale" >}}
Fix an arbitrary ordering of vertices $v_{1},...,v_{n}$. Then for $i\in[1,n]$ define
$$X_{i}=E\bigg[f(G)\mid \sigma(G[v_{1},...,v_{i}])\bigg],$$
where $G[S]$ for $S\subseteq V$ is the induced subgraph of $S$.
{{< /definition >}}

As above, we can see that $X_{0}=E[f(G)]$ and $X_{n}=f(G)$.
Next, to bound the concentration of the chromatic number, we need, well, a concentration bound.
{{< lemma name="Azuma-Hoeffding inequality" >}}
For martingale $(X_{t})$ with bounded increments $|X_{t+1}-X_{t}|\leq c_{t}\text{ a.s.}$, time $T$, and any $\varepsilon>0$:
$$P\bigg(|X_{T}-X_{0}|>\varepsilon\bigg) \leq 2\exp\left(\frac{-2\varepsilon^{2}}{\sum_{s\leq t}c_{s}^{2}}\right).$$
{{< /lemma >}}

Finally, we have all the tools in place.
{{< theorem name="Theorem 1" >}}
Let $G\sim\mathcal{G}(n,p)$. Then 
$$|E[\chi(G)]-\chi(G)|\leq \sqrt{\frac{n}{2}\ln n}\quad \text{a.a.s.}$$
{{< /theorem >}}

Let $X_{i}$ be the vertex exposure martingale with $\chi$  as our function. Check that 
$$|X_{i+1}-X_{i}|=E[\chi(G)\mid \sigma(G[v_{1},.,,,v_{i},v_{i+1}])]-E[\chi(G)\mid\sigma(G[v_{1},...,v_{i}])]\leq 1$$
since the inclusion of a single vertex can change the chromatic number by at most $1$ (i.e., it will require a new color). Thus we get our bound, and all that's left is to put Azuma at the wheel:
$$P(|E[\chi(G)]-\chi(G)|>\varepsilon)=P(|X_{0}-X_{n}|>\varepsilon)\leq 2\exp\left(\frac{-2\varepsilon^{2}}{n}\right).$$
Using $\varepsilon=\sqrt{(n/2)\ln n}$ gives us $P(\cdot >\varepsilon)\leq 2\exp(-\ln n)=2/n$, which means the statement holds asymptotically almost surely as $n\rightarrow \infty$. $\square$

---
### Conclusion
In fact there are results which give a much stricter bound, but they're not as clean as this. I first learned about this during HackMIT hackathon; when we were looking for an open classroom to stay in overnight, the one we wandered into had this theorem written on the chalkboard from, I'm assuming, an earlier lecture. So I guess this is now my good luck charm theorem.