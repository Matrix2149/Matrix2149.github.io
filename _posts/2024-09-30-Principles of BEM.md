---
layout: post
title: "BEM for Potential equation(Laplace or Poisson)"
date: 2024-09-30 12:00:00 +0800
categories: [Science]
tags: [PDE_numerical, RC_extraction]  
---
## 用处
- 数学：解PDE，特别泊松方程、拉普拉斯方程
- IC：RC提取的field solver，比较快的一种

## 全貌
- 基本解构造-格林公式降维-边界积分方程-边界条件分类-边界单元离散

## 概念
- 基本解：方程右侧源为点源，密度为delta函数时的解。用来构造适用格林公式的形式。泊松方程基本解为ln(r)/2pi
- 边界积分方程：将基本解与待求的Laplace方程结合，用格林公式得到区域内解的积分形式。经过分析后，得到边界解的积分。方程左边待求函数有一自由系数，在区域内系数为1，边界上为1/2，区域外为0。取1/2是为边界积分方程。

## 重点
数值解过程
1. 初始化：边界条件、边界单元、离散点数
2. 求矩阵：根据边界积分方程和输入参数求得系数矩阵H、G
3. 解方程：由H、G得方程组AX=B，解出边界上的未知函数值与函数梯度值
4. 计算函数值：再由域内积分方程将边界值代入解得区域内部各点函数值

## 关联
有限元、有限差分

## 参考
The Boundary Element Method for Engineers and Scientists Theory and Applications (John T. Katsikadelis) 