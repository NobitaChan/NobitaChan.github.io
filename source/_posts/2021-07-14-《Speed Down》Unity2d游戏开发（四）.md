---
title: 《Speed Down》Unity2d游戏开发（四）
date: 2021-07-14 20:55:23
categories:	unity
tags:
  - unity
  - c#
  - prefab
---



### 	始、阶段调整

这次不做新功能，先停一停把之前的东西调整一下，细节~太细了~

<!--more-->

---



### 一、图案模糊

顶部尖刺看起来有些模糊

1.找到尖刺的图案，将Advanced → Filter Mode 修改成Point

检查所有的图案是否模糊，套用这个方法



---



### 二、修改背景颜色

1.选择主相机，修改Camera → Background 到喜欢的颜色



---



### 三、给链锤添加链子？

听起来有点奇怪，但是我的链锤现在确实是没有链子，看起来像是原力驱动

1.给链锤整体添加组件Line Renderer，选择喜欢材质、颜色、粗细，并把Position → Size改成2

2.添加脚本：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SpikeBallLine : MonoBehaviour
{

    LineRenderer line;
    public Transform begin;
    public Transform end;

    void Start()
    {
        line = GetComponent<LineRenderer>();

    }

    void Update()
    {
        line.SetPosition(0, begin.position);
        line.SetPosition(1, end.position);
    }
}
```

挂载到链锤父物体上，注意脚本和线组件是同一级别的



---



### 四、保存预制体

为了可以持续不断地随机生成平台，需要将平台保存为预制体

1.创建prefabs文件夹，并把平台和人物添加到其中

**Prefab是什么？**

当制作好了游戏组件（场景中的任意一个gameobject）,我们希望将它制作成一个组件模版，用于批量的套用工作，例如说场景中本质上要重复使用的东西，比如：敌人、士兵、子弹或者一个砖块完全相同的墙体。这里说本质是因为默认生成的prefab其实和模版是一模一样的。就像是克隆体，但生成的位置、角度或者一些属性不同（这个就好像c++里边的类一样）；

