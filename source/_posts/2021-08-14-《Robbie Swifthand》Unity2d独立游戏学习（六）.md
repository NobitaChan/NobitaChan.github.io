---
title: 《Robbie Swifthand》Unity2d独立游戏学习（六）
date: 2021-08-14 14:27:04
categories:	unity
tags:
  - c#
  - unity
  - tilemap
  - audio
---



### 	始、前言

距离上次琢磨unity已经半个月了，希望没有生疏（打开unity已经有点懵逼了）

<!--more-->

`^(*￣(oo)￣)^`



---



### 	一、死亡机制

1.找到Props文件夹中的尖刺Spikes，可以逐个添加并且对齐，但没必要

2.打开Tile Palette，在左下角有个选择笔刷的选项，选择GameObject Brush并且在Cells中添加尖刺的预制体Spikes

3.Active Tilemap选择Platforms，就可以在场景中快乐涂鸦啦，生成的尖刺会成为Platforms物体的子物体

4.新建Traps的Layer，给尖刺更换Layer，利用预制体Apply All

5.新建脚本PlayerHealth，挂到Robbie上，编写代码：

```
public class PalyerHealth : MonoBehaviour
{
    int trapsLayer;
    void Start()
    {
        trapsLayer = LayerMask.NameToLayer("Traps");
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.layer == trapsLayer)
        {
            gameObject.SetActive(false);
        }
    }
}
```

这里当角色触碰到尖刺时就会被摧毁消失



7.添加死亡烟雾效果：

```
public class PalyerHealth : MonoBehaviour
{
    public GameObject deathVFXPrefab;
    
    private void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.layer == trapsLayer)
        {
            Instantiate(deathVFXPrefab, transform.position, transform.rotation);
        }
    }
}
```

找到烟雾特效并添加进来，在角色死亡时顺便用Instantiate(）播放烟雾特效



8.在Audio Manage脚本中添加代码：

```
    [Header("主角音效")]
    //动作声
    public AudioClip deathClip;
    //说话声
    public AudioClip deathVoiceClip;

    [Header("特效音效")]
    public AudioClip deathVFXClip;
```

找到对应声音素材并添加到变量中



9.继续修改Audio Manage脚本：

```
    public static void PlayDeathAudio()
    {
        current.playerSource.clip = current.deathClip;
        current.playerSource.Play();

        current.voiceSource.clip = current.deathVoiceClip;
        current.voiceSource.Play();

        current.fxSource.clip = current.deathVFXClip;
        current.fxSource.Play();
    }
```

和之前的声音播放方法一样，添加音效并且播放音效



10.在PlayerHealth中调用死亡声音：

```
    private void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.layer == trapsLayer)
        {
            AudioManager.PlayDeathAudio();
        }
    }
```



11.死亡后重置场景：

```
using UnityEngine.SceneManagement;

	private void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.layer == trapsLayer)
        {
            SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
        }
    }
```

并且在Unity中找到File → Build Setting 并且点击Add Open Scenes，这样就会在角色死亡时重置当前场景



测试了几次发现虽然实现了重置场景时不会摧毁声音管理，但是这个声音管理又会无限重复生成，暂时不知道怎么解决，先把`DontDestroyOnLoad(gameObject);`注释掉，嘿嘿



---



### 二、收集物体

1.在Props中找到宝珠预制体Shrine，同上的方法添加到Brush中并且随意画到场景中

2.更改Shrine的Layer成为Ground，并且应用到预制体中

3.新建脚本Orb挂到orb物体中：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Orb : MonoBehaviour
{
    int player;
    void Start()
    {
        player = LayerMask.NameToLayer("Player");
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.gameObject.layer == player)
        {
            gameObject.SetActive(false);
        }
    }
}
```

就像角色触碰尖刺就消失一样，这里当角色接触宝珠就会让宝珠消失（记得检查一下角色的Layer是不是Player）



4.添加爆炸特效：

```

public class Orb : MonoBehaviour
{
    public GameObject exposionVFXPrefab;
    void Start()
    {
        player = LayerMask.NameToLayer("Player");
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.gameObject.layer == player)
        {
            Instantiate(exposionVFXPrefab, transform.position, transform.rotation);
        }
    }
}

```

在VFX文件夹中找到Explosion添加到变量中



5.添加音效，在AudioManage脚本中添加：

```
    [Header("主角音效")]
    //说话声
    public AudioClip orbVoiceClip;

    [Header("特效音效")]
    public AudioClip orbFXClip;
    
        public static void PlayOrbAudio()
    {
        current.voiceSource.clip = current.orbVoiceClip;
        current.voiceSource.Play();

        current.fxSource.clip = current.orbFXClip;
        current.fxSource.Play();
    }
```

将音效素材添加到变量中，并且记得在Orb脚本中调用：

```
    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.gameObject.layer == player)
        {
            AudioManager.PlayOrbAudio();
        }
    }
```



---



### 终、总结

本次主要添加了场景中主要的角色互动物体，分别是让游戏结束的**尖刺**和游戏目标需要收集的**宝珠**，总体来说两个物体的添加、与角色之间的互动制作都比较相似
