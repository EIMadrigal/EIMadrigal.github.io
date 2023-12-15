---
title: Research Proposal
url: research-proposal
date: 2021-07-15 02:35:00
description: 科研垃圾
categories: Computer Science
tags: [AutoML]
password: initiate
---

## 创新点1

 1. 加入了采样方法的自动选择
 2. 代理模型对于高维稀疏数据的处理XGBoost
 3. Playout阶段的改进
 - [ ] 代理模型采用二分类器，类似于FB的paper
 - [ ] pipeline：默认config组成的pipeline的影响（结合meta-learning）；components在Tree中的层次搜索顺序：初始数据集均匀随机采3个，嵌入先验分布，如`score_each_cl`通过先验进行修正

## 创新点2

 - [ ] 建立不平衡数据集的meta database作为warm-start，加速搜索过程
 - [ ] 使用bandit等策略来对时间分配策略调整
 - [ ] 考虑从黑盒优化转为灰盒优化（Multi-fidelity优化），如训练集子集、观察学习曲线等
 - [ ] 朱老师说的按照元特征去指导MCTS的搜索方向，提前剪枝等
 - [ ] 多分类不平衡数据

## 探索
 - [ ] AdaBoost/AdaBoost-MH/GBDT/XGBoost/LightGBM（工业界用的多）等集成学习用来对分错的数据集进行提升 
 - [ ] 概率图模型（每个数据集对应的可能的算法是由概率形式表现的）：$P(A_i|D)=\cfrac{P(D|A_i)P(A_i)}{P(D)}$
 - [ ] BO/CBO/SOO (综述paper)
 - [ ] 除了基于精确度和时间的对比，但是很多时候搜索阶段表现最好的，在评估阶段并不是最好的，甚至可能表现很差。 因此一种用的比较多的就是Kendall Tau metric，它会评估两个阶段模型性能的相关性，相关性越高则表示算法越有效

可能由于样本量太少，特征太多，导致数据稀疏，用低维特征去做分类，元特征选择/降维

致命缺点：算法性能的差别可能是由于数据集质量决定的，而不是元特征的质量？？所以元特征还应有一个衡量数据集质量的特征？？

## Advice

1. 我要解决的问题是什么？
2. 这个问题的难点在哪？
3. 别人是怎么干的？为什么这么干？效果怎么样？这么干有什么缺陷？
4. 我打算怎么干？为什么这样干是对的？
5. 实验验证
