---
layout: post
title:  基于学习的MVS深度估计
date: 2022-5-05
categories: blog
tags: [MVS,标签二]
description: 基于学习的MVS方法的经典pipeline
---

这篇文章是在阅读了《Rethinking Depth Estimation for Multi-View Stereo: A Unified Representation》这篇文章之后，感觉论文中作者对MVS方法的论述很是精炼，因此个人加以润色后记录下来，
既是作为留档，也希望能够分享我的看法
## Multi-View Stereo简介
MVS可以简单地理解为多视立体几何，即通过两个（双目）或多个视角的2D图像信息理解3D信息的算法，比如说预测深度、点云重建和SLAM等任务。如果从方法上分类，则大概可分为传统方法和基于学习的方法
### 传统方法
如果以输出结果的形式作为分类依据，那么传统方法可大体分为4类：<br>

1. 基于体素的
2. 基于点云的
3. 基于面片(mesh)的
4. 基于深度图的<br>
   
在这四种方法中，基于深度图的方法是灵活性最强的。相比与其他三种方法，基于深度图的方法将复杂的3维几何重建问题降级为了2维的深度图估计问题。而且深度图作为重建3维信息的一个中间表示，每张单独视角的深度图都可以被进一步融合为点云、体素和面片
### 基于学习的方法
和其他领域类似，MVS的传统方法同样依赖于手工特征，随着深度学习的发展，许多基于学习的方法在MVS相关的任务中表现很好。比如说SurfaceNet和LSM，这两个网络是基于学习的体素重建网络。然而重建体素
的一大缺点是对存储占用太高。MVSNet（2018 ECCV）第一个实现了端到端的，基于cost-volume的低内存占用pipeline。这个pipeline主要包含四个部分：

1. 通过2D CNN进行特征提取
2. 通过单应性变换进行代价聚合
3. 通过3D CNN进行代价正则化
4. 深度回归<br>

在MVSNet之后，也有非常多的工作在其基础上进行了改进<br>
## MVS pipeline
首先明确任务：深度估计。具体来说就是给定一张图像$I_1$(reference image)，推断得到深度图$D\in\mathbb{R}^{H'\times W'}$的过程。对于MVS中的深度估计而言，其输入是一系列的图像$\{I_i\in\mathbb{R}^{C\times H\times W }\}_{i=1}^{N}$<br>
第一步是通过特征提取网络得到所有输入图像的feature map$\{F_i\in\mathbb{R}^{C'\times H'\times W'}\}^N_{i=1}$，这里的特征提取网络是一个共享权重的2D CNN（当然也可以选择其他backbone）<br>
正如前面所言，基于学习的MVS方法是建立在cost volume基础上的。这样的cost volume又是基于深度值假设$\{d_i\in\mathbb{R}^{H'\times W'}\}^M_{i=1}$，这里面$d_1$是最小的深度值，$d_M$是最大的深度值。而$\{d_i\in\mathbb{R}^{H'\times W'}\}^M_{i=1}$就是在这个范围内取值的<br>
这样，将每个source image的feature map可微单应性变换至reference image的相机光锥中，就可以得到feature volume$\{V_i\in\mathbb{R}^{M\times C'\times H'\times W'}\}^M_{i=1}$<br>
第$i$个视角和reference视角间的单应性变换如下：
$$H_i(d)=dK_iT_iT_1^{-1}K_1^{-1}$$













