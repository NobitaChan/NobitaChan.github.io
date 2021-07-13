---
title: 《Speed Down》Unity2d游戏开发（一）
date: 2021-07-06 17:45:05
categories: unity
tags:
  - unity
  - c#
  - unityhub
  - 2d
---



### 			始、版本

unity 2021.1.12f1c1

unityhub 2.4.13

vs 2019

<!---more--->

---



### 				一、导入素材

1.新建unity2d项目

2.在assetstore中查找 Pixel Adventure 1并添加到我的资源

3.在unity中打开Package Manager → My Assets → Pixel Adventure 1，下载并导入项目中

资源栏就出现这个包啦！



---



### 			二、切换发布平台

1.点击file → build setting → Android → switch platform

2.发现自己unity没有这个模块，要手动在unityhub安装模块

3.发现没有添加模块的按钮，要移除版本，前往官网找到改版本并选择从unityhub中下载

4.添加下载Android Target Support



---



### 			三、背景图案设置

1.新建3d object → quad，reset位置，设置scale中x为9，y为16

2.调整主摄像机，包裹场景

3.新建材质球background，修改shader为unlit → texture

4.选择喜欢的背景图案，更改pixel per unit为64，拖到材质球中

5.添加材质球到quad中

6.相机中没有图片，因为相机和场景重叠了，修改相机z轴位置为-10

简单的背景图片出现了，现在想要背景图案变成多个重复的图案，并且可以循环移动：

7.设置图案advanced → wrap mode → repeat，表示图案是可以循环重复的（记得apply）

8.更改材质球的tilling就可以看到场景图案铺垫的样子，可以看到改变offset的值就可以让场景移动



---



### 		四、脚本实现背景移动

1.新建c#脚本，等unity加载完毕（右下角有转圈标识，没加载完就操作会出现奇怪的错误）

2.编写脚本：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class BackgroundMove : MonoBehaviour
{
    Material material;
    Vector2 movement;

    public Vector2 speed;
    void Start()
    {
        material = GetComponent<Renderer>().material;
    }

    void Update()
    {
        movement += speed * Time.deltaTime;
        material.mainTextureOffset = movement;
    }
}
```

注意：获取material时是获取组件Renderer中的material，即“material是Renderer的material”

这样就可以通过改变背景脚本一栏中的speed来改变背景移动速度



---



### 	五、添加顶部尖刺

1.找到素材中的spikes，修改pixel per unit 为16

2.新建空物体TopSpikes用来存储所有的顶部尖刺

3.将尖刺图案拖拽到TopSpikes下方成为子物体

4.修改尖刺的rotation → z 为18翻转尖刺，修改scale → y为2延长图案

5.按住`V`可以选择锚点对齐放置，`ctrl + d`复制物体，重复步骤直到填满顶部

6.全选尖刺，添加组件polygon collider 2d 多边形碰撞体，打勾used by composite，即把这些尖刺的碰撞体合并起来

7.添加组件composite collider 2d ，此时会自动添加rigibody 2d

8.将rigibody 2d → body type改为static，这样尖刺就保持在原地不会掉下去

9.调整composite collider 2d → offset 可以改变碰撞体的位置



---



### 	终、未完待续

基本的场景搭建已经完成！
