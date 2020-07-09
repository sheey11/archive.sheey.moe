---
title: Infomation Theory Basics
date: 2020-07-08 18:27:08
update: 2020-07-08 18:27:08
tags: [笔记, ML]
---

Notes about infomation theory basics.

<!--more-->

# Entropy
In information theory, an entropy of a **distribution** $p$, is captured by the following equation:

$$ H[p] = \sum_j -p(j) \log p(j) $$

Or for single distribution:

$$ H[y] = - y \log y $$

Entropy is level of surprise experienced by someone who knows the true probability.  
熵描述了一个变量的不确定性。

## Nut and Bit

In order to encode data drawn randomly from the distribution $p$, we need at least $H[p]$ *nuts* to encode it.

- Nut
  Nut is the equivalent of bit but when using a code with base $e$ rather than one with base $2$.  
  $$ 1 \text{Nut} = \frac{1}{\log(2)} \approx 1.44 \text{bit}$$
- $\frac{H[p]}{2}$ is often also called the *binary entropy*.

# Cross-Entropy
*Cross-Entropy* from $p$ to $q$, denoted $H(p,q)$, is the <u>expected surprisal of an ovserver with subjective probalilities $q$ upon seeing data that was actually generated accroding to probalilities $p$</u>.  
交叉熵则描述了概率分布间的差异性。

$$H(p,q) = \sum_j -p(j) \log q(j)$$

# Kullback-Leibler Divergance
***Kullback-Leibler Divergance***, ***KL Divergance***, or ***Relative Entropy***, is the most commom way to measure the distance between two distributions. Which is simply <u>the differece between the cross-entropy and the entropy</u>.

$$ D(p||q) = H(p,q) - H[p] = \sum_j p(j) \log \frac{p(j)}{q(j)} $$
