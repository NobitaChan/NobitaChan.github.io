---
title: 《Speed Down》Unity2d游戏开发（三）
date: 2021-07-14 16:16:29
categories:	unity
tags:
  - unity
  - c#
  - animator
  - trigger
  - collider
---



### 	始、前言

背景和障碍物都搭建完毕了，现在来制作我们操作的主角！（洗个衣服先...）



<!---more--->

---



### 	一、添加动画及组件

1.先选中资源中喜欢的角色，修改所有精灵图的ppu为32

2.制作动画，idle、run、jump、hit

3.添加组件rigidbody 2d并且勾选constraints → freeze rotation → z 以此锁定z轴，防止碰撞时产生z轴翻转

4.添加碰撞组件box collider 2d



---



### 	二、脚本控制主角

添加脚本：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    Rigidbody2D rb;
    Animator anim;
    public float speed;
    float xVelocity;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        anim = GetComponent<Animator>();
    }

    void Update()
    {
        Movement();
    }

    void Movement()
    {
        xVelocity = Input.GetAxisRaw("Horizontal");
        rb.velocity = new Vector2(xVelocity * speed, rb.velocity.y);

        if(xVelocity != 0)
        {
            transform.localScale = new Vector3(xVelocity, 1, 1);
        }
    }
}
```

挂载到角色上，就可以用方向键控制左右移动了（wsad也可以）

并且`transform.localScale = new Vector3(xVelocity, 1, 1);`还实现了图案的左右翻转（之前我还傻傻地自己画各个方向的图）



---



### 	三、切换跑步动画

1.打开player的Animator界面，相互连接idle和run，新建speed变量，设置idle转换为run的条件为speed大于0.1，run转换为idle的条件为speed小于0.1，同时记得调整转换时间和混合时间等设置（这里全部取消）

2.在`void Movement()`中添加代码：

```
anim.SetFloat("speed", Mathf.Abs(rb.velocity.x));
```



---



### 	四、切换下落动画

1.动画all和idle相互连接，创建isOnGround变量来检测是否在平台上，把所有平台的Layer都改成新的Platform

2.添加代码：

```
    public bool isOnGround;
    public float checkRadius;
    public LayerMask layer;
    public GameObject groundCheck;
    
    void Update()
    {
        Movement();
        isOnGround = Physics2D.OverlapCircle(groundCheck.transform.position, checkRadius, layer);
        anim.SetBool("isOnGround", isOnGround);
    } 
```

3.在角色物体下方添加子物体CheckPoint并拖拽入角色的检测点位置

4.修改参数就可以实现踩到平台时触发isOnGround，并且由fall动画转换到idle动画

但是现在看不见检测范围，只能大致估计，怎么办呢？

小技巧：添加代码：

```
    private void OnDrawGizmosSelected()
    {
        Gizmos.color = Color.green;
        Gizmos.DrawWireSphere(groundCheck.transform.position, checkRadius);
    }
```

上述代码是unity内置的方法，可以把检测范围可视化

---



### 	五、切换死亡动画

1.修改尖刺的tag为新的Spike，状态机新建trigger型变量isDead，将Any State 连接到hit调整转换条件，这样就可以从任何状态转换到死亡状态

2.添加代码：

```
    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.CompareTag("Spike"))
        {
            anim.SetTrigger("isDead");
        }
    }
```

这样就实现了播放死亡动画，接下来再加一个表示角色死亡的标志

3.添加代码：

```
    public bool playerDead;
    
    public void PlayerDead()
    {
        playerDead = true;
    }
```

4.找到玩家死亡的动画效果，在最后一帧添加函数`PlayerDead()`



---



### 	六、实现跳跃平台的跳跃效果

1.设置跳跃平台的tag为新的Fan

2.添加代码：

```
    public float bounceH;
    
    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Fan"))
        {
            rb.velocity = new Vector2(rb.velocity.x, bounceH);
        }
    }
```

这样以来，角色碰到跳跃平台时就会给rigidbody产生向上的大小为bounceH的力（就是被打飞了）

发现之前说好的跳跃平台碰到角色会转起来现在好像没有转，原来是主角的tag没有设置成Player

完美！



---



### 	终、吐槽

角色的各种动画转换也太复杂了！难道每次添加一个新的动作就要和所有的动作一起调整吗，天啊！有没有简单的方法啊，快浇浇我吧！

