---
title: 《Robbie Swifthand》Unity2d独立游戏学习（五）
date: 2021-07-30 20:37:14
categories:	unity
tags:
  - unity
  - c#
  - animator
  - audio
---



### 	始、前言

一个小知识，预制体放入场景中后，如果在场景中的物体修改完想同步到预制体，需要选择场景中物体的 Inspector → Prefab → Overrides → Apply All

<!--more-->

---



### 	一、角色动画

1.打开Robbie 文件夹将资源里的材质拖拽到场景中主角的子物体中，并关闭原父物体的Render

2.发现新加的body 里骨骼的Sorting Layer 全是Default ，直接修改Sorting Layer 的顺序好了，把Default 移动到背景的下方，这样就会显示在背景的前面

3.打开body的Animator 看看，已经有几个变量和连接好的状态机

4.新建脚本PlayerAnimation 挂到body上 ，先获取组件和变量：

```
	Animator anim;
    PlayerMovement movement;
    Rigidbody2D rb;
    
    void Start()
    {
        anim = GetComponent<Animator>();
        movement = GetComponentInParent<PlayerMovement>();
        rb = GetComponentInParent<Rigidbody2D>();
    }
```

GetComponentInParent可以获取父级的组件

状态机中已经添加了很多动画效果，但是跳跃的还没有，那现在就来自己添加跳跃的动画效果



5.打开混合树Mid Air，同时打开资源中动画资源的MId Air文件夹，给混合树添加7个MId Air的动画资源，设置参考变量为verticalVelocity，然后按照动作设置好各自的值（值的大小就参照跳跃时Rigidbody的Velocity.y 的值）



6..获取角色移动代码PlayerMovement中的变量，记得先改成Public类型。然后用编号法给动画变量赋值：

```
    //动画状态编号
    int groundID;
    int speedID;
    int crouchID;
    int hangingID;
    int fallID;
    
    void Start()
    {
        groundID = Animator.StringToHash("isOnGround");
        speedID = Animator.StringToHash("speed");
        hangingID = Animator.StringToHash("isHanging");
        crouchID = Animator.StringToHash("isCrouching");
        fallID = Animator.StringToHash("verticalVelocity");
    }
    
    void Update()
    {
        anim.SetFloat(speedID, Mathf.Abs(movement.xVelocity));//撞墙也会继续跑
        /*anim.SetFloat(speedID, Mathf.Abs(rb.velocity.x));*/ //撞墙停下
        anim.SetBool(groundID, movement.isOnGround);
        anim.SetBool(crouchID, movement.isCrouch);
        anim.SetBool(hangingID, movement.isHanging);
        anim.SetFloat(fallID, rb.velocity.y);
    }
```

Animator.StringToHash() 可以获取变量在变量列表中的编号

为什么官方推荐用编号来赋值呢？给出的解释是这样可以避免移动端可能产生的一些错误



---



### 二、音效控制

在角色的走路和下蹲走路的动画中都插入了一些音效，模拟脚步的声音，现在就来补上这些音效

1.在PlayerAnimation 脚本中添加对应的发声函数：

```
    public void StepAudio()
    {
        AudioManager.PlayFootstepAudio();
    }

    public void CrouchStepAudio()
    {
        AudioManager.PlayCrouchFootstepAudio();
    }
```

名字不同的话记得修改动画中调用的函数



2.将Audios文件夹中的预制体Audio Manager加入场景中

3.新建AudioManager脚本挂在Audio Manager物体中（绕口令？），先新建一些变量来存放音效资源：

```
    static AudioManager current;

    [Header("环境声音")]
    public AudioClip ambientClip;
    public AudioClip musicClip;

    [Header("主角音效")]
    public AudioClip[] walkStepClips;
    public AudioClip[] crouchStepClips;
    public AudioClip jumpClip;
    public AudioClip jumpVoiceClip;
```

然后将资源里的音效资源拖拽到相应的变量位置，这样就可以在代码中控制音效播放



4.我们需要在游戏一开始就处理好音效的事情，所以用Awake函数来进行各种设置：

```
    private void Awake()
    {
        current = this;

        //切换场景也不会销毁这个物体
        DontDestroyOnLoad(gameObject);
    }
```



5.为了避免在属性界面中处理太多的组件，选择在代码中添加组件，这样有助于同一管理并且减少失误的发生：

```
    //声音播放组件
    AudioSource ambientSource;
    AudioSource musicSource;
    AudioSource fxSource;
    AudioSource playerSource;
    AudioSource voiceSource;
    
    private void Awake()
    {
        ambientSource = gameObject.AddComponent<AudioSource>();
        musicSource = gameObject.AddComponent<AudioSource>();
        fxSource = gameObject.AddComponent<AudioSource>();
        playerSource = gameObject.AddComponent<AudioSource>();
        voiceSource = gameObject.AddComponent<AudioSource>();
    }
```



6.编写背景声音的发声函数：

```
    void StartLevelAudio()
    {
        current.ambientSource.clip = current.ambientClip;
        current.ambientSource.loop = true;
        current.ambientSource.Play();

        current.musicSource.clip = current.musicClip;
        current.musicSource.loop = true;
        current.musicSource.Play();
    }
```

在本脚本Awake()调用，一开始运行游戏就有背景声音



7.编写脚步声音的两种发声函数：

```
    public static void PlayFootstepAudio()
    {
        int index = Random.Range(0, current.walkStepClips.Length);

        current.playerSource.clip = current.walkStepClips[index];
        current.playerSource.Play();
    }    
    
    public static void PlayCrouchFootstepAudio()
    {
        int index = Random.Range(0, current.crouchStepClips.Length);

        current.playerSource.clip = current.crouchStepClips[index];
        current.playerSource.Play();
    }
```

在PlayerAnimation脚本中调用，因为这是在动画中调用的函数：

```
    public void StepAudio()
    {
        AudioManager.PlayFootstepAudio();
    }

    public void CrouchStepAudio()
    {
        AudioManager.PlayCrouchFootstepAudio();
    }
```



8.编写跳跃声音的发声函数：

```
    public static void PlayJumpAudio()
    {
        current.playerSource.clip = current.jumpClip;
        current.playerSource.Play();

        current.voiceSource.clip = current.jumpVoiceClip;
        current.voiceSource.Play();
    }
```

其中包括了起跳的脚步声和人物声，在PlayerMovement脚本中的跳跃函数中的起跳部分调用：

```
    void MidAirMovement()//空中移动
    {
        if(jumpPressed && isOnGround && !isJump && !isHeadBlocked)//起跳
        {
            AudioManager.PlayJumpAudio();
		}
	}
```



---



### 终、本节总结

测试游戏，移动时发现主角的反光情况有些不对劲，原因是角色移动时图案的翻转代码中用的是二维的向量，这样就会使得z 轴大小变为0 ，即厚度没了人物瘪了光也反不了了，要改成三维向量：

```
    void flipDirection()//改变角色图案方向
    {
        if (xVelocity > 0) transform.localScale = new Vector3 (1, 1, 1);
        else if (xVelocity < 0) transform.localScale = new Vector3(-1, 1, 1);
    }
```



下面记录一下这次新增的两个脚本内容：

PlayerAnimation脚本：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerAnimation : MonoBehaviour
{

    Animator anim;
    PlayerMovement movement;
    Rigidbody2D rb;

    //动画状态编号
    int groundID;
    int speedID;
    int crouchID;
    int hangingID;
    int fallID;

    void Start()
    {
        anim = GetComponent<Animator>();
        movement = GetComponentInParent<PlayerMovement>();
        rb = GetComponentInParent<Rigidbody2D>();

        groundID = Animator.StringToHash("isOnGround");
        speedID = Animator.StringToHash("speed");
        hangingID = Animator.StringToHash("isHanging");
        crouchID = Animator.StringToHash("isCrouching");
        fallID = Animator.StringToHash("verticalVelocity");
    }

    void Update()
    {
        anim.SetFloat(speedID, Mathf.Abs(movement.xVelocity));//撞墙也会继续跑
        /*anim.SetFloat(speedID, rb.velocity.x);*/ //撞墙停下
        anim.SetBool(groundID, movement.isOnGround);
        anim.SetBool(crouchID, movement.isCrouch);
        anim.SetBool(hangingID, movement.isHanging);
        anim.SetFloat(fallID, rb.velocity.y);
    }

    public void StepAudio()
    {
        AudioManager.PlayFootstepAudio();
    }

    public void CrouchStepAudio()
    {
        AudioManager.PlayCrouchFootstepAudio();
    }
}

```



AudioManager脚本：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

using UnityEngine.Audio;

public class AudioManager : MonoBehaviour
{
    static AudioManager current;

    [Header("环境声音")]
    public AudioClip ambientClip;
    public AudioClip musicClip;

    [Header("主角音效")]
    public AudioClip[] walkStepClips;
    public AudioClip[] crouchStepClips;
    public AudioClip jumpClip;
    public AudioClip jumpVoiceClip;

    //声音播放组件
    AudioSource ambientSource;
    AudioSource musicSource;
    AudioSource fxSource;
    AudioSource playerSource;
    AudioSource voiceSource;

    private void Awake()
    {
        current = this;

        //切换场景也不会销毁这个物体
        DontDestroyOnLoad(gameObject);

        ambientSource = gameObject.AddComponent<AudioSource>();
        musicSource = gameObject.AddComponent<AudioSource>();
        fxSource = gameObject.AddComponent<AudioSource>();
        playerSource = gameObject.AddComponent<AudioSource>();
        voiceSource = gameObject.AddComponent<AudioSource>();

        StartLevelAudio();
    }

    void StartLevelAudio()
    {
        current.ambientSource.clip = current.ambientClip;
        current.ambientSource.loop = true;
        current.ambientSource.Play();

        current.musicSource.clip = current.musicClip;
        current.musicSource.loop = true;
        current.musicSource.Play();
    }

    public static void PlayFootstepAudio()
    {
        int index = Random.Range(0, current.walkStepClips.Length);

        current.playerSource.clip = current.walkStepClips[index];
        current.playerSource.Play();
    }    
    
    public static void PlayCrouchFootstepAudio()
    {
        int index = Random.Range(0, current.crouchStepClips.Length);

        current.playerSource.clip = current.crouchStepClips[index];
        current.playerSource.Play();
    }

    public static void PlayJumpAudio()
    {
        current.playerSource.clip = current.jumpClip;
        current.playerSource.Play();

        current.voiceSource.clip = current.jumpVoiceClip;
        current.voiceSource.Play();
    }
}
```

