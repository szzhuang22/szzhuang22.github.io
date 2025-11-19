---
title: "Hello World!"
date: 2025-09-11 10:49:00
categories: [环境配置]
tags: [水下机器人, Isaac sim, Isaac ros, OceanSim]
---

# 前言

本文用于指导 Isaac Sim、OceanSim 以及 ROS2 的安装及配置。截止当前，Isaac Sim版本为5.0.0。

--- 

## Isaac Sim 5.0安装
首先进入官网[Nvidia Isaac Sim 官网](https://developer.nvidia.com/isaac/sim)，里面有关于isaac sim的一些介绍，Isaac Sim是基于Nvidia Omniverse构建的用于数据仿真、数据采集和机器人训练的仿真平台。

点击下面的[Download Isaac Sim](https://docs.isaacsim.omniverse.nvidia.com/5.0.0/installation/index.html)，会显示Isaac Sim的安装教程，分为两种方式，分别是Quick Install和Full requirements Install，选择第二种，先点击Isaac Sim Requirements查看依赖项，里面会详细的说明5.0.0版本所需要的硬件配置![硬件依赖](/szzhuang22.github.io/assets/img/isaac%20sim%20hardware%20requirements.png)

可以输入以下命令查看本机硬件配置
```terminal
lscpu
free -h
nvidia-smi
```

尽管Isaac Sim 5.0.0版本要求的最低VRAM为16GB，大于我的电脑的10GB 3080，但仍然无视风险，继续安装。如果想一键查看是否满足配置，可以点击上方的Isaac Sim Compatibility Checker，下载检查器，下载完成后解压

```terminal
unzip isaac-sim-comp-check-5.0.0-linux-x86_64.zip -d isaac-sim-comp-check
```

来到isaac-sim-comp-check文件夹下，输入
```terminal
./omni.isaac.sim.compatibility_check.sh
```

可以看到结果为
![本地硬件](/szzhuang22.github.io/assets/img/local%20hardware%20check.png)似乎配置也够。


回到下载页面[Isaac Sim 5.0 Download](https://docs.isaacsim.omniverse.nvidia.com/5.0.0/installation/download.html)，找到Isaac Sim那一行点击Linux版本下载。


下载完解压
```terminal
unzip isaac-sim-standalone-5.0.0-linux-x86_64.zip -d isaac-sim 
```
等待解压完成。

左侧列表中有三种安装方式，分别为workstation, container, cloud，我们选择workstation安装方式。










## 参考链接
[Isaac ROS安装指南，包含Sim安装，ROS安装，ZED等传感器调试](https://nvidia-isaac-ros.github.io/getting_started/index.html)
[Isaac Sim调试视频](https://www.bilibili.com/video/BV1uFGTzSEdz?spm_id_from=333.788.videopod.sections&vd_source=108bdf2b7083151030d235ef8674417e)
[OceanSim github](https://github.com/umfieldrobotics/OceanSim)
[Isaac Sim主页](https://developer.nvidia.com/isaac/sim)
[Isaac ROS ZED 安装](https://nvidia-isaac-ros.github.io/getting_started/hardware_setup/sensors/zed_setup.html)
[Isaac Sim下，SLAM采集数据完整搭建流程](https://zhaoxuhui.top/blog/2022/12/16/omniverse-and-isaac-sim-note5-isaac-sim-ros-api-for-image-and-pose-topic-publish.html)
[Isaac 环境入门-Bilibili](https://www.bilibili.com/video/BV1gBGTz9EBA?spm_id_from=333.788.videopod.sections&vd_source=108bdf2b7083151030d235ef8674417e)