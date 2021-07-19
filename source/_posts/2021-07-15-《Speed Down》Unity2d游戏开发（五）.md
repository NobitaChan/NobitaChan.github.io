---
title: 《Speed Down》Unity2d游戏开发（五）
date: 2021-07-15 15:34:28
categories:	unity
tags:
  - unity
  - c#
  - trigger
  - collision
---



### 	一、随机生成平台

1.可以删除其他平台，只留下一个开局的平台

2.新建一个空物体作为平台出生点，调整大小，添加碰撞体，勾选is trigger，修改标签为spike

<!--more-->

3.新建脚本挂载到平台出生点：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Spawner : MonoBehaviour
{
    public List<GameObject> platform = new List<GameObject>();

    public float spawnTime;
    float countTime;
    Vector3 spawnPosition;


    void Update()
    {
        SpawnPlatform();
    }

    public void SpawnPlatform()
    {
        countTime += Time.deltaTime;
        spawnPosition = transform.position;
        spawnPosition.x = Random.Range(-3f, 3f);

        if(countTime > spawnTime)
        {
            CreatePlatform();
            countTime = 0;
        }

    }

    public void CreatePlatform()
    {
        int index = Random.Range(0, platform.Count);
        Instantiate(platform[index], spawnPosition, Quaternion.identity );
    }
}
```

4.回到unity设置脚本中平台列表的大小为3并且拖入三种基础平台

5.设置好生成速度Spawn Time就可以开始玩啦！



---



### 	二、细节调整

1.将链锤的锤子设置标签为spike（不小心删了链锤的预制体，我是sb，以后碰到警告操作不可撤回的要万分小心）

2.角色检测碰到尖刺死亡的代码是在OnTriggerEnter2D中的，而顶部尖刺是触发器，链锤是碰撞器，需要将死亡代码复制到OnCollisionEnter2D中。注意两个函数接收的变量不同，需要按照提示修改一下

3.改变平台列表的大小并拖入不同的平台，这样就可以调整游戏难度以及未来新增其他平台或者陷阱

4.在平台出生点中修改代码，添加：

```
        GameObject newPlatform = Instantiate(platform[index], spawnPosition, Quaternion.identity );
        newPlatform.transform.SetParent(transform);
```

这样就可以让新生成的平台作为平台出生点的子物体

5.为了不让链锤连续生成太多以至于游戏难度过大，修改spawn代码：

```
    int spikeBallNum = 0;
    
        public void CreatePlatform()
    {
        int index = Random.Range(0, platform.Count);

        if (index == 3)
        {
            spikeBallNum++;
            if(spikeBallNum > 2)
            {
                index = 0;
                spikeBallNum = 0;
            }
        }

        GameObject newPlatform = Instantiate(platform[index], spawnPosition, Quaternion.identity );
        newPlatform.transform.SetParent(transform);
    }
```

当连续出现链锤时变成普通平台，免得出现必死局



---



### 	终、总结

到此为止主要游戏内容都已经完成啦！最后就是制作一些UI管理游戏
