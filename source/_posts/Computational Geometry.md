---
title: Computational Geometry
url: computational-geometry
date: 2020-04-20 10:15:00
description: 计算几何摘录
categories: Math
tags: [Algorithm]
---

## 线段重叠
```cpp
start1 <= end2 && end1 >= start2
```

## 矩形重叠面积
看过某司一道笔试题：给$n$个矩形左下和右上坐标（不能斜放），求重叠最多处矩形个数。  
这道题本身不难：可以遍历所有矩形边界组成的点，计算**该点被多少矩形包围**，从而选出最大值。  
由此引申出一个问题：**判断两个矩形重叠**。

 - 如果正向思考，会有很多种情况：包含、重叠某个角、交叉...

那么如果逆向思考：什么情况两个矩形不重叠？无非就是$A(p_1, p_2)$在$B(p_3, p_4)$的上下左右：
$$(p_1.y>=p_4.y)\vee(p_3.y>=p_2.y)\vee(p_3.x>=p_2.x)\vee(p_1.x>=p_4.x)$$
取反后用De Morgan's law化简就是重叠的情况：
$$(p_1.y<p_4.y)\wedge(p_3.y<p_2.y)\wedge(p_3.x<p_2.x)\wedge(p_1.x<p_4.x)$$

```cpp
struct point {
    int x, y;
};

int crossArea(point p1, point p2, point p3, point p4) {
    // 不重叠
    if (p1.y >= p4.y || p3.y >= p2.y || p3.x >= p2.x || p1.x >= p4.x) {
        return 0;
    }
    // 左右上下边界
    int x1 = max(p1.x, p3.x);
    int x2 = min(p2.x, p4.x);
    int y1 = min(p2.y, p4.y);
    int y2 = max(p1.y, p3.y);
    return (x2 - x1) * (y1 - y2);
}
```

## 线段交点
联立方程组求解当然没问题，也可以用几何的方法解：  
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDIwLmNuYmxvZ3MuY29tL2Jsb2cvMTI2MDU4MS8yMDIwMDQvMTI2MDU4MS0yMDIwMDQyMDA5Mjk1NTI0OS05MzA1Nzg4NjkucG5n?x-oss-process=image/format,png)  
易知，$\frac{AO}{BO}=\frac{AE}{BF}=\frac{S_{ACD}}{S_{BCD}}$，两个三角形面积可以用叉积求得，又$\vec{AO}=\frac{AO}{AB}\vec{AB}=\frac{AO}{AO+BO}\vec{AB}$，所以$\vec{O'O}=\vec{O'A}+\vec{AO}$，即可求得$O$点坐标。

## 线段覆盖
有若干线段$[l_i,r_i]$以及目标线段$[a,b]$，需要用尽可能多的线段去覆盖目标线段，且线段之间不相交，线段长度之和最小。  
直观上看：我们的策略首先以长度为准则：显然不妥，选了黑的就不是最优  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729213724839.png)  
按照起始点：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729213754372.png)  
按照结束点：最优  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729213857178.png)  
从前向后取区间，最小化对后面的影响，选择最早结束的区间。

## 向量旋转
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDIwLmNuYmxvZ3MuY29tL2Jsb2cvMTI2MDU4MS8yMDIwMDQvMTI2MDU4MS0yMDIwMDQyMDA5NDU1Mzg4My0xOTgxNzI0NjU3LnBuZw?x-oss-process=image/format,png)  
三角变换可得：
$$\vec b=(xcos\alpha-ysin\alpha,ycos\alpha+xsin\alpha)$$

## 多边形面积
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDIwLmNuYmxvZ3MuY29tL2Jsb2cvMTI2MDU4MS8yMDIwMDQvMTI2MDU4MS0yMDIwMDQyMDEwMTQxNjU5NC0xMjc1OTkyNDExLnBuZw?x-oss-process=image/format,png)  
三角剖分：
$$S_{ABCDEF}=\frac{\vec{OA}\times\vec{OB}+\vec{OB}\times\vec{OC}+...+\vec{OF}\times\vec{OA}}{2}$$
即：
$$S=A_n\times A_1+\sum_{i=1}^{n-1}A_i\times A_{i+1}=x_ny_1-y_nx_1+\sum_{i=1}^{n-1}x_iy_{i+1}-y_ix_{i+1}$$

## 凸包
包围所有给定点并且周长最小的多边形。

![image](https://img2020.cnblogs.com/blog/1260581/202112/1260581-20211229164832081-268323827.jpg)  
直接求不好算，需要旋转坐标轴：  
![image](https://img2020.cnblogs.com/blog/1260581/202112/1260581-20211229164921157-1565408775.png)  
![image](https://img2020.cnblogs.com/blog/1260581/202112/1260581-20211229164931460-670765424.png)
```python
class Solution:
    def cloest(self, x, y, l, w, yaw):
        # 原始坐标系逆时针旋转theta = yaw - 90得到坐标系(xx,yy)
        theta = math.radians(yaw - 90)
        xx = x * math.cos(theta) + y * math.sin(theta)
        yy = -x * math.sin(theta) + y * math.cos(theta)

        points = [(xx + w / 2, yy + l / 2), (xx - w / 2, yy - l / 2), (xx + w / 2, yy - l / 2), (xx - w / 2, yy + l / 2)]
        dis = [a ** 2 + b ** 2 for a, b in points]
        ans = points[dis.index(min(dis))]
        ans_x = ans[0] * math.cos(theta) - ans[1] * math.sin(theta)
        ans_y = ans[0] * math.sin(theta) + ans[1] * math.cos(theta)
        return ans_x, ans_y
```

---
*reference*
*[洛谷日报#142 计算几何初步](https://zhuanlan.zhihu.com/p/68617952)*