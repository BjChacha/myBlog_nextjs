---
title: '进化算法基础'
date: '2021-02-02'
# lastmode: ''
tags: ['deep learning']
draft: true
summary: '进化算法基础'
# images: ['']
authors: ['default']
layout: PostLayout
---

## 编码

- 二进制编码
- 格雷码编码
- 浮点数编码

## 选择算子

- 轮盘赌 Roulette Wheel Selection

  - ![Roulette Wheel Selection](https://i.loli.net/2021/02/04/gP3IHO6VnxTkFwl.png)

- 随机通用采样 Stochastic Universal Sampling

  - 不同于轮盘赌，SUS 有多个固定点（fixed point），故一次轮盘转动可决定多个选择样本。
  - ![SUS](https://i.loli.net/2021/02/04/FvR2azXUkmbpIq1.png)

- 联赛选择 Tournament Selection

  - 每次选择 k 个个体，从中选出一个优胜个体
  - ![Tournament Selection](https://www.tutorialspoint.com/genetic_algorithms/images/tournament_selection.jpg)

- 排序选择 Rank Selection

  - 当个体的适应度差距都比较小，可对种群按照适应度降序排序，然后再用轮盘赌算法来选择（此时每个个体的概率应该非常相近）

  - ![Rank Selection](https://i.loli.net/2021/02/04/yHEOI2bJtz9mPoG.png)

  - | Chromosome | Fitness Value | Rank |
    | ---------- | ------------- | ---- |
    | A          | 8.1           | 1    |
    | B          | 8.0           | 4    |
    | C          | 8.05          | 2    |
    | D          | 7.95          | 6    |
    | E          | 8.02          | 3    |
    | F          | 7.99          | 5    |

- 精英选择

  - 子父代一起参与候选，选择最优的前$N$个个体。精英选择的问题会导致算法收敛到局部最优。

## 交叉算子

- 单点交叉 One Point Crossover

  - 随机选择一个交叉点，将两个父母个体的染色体分别“切”成两半，然后将其中一部分交换，形成两个新的子代个体。
  - ![One Point Crossover](https://i.loli.net/2021/02/04/5elfxpHSYVCnXkR.png)

- 多点交叉 Multi Point Crossover

  - 随机选择多个交叉点，其余操作类似单点交叉
  - ![Multi Point Crossover](https://i.loli.net/2021/02/04/lZeFvNWTHDVAJhS.png)

- 均匀交叉 Uniform Crossover

  - 对于每个基因，随机选择两个父母个体的其中一个基因，直至形成新的子代个体的基因。
  - ![Uniform Crossover](https://i.loli.net/2021/02/04/wfnOBEULdWr3Qzv.png)

- CMX(Center of Mass Crossover)\SPX(Simplex Crossover)

- 模拟二进制交叉 Simulated Binary Crossover

  $$
  \begin{equation}
  \left\{
  \begin{aligned}
  c_i^1 & = 0.5\times[(1+\beta)\cdot x_i^1+(1-\beta)\cdot x_i^2] \\
  c_i^2 & = 0.5\times[(1+\beta)\cdot x_i^1+(1-\beta)\cdot x_i^2]
  \end{aligned}
  \right.
  \end{equation}
  $$

  $$
  \begin{equation}
  \beta=
  \left\{
  \begin{aligned}
  & (rand\times 2)^{1/(1+\eta)} &rand\leq0.5 \\
  & (1/(2-rand\times 2))^{1/(1+\eta)} &otherwise.
  \end{aligned}
  \right.
  \end{equation}
  $$

  - $\eta$ 是一个自定义的参数，$\eta$ 越大，产生的子代个体逼近父代个体的概率就越大。所以 SBX 算子在局部优化搜索上表现较佳。

- 差分算子 DE

  $$
  v_i=x_i+F\times (x_i^2-x_i^3)
  $$

  - $F$ 是一个量化因子，可用于控制新生个体与父代个体之间的差别。由于 DE 算子产生的子代个体与父代个体之间的差别较大，所以在**全局**搜索能力上有不俗的表现。

## 变异算子

- 二进制变异
- 差分变异
- 高斯变异
- 多项式变异
- 均匀变异
