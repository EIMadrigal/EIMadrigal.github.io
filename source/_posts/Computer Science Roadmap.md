---
title: Computer Science Roadmap
url: cs-roadmap
date: 2019-02-15 22:18:32
description: 计算机科学技能树
top: false
categories: Computer Science
tags: [Interview, Experience]
---

有感于国内令人发指的CS教育(尤其某校的计算机基本就是一堆SB在自嗨自娱自乐而已)，决定自学为主。  
主要资源是四大的比较完整的课程：video+reading+lab

 - [Awesome CS Courses](https://github.com/prakhar1989/awesome-courses)
 - [CS自学指南](https://csdiy.wiki/)
 - [名校公开课程评价网](https://conanhujinming.github.io/comments-for-awesome-courses/)
 - [Teach Yourself Computer Science](https://teachyourselfcs.com/)
 - [RT Huang的自学笔记](https://github.com/huangrt01/CS-notes)
 - [LEARNSYS](https://learn-sys.github.io/)
 - [OSSU](https://github.com/ossu/computer-science)

video比较费时间，而且我看视频总是来不及反应，好像不太适合我，所以一般只在看不懂材料时去针对性地看看视频。(当然一些讲得非常好的视频除外)

## Basics
 - **Programming Languages**: 精通C, 熟悉1~2门(Java/Python/C++/Go), 了解一门(Haskell/Rust), 掌握debug技巧
 - **Tools/Frameworks**: 熟悉Linux系统的各项操作，最好看下源码，掌握Git等工具和框架
 - **Math**: 线性代数，概率论，数理统计，组合数学，离散数学，微积分. 现用现学
 - **Core Courses**: DS/Algorithms/OS/Organization/Network/DB

## Coding Interview
没啥好说的，要么有天赋，要么很刻苦，经常在blog**分析总结**，**穿透做过的题目及变种**.  
不要抱着可能撞到原题的心态去准备，反复练习提升自己的能力，需要有较多的训练量。

 - **Data structure**: 哈希表、堆、AVL、链表动手实现一遍，B+树啥的都能扯扯~
 - **Algorithms**: [剑指offer](https://leetcode-cn.com/problemset/lcof/), [Cracking the Coding Interview](https://leetcode-cn.com/problemset/lcci/), [LeetCode](https://leetcode.com/)  
 - **System design**: 频率低但是也会有.

## Projects/Paper
**选一个前沿的、不太讨厌的方向，研究研究，做点小项目**，具体的方向可以参考[CSRankings](http://csrankings.org/)

 - 实习项目
 - 学校大作业
 - 兴趣项目
 - 开源项目

## 关于面试

每家公司的风格不一, 不过总体上可以分为以下几块:

### 笔试
算法题为主, 不需要时空复杂度最优, 需要练习ACM模式. 可以通过[牛客笔试题](https://www.nowcoder.com/), kickstart, codeforces等训练.

### 做题
通常需要给出最优解, 多练习就是了.

 1. 问清题目：数据范围是多少？这个数组的大小范围是多少？能不能给个样例？如果输入是这个，那输出应该是什么  
 2. 确认函数签名  
 3. 确认思路：修改输入数据  
 4. 确认corner case处理方式
 5. 编码过程中不断交流  
 6. 主动测试：写完后不要急于告诉面试官写完了，手动跑一个样例：在屏幕上写出中间变量的当前取值，然后用鼠标光标告诉面试官现在程序跑到了哪一行代码，当前各个变量的取值是多少等等  
 7. 主动分析复杂度  
 8. 讨论算法的trade-off

### 八股
没啥好说的, 多背就是了.

### 项目
面国内大厂比较重要, 如果有岗位相关的项目就可以聊天了, 不过一定要熟悉.

可以是实习项目, Paper, 自学项目等.

项目/research的背景主要包括场景、问题定义、需求、自己负责的部分扮演的角色等等, 指出项目中的困难点和解决方案.

### 软实力
 - GPA/数学/英语
 - 比赛奖项
 - 沟通交流能力: 更是一次需要充满着沟通与交流的谈话，让面试官认为他/她愿意成为你的同事. 虽然我不太懂，但是可以试着说一下, 说出自己的insight:cache不友好. 获得监督信息与正反馈.
 - 面经和技巧: 面经是告诉你这家公司面试的时候喜欢问哪些知识，而不是告诉你他们喜欢问哪些特定的问题。锦上添花, 最重要的还是及格的实力. Nothing replaces hard work. 先拿一些自己不target的公司练练手. 模拟面试.

## How to learn
It is very important to take classes around my future work. It doesn't matter you learn it slowly, the most important part is that you **take it seriously** and build a **solid foundation**.  
根据大佬们的经验，一门课大概要花150-300小时，每天2小时至少也要2个半月，所以千万千万不要着急，不要急于求成，总想着完成任务，多多反思自己到底学到了什么？真的透彻地理解了吗？又有多少内化到自身的知识体系？  
还有就是最好按照他们的课表时间上课，同时上的课最好不要超过2门（经过血泪实践，我只能1门单线程┭┮﹏┭┮，他们课程内容实在太充实了...，然后自己还有一堆屁事...）

**严格遵守学术规范**，独立完成之后可以参考别人，修正自己。

Recently I've changed my way to learn new things. Previously I just wanted to understand the new things and tried to memorize all the details of a specific problem, or just translated others' materials into my words, which melted my brain and showed a very low efficiency. The reason why I learn things this way (passively) is mostly due to the Chinese's cramming education. But for me, heuristic teaching (actively) is more appropriate. The specific problem/model/algorithm is important, but the **motivation** is much more important. **Everything has its motivation.** So I decide to write my blogs with the following components:

 1. Motivation: What problems do we meet? Why propose this one?
 2. Details: Mathematical derivation or tricky things.
 3. Example: Use a handy example to illustrate.
 4. Implementation: Code it out or use it to **solve the problem**.
 5. Properties: **When** should/can we use the method? When shouldn't/can't? **Why**? What's the benefits and drawbacks if we use it?
 6. Can we make some improvements on the off-the-shelf method for a specific problem?

## DONE LIST
Count the courses I've taken so far:

 1. Introduction to Computer Science. Harvard University  
    "This is CS 50". It should be the first class of CS rather than Haoqiang Tan's C Programming Language.
 2. Linear Algebra. Massachusetts Institute of Technology  
    If you want to learn Linear Algebra, just follow this one and you'll be fine.
 3. Mathematics for Computer Science. Massachusetts Institute of Technology  
    Very interesting course but I only took several lectures. SAD~
 4. Data Structures. University of California, Berkeley  
    Strong recommend for Data Structure. You'll pick up Java from the interesting projects.
 5. Introduction to Computer Systems. Carnegie Mellon University  
    If you only want to take one system course, then select this one. But I haven't finished the whole lectures and labs. SAD again~
 6. Introduction to Database Systems. Carnegie Mellon University  
    Hard for me. Just finished lab1. I'll come back one day~
 7. Machine Learning. Stanford University  
    It's almost the first course I took after I found the true CS courses. But I forgot a lot. Sorry Andrew~
 8. Positive Psychology. Harvard University  
    When I start to be anxious or depressed I'll go and find the lecture. Tal is an amazing teacher and I'm sure you'll become happier.
 9. Convolutional Neural Networks for Visual Recognition. Stanford University  
    High quality, especially its readings.
 10. Introduction to Computer Networking. Stanford University  
    Lab is amazing!
