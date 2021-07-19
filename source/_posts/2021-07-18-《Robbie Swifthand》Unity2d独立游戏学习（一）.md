---
title: 《Robbie Swifthand》Unity2d独立游戏学习（一）
date: 2021-07-18 16:16:23
categories: unity
tags:
  - unity
  - c#
  - tile
---



### 	始、前言

继续跟M大学习unity2d开发，这次是《从独立游戏学习开发》系列，我们将从一款已经上架steam的独立游戏《Robbie Swifthand and the Orb of Mysteries》中学习



版本：

unity 2021.1.12f1c1

vs 2019

unityhub 2.1.14



<!--more-->

---



### 一、导入资源

1.资源地址：https://pan.baidu.com/s/1c0I1pO7Rr8w4s4SGcfscHA

提取码:   a338

2.新建2d unity项目，界面左上角工具栏中Assets - import package - custom assets ，导入刚刚下载的资源

3.导入过程中提示更新API，选择“Yes”

4.导入完成后发现脚本报错，原因是我的新版unity自带的脚本和资源里的有冲突，需要删除对应的新脚本



---



### 二、整理资源

导入工作完成后Assets文件夹下出现了大量的文件夹，先看看他们都是些什么

1._Extended：额外的扩展包，后期需要用到的资源

2.Addons：插件，新版本unity有自带的，这里需要删掉

3.Audio：音乐素材

4.Fonts：字体

5.Gizmos：小插件，这里包含2D摄像机跟踪

6.Level：背景前景素材，用于制作透视效果

7.Props：道具，里面是一些游戏元素的预制体

8.Robbie：本游戏主角，包含主角的素材、动画等

9.Scripts：脚本，包含几个简单代码，其他的自己写

10.Tilemaps：用来存放游戏素材，即项目中正在用的素材（为啥得用他给我的文件夹，我就不）

11.UI：用户界面用到的素材

12.VFX：特效



---



### 三、绘制背景准备工作

1.找到windows - 2D - Tile Palette调出调色板窗口

2.新建调色板，起名Background，存放到Tilemap文件夹里新建的Palettes文件夹

3.将Level中的素材BG walls 拖入调色板里，保存到Tilemap - Tile 新建的Background文件夹

4.重复2~3步骤，制作Platform和Shadow的调色板，同样管理好切割的瓦片

5.在调色板界面中点击Edit 就可以修改调色板的素材（界面中几个画画工具都玩一下）

6.在Hierarchy中新建2D Object - Tilemap ，命名为Background

7.在调色板Active Tilemap 中可以选择要绘制的对象

8.同样地，在Grid 父物体下新建Tilemap 三个物体，分别命名Background Detailed、Shadow、Platform

9.设置Sorting Layer，添加四种，分别为Background、Platform、Player、Foreground（Sorting Layer 中越往下排的层级在镜头中越靠前）

10.分别设置Tilemap的Sorting Layer ，其中Order In Layer 又可以在同Layer 中再排序一次

（设置都要套娃？）

11.拖入主角Robbie，设置Sorting Layer 为Player



---



### 四、设置Rule Tile

Rule Tile 是超强的瓦片绘制工具，通过设定瓦片生成规则，实现利用一个瓦片就可以任意拖拽生成各种不规则图案。新版本的unity已经成为内置功能，可以直接创建使用

1.在Tilemaps - Tile - Backgrounds中新建Rule Tile，命名为BG rule ，默认图片随便选个背景瓦片

2.添加瓦片，并给每个瓦片设置规则，上面的规则比下面的规则优先。

规则为：检测九宫格中是否存在其他同样在列表中的瓦片，绿色箭头表示那个方向**必**存在此类瓦片，红色叉叉表示**必**不存在此类瓦片，没有设置的方向就是没要求

3.设置好每个瓦片后，将BG rule 拖入调色板Palette 中， 这样就可以用这个瓦片随意画图啦！

4.多设置几个功能瓦片堆积在一起可以变成大瓦片，方便大面积作图

5.同上，设置Shadows 瓦片的Rule Tile

6.设置Platforms 瓦片的Rule Tile 时，我们希望平台瓦片随机生成，而不用某种特定规则。在Rule Tile 下添加一个瓦片，在Output中选择Random，Size设置为4，将要随机生成的四种瓦片拖入空格中



---



### 五、绘制地图

可以随意绘制地图，但是有几个注意点：

1.在调色板中选取瓦片时，要记得在Active Tilemap 中选择要绘制的对象

2.瓦片要绘制两层以上，因为各种规则瓦片在只有一层时会非常难看

3.Shadows 瓦片覆盖在在Platforms 瓦片上面，后面调灯光的时候就会有很美妙的效果

4.地图记得留一个出口位置



---



### 终、睡觉

画地图还挺过瘾的，先这样了，洗澡睡觉！
