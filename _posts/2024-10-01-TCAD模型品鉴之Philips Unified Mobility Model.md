---
layout: post
title: "TCAD模型品鉴之Philips Unified Mobility Model"
date: 2024-10-01 17:30:00 +0800
categories: [Technology]
tags: [TCAD, Modeling]  
---

# 概况
Philips unified mobility model或者说Klaassen mobility model，是TCAD仿真最常用的低场迁移率模型了。原因无他，就因为"unified"，把低电场下一般会有影响的散射机制用统一的公式都考虑进去了，电子空穴都能算。而且还考虑了诸多物理效应：杂质散射的屏蔽效应、高掺杂下杂质团簇（clustering）、温度依赖。
散射机制总共四种：
- 晶格散射
- 多数杂质散射(majority impurity)
- 少数杂质散射(minority impurity)
- 电子空穴散射(electron-hole)

$\frac{1}{\mu_{i, b}} = \frac{1}{\mu_{i, L}}+\frac{1}{\mu_{i, DAeh}} \tag 1$

后三种库伦散射统一在（1）右边的第二项，第一项是晶格散射。
就迁移率来说，除开材料本身的晶体结构影响，就三种因素决定：$\mu(N,T,E)$。这模型一出，基本就把N和T的作用全囊括了。

# 精髓
## 如何unify
Klaassen model的精髓在于它对三种库伦散射的统一，而且统一在对应散射粒子的浓度上。既保证足够精确，又在公式上实现了物理的直观。

$\mu_{i,\text{ DAeh }}= \mu_{i, N}\Bigg(\frac{N_{i, \text{sc}}}{N_{i, \text{sc,eff}}}\Bigg)\Bigg(\frac{N_{i, \text{ref}}}{N_{i,\text{sc}}}\Bigg)^{\alpha_i} 
+\mu_{i, \text{c}}\Bigg(\frac{n+p}{N_{i, \text{sc,eff}}}\Bigg) \tag2 $

$\begin{cases}
N_{\mathrm{e,sc,eff}}=N_{\mathrm{D}}+G(P_{\mathrm{e}})N_{\mathrm{A}}+\frac p{F(P_{\mathrm{e}})}
\\ N_{\mathrm{h,sc,eff}}=N_{\mathrm{A}}+G(P_{\mathrm{h}})N_{\mathrm{D}}+\frac n{F(P_{\mathrm{h}})}
\end{cases}
 \tag3$

unify最关键的就是(3)算的这个$N_{i,sc,eff}$，从脚标可以称之为有效散射浓度。公式右边，从左到右分别对应多数杂质散射、少数杂质散射和电子空穴散射。多数杂质散射直接取材于半经验公式，重要的是后两种散射通过$G$和$F$两个迁移率之比获得了相同的形式，这就很妙。
$\mu_{\mathbf{e},\mathbf{A}}(N_\mathbf{A},c)=\frac{\mu_{\mathbf{e},\mathbf{D}}(N_\mathbf{D}=N_\mathbf{A},c)}{G(P_\mathbf{e})} \tag4$
$\mu_{\mathrm{e.h}}(p,c)=F(P_e)\mu_{\mathrm{e.D}}(N_D=p,c) \tag5$

这里只写电子迁移率了，空穴换一下符号就行。

可以看到(4)(5)有相同的项，用Matthiessen Rule合起来算迁移率就是：
$\frac{1}{\mu_{i,\text{ DAeh }}}=\frac{1}{\mu_{e,D}(N_D)}+\frac{G(P_e)}{\mu_{e,D}(N_A)}+\frac{1}{F(P_e)\mu_{e,D}(p)} \tag6$

所以算$\mu_{i,DAeh}$只需要带入$\mu_{e,D}$的公式。这个公式第一篇论文的第2.2节，Klaassen采用的是基于Caughey-Thomas半经验公式的改进版：
$\mu_{i,I}(N_i)=\mu_{i,N}\left(\frac{N_{\mathrm{ref,1}}}{N_i}\right)^{x_1}+\mu_{i,c}\left(\frac{c}{N_i}\right) \tag7$

他把$\mu_{i,c}$这一项乘上了个$c/N_i$，采用的是Ridley统计屏蔽理论，主要考虑高掺杂下的屏蔽作用使得散射没那么多。

(7)已经能看到(2)的框架了，但是把(7)带入(6)还直接得不到(2)，会发现$N_D, G*N_A, p/F$三者之间是一种比较复杂的数学关系，远不像(3)那么赏心悦目。我推了很长时间也没想出来从理论上怎么得到(3)，只能解释为Klaassen做了近似。

**换个角度想，这其实就是建模的艺术！模型开发不能只是搬运理论，更要把这些搬来的理论化为己用。结合建模的场景和条件，对原始理论做工程性的改造，建模工作的独特价值就在于此。**

还有一点要明白的是，Klaassen之所以敢做这种近似，是因为这里损失的精度可以通过其他参数补回来。两篇论文验证的结果都相当精确，所以Klaassen model在trade-off上把握得非常之好。

## 两个关键变量的计算
上面说的unify，最关键的就是找到了$G$和$F$两个变量，而且这方面散射的研究前人也做过不少。理论基础Klaassen都是现成的，无论是$G$的WKB算散射相移，再用分波法从相移算到散射截面，还是用Born近似算$F$。

他能想到将散射截面之比是受57年Blatt工作的启发，那篇文章里就算了很多多数杂质和少数杂质的散射截面之比。把散射截面之比迁移到迁移率之比，算是Klaassen的创新，再类似地把弛豫时间之比等效为迁移率之比也顺理成章。所以这两个变量的计算也是精华。

# 背景意义
Klaassen model早期说是主要用来算Bipolar，因为电子空穴都能算，也就意味着可以算多子和少子。但不知道为啥MOS一个单极型器件后来也用上了，可能是模型确实强大吧。

读Klaassen model原文的Introduction部分，可以看到他当时开发这个模型要解决的一个问题就是之前多子和少子迁移率分开用各自的模型算，导致数值问题。而且之前的模型，经验公式多，考虑的散射机制也不够。所以他才搞了个"physics-based analytical"模型。

# 总结
评价一个TCAD模型有诸多维度，现在能想到五条：
- 计算精确
- 理论可靠
- 公式易懂
- 数值稳定
- 条件鲁棒

Klaassen model毫无疑问是迁移率模型中多边形战士。如果说还有不足，那也是历史局限：比如新器件、新材料中新的散射机制没考虑进去；极端的应用场景超出理论有效范围，导致模型失效......

但每当我们在器件仿真的Mobility部分敲下`PhuMob`或`KLA`时，依然会惊叹其三十多年前开发时的精妙。

# 参考
- [A unified mobility model for device simulation](https://ieeexplore.ieee.org/document/237157)
- [A unified mobility model for device simulation—I. Model equations and concentration dependence](https://www.sciencedirect.com/science/article/abs/pii/0038110192903257#:~:text=The%20first%20physics-based%20analytical%20model)
- [A unified mobility model for device simulation—II. Temperature dependence of carrier mobility and lifetime](https://www.sciencedirect.com/science/article/abs/pii/0038110192903268#:~:text=Abstract.%20In%20Part%20I%20we%20presented%20the)
- Sentaurus Sdevice User Manual
- Silvaco Atlas User Manual