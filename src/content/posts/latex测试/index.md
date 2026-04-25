---
title: "机器学习概论"
description: "机器学习概论"
published: 2022-09-16
draft: false
tags:
  - "机器学习"
  - "NFL定理"
lang: ""
category:
  - "课程笔记"
---

# NFL定理

**No Free Lunch Theorem**：天下没有免费的午餐

我们计算一个学习算法$\mathcal{L}_a$的“训练集外误差”，有

$$
\begin{aligned}
  E_{ote}(\mathcal{L}_a|X,f)=\sum_{h}\sum_{x\in\mathcal{X}-X}P(x)\mathbb{I}(h(x)\ne f(x))P(h|X,\mathcal{L}_a)
\end{aligned}
$$

如果对于所有真实目标函数$f$求和，则有

$$
\begin{aligned}
  \sum_f E_{ote}(\mathcal{L}_a|X,f)&=\sum_f\sum_h\sum_{x\in \mathcal{X}-X}P(x)\mathbb{I}(h(x)\ne f(x))P(h|X,\mathcal{L}_a)\\
  &=\sum_{x\in\mathcal{X}-X}P(x)\sum_hP(h|X,\mathcal{L}_a)\sum_f\mathbb{I}(h(x)\ne f(x))\\
  &=\sum_{x\in \mathcal{X}-X}P(x)\sum_hP(h|X,\mathcal{L}_a)\frac{1}{2}2^{|\mathcal{X}|}\\
  &=\frac{1}{2}2^{|\mathcal{X}|}\sum_{x\in \mathcal{X}-X}P(x)\sum_hP(h|X,\mathcal{L}_a)\\
  &=\frac{1}{2}2^{|\mathcal{X}|}\sum_{x\in \mathcal{X}-X}P(x)\\
\end{aligned}
$$

我们可以看到，总误差与学习算法无关，也就是说，对于任意两个学习算法$\mathcal{L}_a$，$\mathcal{L}_b$，无论看上去$\mathcal{L}_a$多复杂，多聪明，在期望的意义下，他们的性能是相同的。~~（也就是说，随便瞎猜和复杂推导一样，不学了 (\*｀皿´\*)ﾉ ）~~

<br>

# 尝试证明AUC计算公式

排序损失$\mathcal{l}_{rank}$被定义为

$$
\begin{aligned}
  \mathcal{l}_{rank} = \frac{1}{m^+m^-}\sum_{x^+\in D^+}\sum_{x^-\in D^-}\left(\mathbb{I}(f(x^+)<f(x^-)) + \frac{1}{2}\mathbb{I}(f(x^+)=f(x^-))\right)
\end{aligned}
$$

对于一个ROC图，我们有如下关系：

$$
\text{AUC}=1-\mathcal{l}_{rank}
$$

（代入理想模型和随机模型，发现是对的。。）
