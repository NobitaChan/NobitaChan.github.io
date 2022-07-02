---
title: 《Robbie Swifthand》Unity2d独立游戏学习（七）
date: 2021-09-12 15:30:04
categories:	unity
tags:
  - c#
  - unity
---



### 	始、回顾

（忙完开学的琐事）

先回顾上一回完成的内容，我们制作了会让角色死亡的尖刺，还加上了死亡效果配套的特效和声音。场景中添加了游戏的主要任务——收集宝珠，同样也有配套的特效和声音



<!--more-->

---



### 	一、更炫酷的视觉效果

1.检查插件Post Processing是否已安装，Window → Package Manage

2.为主摄像机添加组件Post Processing Layer

3.新建空项目Global Post Processing，将Layer 改成 Post Processing（新建）

4.将主摄像机中Post Processing Layer的Layer设置成Post Processing，这样主摄像机就会观察新项目的特效

5.为Global Post Processing添加组件Post Processing Volume，勾选is Global

6.为Global Post Processing新建一个Profile（new）用来承载即将要设置的特效，Add effect 可以添加各种视觉效果：

Bloom：泛光效果

Color Grading：镜头调色

Chromatic Aberration：场景畸变效果

Lens Distortion：镜头畸变效果9

Vignette：边缘阴影效果



7.调好特效了吗，调好了偏不用，就是玩儿。在Assets → VFX → Profiles 中找到Global Reference预制体，拖入Global Post Processing → Post Processing Volume → Profile（如果场景过亮可以在Lightning → Environment Lighting调整场景亮度）



---



### 	二、摄像头震动效果

1.在跟随人物的2D摄像机中的Extension添加新的组件Cinemachine Impulse Listener

2.在宝珠Shrine → Orb预制体中添加新组件Cinemachine Collision Impulse Sourse（就像音效系统一样，有一个接收器接收声音并且播放，有一个物体设定条件发出声音）

3.将Assets → VFX → Profiles中的Explosion Shake 拖入Cinemachine Collision Impulse Sourse → Raw Signal

4.设置触发此特效的Layer ，将Cinemachine Collision Impulse Sourse → Layer Mask 设置成Player，我们希望当Layer为Player的物体触碰到Orb时就会触发摄像头震动效果

5.还可以在Cinemachine Collision Impulse Sourse → Frequency Gain 设置成100，这个数值越高，震动的频率越高，更快完成



---



### 	终、休整

到此为止，我们已经实现了游戏大部分刚需内容，接下来把目光转移到游戏整体管理，还有一些细节的修补

