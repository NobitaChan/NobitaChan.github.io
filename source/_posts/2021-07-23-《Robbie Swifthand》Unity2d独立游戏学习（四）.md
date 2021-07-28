---
title: 《Robbie Swifthand》Unity2d独立游戏学习（四）
date: 2021-07-23 15:22:41
categories: unity
tags:
  - unity
  - c#
  - camera
---



### 	始、前言

接下来做一些视觉上的东西，首先是然镜头跟随主角移动，还有场景的灯光效果



<!--more-->

---

### 	一、摄像机

1.可以选择Camera 修改背景颜色

2.将摄像机的Projection 修改为Perspective，这样就设置成了透视模式，可以通过修改图层深度来产生景深效果

3.为了产生景深效果，将Grid 中的Background 的Transform → Position → z 设置为 1， 同样地，将Background Details 设置为0.99，Platforms 不变， Shadow 为 - 0.1

4.在unity界面Window → Package Manager 中添加 Cinemachine

5.创建2D Camera物体，将主角拖拽到相机的Follow中，摄像机会跟随角色移动

6.可以设置Camera Distance 到一个合适的位置，这个选项会影响游戏画面的远近

7.选择2D Camera → Extensions → Add Extension 添加一个CinemachineConfiner

8.新建空项目，添加多边形碰撞体，设置成触发器，拖拽到2D摄像机的Confiner中。这样就可以通过调整多边形的形状来限制摄像机的移动范围啦

完成，调试

发现阴影没有遮挡角色，要把Shadow 的Sorting Layer 修改成Foreground



---



### 二、2D灯光效果

1.在Props 中找到墙灯，打开预制体修改父物体和子物体的Sorting Layer为Background（粒子效果又是一个大坑）

2.拖入场景之前添加点光源，设置光源的z 轴为 -0.2 ，这样就可以在所有背景的前面照亮它们（更靠近摄像机），还可以修改颜色的照明距离等

拖入火炬后为什么场景没有被照亮呢？因为场景中的物体都还没添加法线贴图

3.打开Level 文件夹，将Platform 和Background 的材质球拖入对应的场景物体中（材质会自动对应瓦片）

4.打开Props 添加另外一种灯：悬挂蓝灯，蓝灯会左右摇摆，因为它有一个动画效果修改z 轴旋转

5.调整场景的整体灯光，打开Window → Rendering → Lighting，在Environment Lighting 中可以调整颜色和亮度

