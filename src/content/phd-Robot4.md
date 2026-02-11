---
title: 机器人研究(四)
date: 2020-03-06 21:40:33
categories: 技术博客
tags:
    - IT,Web，
toc: true
thumbnail:
---

<!--more-->

## SLAM

在 ROS2 中，地图构建常用 SLAM（Simultaneous Localization and Mapping） 技术。其大概流程是:

- 传感器采集数据：可使用激光雷达（2D/3D LiDAR）或相机（VSLAM）
- 里程计信息：来自机器人轮式里程计/IMU
- SLAM算法处理：融合传感器数据+里程计，估计机器人位姿同时构建环境栅格地图。
- 地图表示：最终生成map.pgm（图像）+map.yaml（参数配置）

SLAM简单理解就是让机器人在一个陌生的环境能够自主定位和地图构建，常用的ROS2建图工具有：

- slam_toolbox：ROS2官方推荐，支持在线建图（机器人移动时建图）、离线建图（先存bag后生成地图）、增量式建图（可保存后编辑地图）。
- cartographer_ros：google出品，实时性能好，但配置较复杂。
- RTAB-Map：可用于视觉/激光SLAM，支持3D

先来简单看看slam_toolbox的流程，其输入为/scan（激光雷达点云）、/odom（里程计）、/tf（坐标变换）三个话题；经过处理后输出/map（占据栅格地图nav_msgs/OccupancyGrid格式）、/tf（发布map->odom的变换用于定位



### Nav2

目前导航使用比较多的是Nav2

其主要流程可以分为4大部分

- 感知输入：输入激光雷达/scan、里程计/odom、TF（map->odom->base_link）、地图/map。
- 定位：使用AMCL（Adaptive Monte Carlo Localization)，在已有的地图中定位机器人位置，发布map->odom的TF。
- 规划：全局规划器 (planner_server)从当前位置到目标点，规划一条全局路径（比如 A*、Dijkstra、NavFn）。局部规划器 (controller_server)：根据实时传感器数据，计算机器人下一步的速度指令 /cmd_vel（如 DWB controller）。
- 执行调度：行为树 (bt_navigator)管理整个任务流程定位 → 规划 → 控制 → 到达目标。恢复器 (recoveries_server)遇到障碍或失败时执行恢复动作（原地旋转、清理代价地图）。

https://www.laumy.tech/2945.html/ros2-%e5%bb%ba%e5%9b%be%e4%b8%8e%e5%af%bc%e8%88%aa%ef%bc%9aslam_toolbox%e3%80%81nav2%e5%ae%9e%e8%b7%b5/#sec-4



### mobile-aloha

将预训练的VLA模型（原本用于固定基座）应用于移动操作场景中，通过生成路径点（Waypoint）来指导移动和操作

https://mobile-aloha.github.io/cn.html
