---
title: ROS与机器人控制
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，
toc: true
thumbnail: 
---



<!--more-->

## 机器人运动学

### 雅各比矩阵

Jacobian Matrix 在[机械臂运动学](https://zhida.zhihu.com/search?content_id=187118345&content_type=Article&match_order=1&q=机械臂运动学&zhida_source=entity)中用来计算机械臂末端执行器的速度与各个关节运动速度之间的关系。

雅各比矩阵的实质是微分方程式。对于机械臂来说，我们可以通过[正向运动学](https://zhida.zhihu.com/search?content_id=187118345&content_type=Article&match_order=1&q=正向运动学&zhida_source=entity)，由关节运动信息，得到机器人位姿，即 

对于一个有 6 自由度的机器人来说，我们可以直接对矩阵求逆。但对于一个没有 6 自由度的机器人来说，就不能直接求逆，而是要用 pseudo-inverse 的方法来求逆



### 轨迹规划

在[机械臂](https://zhida.zhihu.com/search?content_id=187227923&content_type=Article&match_order=1&q=机械臂&zhida_source=entity)中，我们将机器人末端执行器移动时的位姿随时间变化而得出的曲线称为其轨迹。我们之所以需要对机器人的轨迹进行规划是因为通常来说，我们对机器人的输入只有工具的起点和终点。而我们需要让机器人能够自然平滑的按照我们想要的方式从起点移动到终点，所以需要设计一个算法，使得机器人在移动的过程中，无论是位置的变化还是速度的变化都是连续且平滑的，有时，也会要求加速度的变化为连续的

对于机械臂来说由两种空间下的轨迹规划，一种是基于关节空间 ([Joint-space](https://zhida.zhihu.com/search?content_id=187227923&content_type=Article&match_order=1&q=Joint-space&zhida_source=entity))， 另外一种是基于世界空间 ([Cartesian-space](https://zhida.zhihu.com/search?content_id=187227923&content_type=Article&match_order=1&q=Cartesian-space&zhida_source=entity))。

对 Joint-Space 来说进行轨迹规划需要以下步骤：

1. 确定轨迹的起点(Initial Point) - 途径点(Via Point) - 终点(Finial Point)
2. 通过 Inverse Kinematics (IK) 计算出以上所有点的 Joint Angle，即 Joint-space 下的所有点关于时间的位置.
3. 设计一条轨迹将 Joint-space 所有点都平滑的连接起来。
4. 再通过 Forword Kinematics (FK) 算出在这种情况下末端关于世界坐标的曲线。
5. 检查世界坐标下的曲线是否合理。

对 Cartesian-space 来说进行轨迹规划需要以下步骤：

1. 确定轨迹的起点(Initial Point) - 途径点(Via Point) - 终点(Finial Point)
2. 设计一条曲线将 Cartesian-space 所有点都平滑的连接起来。
3. 使用 IK 计算出这条曲线在 Joint-space 的曲线。
4. 检查 Joint-space 的曲线是否平滑。

Cartesian-space 来做轨迹规划是一种直观的对位姿轨迹的规划。但可能出现机器人的运动速度上的不平滑。

Joint-space 可以保证运动速度的平滑，但是运动位姿的轨迹可能与设计前预想的轨迹有区别，不会出现尖锐的拐角。

一般来说需要根据实际应用的情况来选择使用那种方法来设计轨迹。

对于多轴机器人来说，当机器人的位置处于奇点的时候，机器人会失去至少一个自由度。这可能会导致机器人在此处卡住。所以应该尽量避免奇点。

由几何法找奇点会比较复杂，较为简单的方法是根据[雅各比矩阵](https://zhuanlan.zhihu.com/p/445449208)来判断。当：

> determinat(det) 即行列式。

时，机器人就在奇点上。

这里所说的轨迹规划算法，实际上就是经过多点的平滑曲线拟合算法。如果使用多项式拟合，通常需要使用三次以上的多项式来拟合。线性拟合也可以，如[抛物线混合](https://zhida.zhihu.com/search?content_id=187227923&content_type=Article&match_order=1&q=抛物线混合&zhida_source=entity) (Parabolic Blends)。由于曲线需要经过每一个点，所以不能用贝塞尔曲线来设计轨迹。



## 机器人动力学

### 牛顿欧拉方程

牛顿-欧拉方程描述了刚体的组合平移和旋转动力。传统上，[牛顿-欧拉方程](https://zhida.zhihu.com/search?content_id=190538889&content_type=Article&match_order=3&q=牛顿-欧拉方程&zhida_source=entity)是使用列向量和矩阵将刚体的欧拉两个运动定律组合成一个具有 6 个分量的单个方程。这些定律将刚体重心的运动与作用在刚体上的力和扭矩（或[力矩](https://zhida.zhihu.com/search?content_id=190538889&content_type=Article&match_order=1&q=力矩&zhida_source=entity)）之和联系起来。

对于原点与物体质心重合的坐标系，可以用[矩阵](https://zhida.zhihu.com/search?content_id=190538889&content_type=Article&match_order=2&q=矩阵&zhida_source=entity)形式表示为

