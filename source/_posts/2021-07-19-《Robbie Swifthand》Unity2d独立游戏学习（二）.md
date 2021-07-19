---
title: 《Robbie Swifthand》Unity2d独立游戏学习（二）
date: 2021-07-19 15:23:30
categories: unity
tags:
  - unity
  - c#
  - update
  - time
---



### 	一、添加物理组件

1.给Platforms 添加Tilemap Collider 2D，勾选Used By Composite，添加Composite Collider 2D

2.将Platforms自动添加的Rigidbody 2D 选择成Static 模式

3.给主角添加Rigidbody 2D，把Collision Detection选择成Continuous连续不断地检测碰撞，把Interpolate 设置成Interpolate产生碰撞时会有一个缓冲的效果

4.添加Box Collider 2D，调整主角碰撞器大小，size - y 最好设置在 2 以下，这样就可以通过两格高的空间

5.运行，发现在角落处下落会导致角色翻转，需要在主角Rigidbody 2D 中锁定 z 轴

6.为了避免角色碰到墙壁时会卡进墙体里，给主角Box Collider 2D添加Material 物理材质Teflon（资源包里的，摩擦力和弹力都是0）

7.新增Layer ，主角设置成Player，Platforms 设置成Ground，以供后面进行碰撞检测



<!--more-->



---



### 二、角色移动

1.在Scripts文件夹新建PlayerMovement脚本，编写代码：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    private Rigidbody2D rb;

    [Header("移动参数")]
    public float speed = 10f;
    public float crouchSpeedDivisor = 5f;
    private float xVelocity;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()//每帧调用
    {
        
    }

    private void FixedUpdate()//每一段时间调用，默认0.02s
    {
        GroundMovement();
    }

    void GroundMovement()
    {
        xVelocity = Input.GetAxis("Horizontal");//-1f ~ 1f
        //在FixedUpdate()中调用无需Time.deltatime
        rb.velocity = new Vector2(xVelocity * speed, rb.velocity.y);
        flipDirection();
    }


    void flipDirection()
    {
        if (xVelocity > 0) transform.localScale = new Vector2 (1, 1);
        else if (xVelocity < 0) transform.localScale = new Vector2(-1, 1);
    }
}
```

键盘左右键可以让主角左右移动，并且会改变图案方向



---



### 三、下蹲

1.打开Edit - Project Settings - Input ，复制Jump 命名为Crouch，Positive Button 改为down，Alt Positive Button 改为 s

2.修改角色移动代码：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    private Rigidbody2D rb;
    private BoxCollider2D cd;

    [Header("移动参数")]
    public float speed = 10f;
    public float crouchSpeedFactor = 0.5f;
    private float xVelocity;

    [Header("角色状态")]
    public bool isCrouch;

    //碰撞体尺寸
    private Vector2 crouchOffset;
    private Vector2 crouchSize;
    private Vector2 standOffset;
    private Vector2 standSize;


    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        cd = GetComponent<BoxCollider2D>();

        standOffset = cd.offset;
        standSize = cd.size;
        crouchOffset = new Vector2(standOffset.x, standOffset.y * 0.5f);
        crouchSize = new Vector2(standSize.x, standSize.y * 0.5f);
    }


    void Update()//每帧调用
    {
        
    }


    private void FixedUpdate()//每一段时间调用，默认0.02s
    {
        GroundMovement();
    }
    void GroundMovement()//移动的各种数值
    {
        xVelocity = Input.GetAxis("Horizontal");//-1f ~ 1f
        if (Input.GetButton("Crouch")) Crouch();
        else if (isCrouch) StandUp();

        //在FixedUpdate()中调用无需Time.deltatime
        rb.velocity = new Vector2(xVelocity * speed, rb.velocity.y);
        flipDirection();
    }
    void flipDirection()//改变角色图案方向
    {
        if (xVelocity > 0) transform.localScale = new Vector2 (1, 1);
        else if (xVelocity < 0) transform.localScale = new Vector2(-1, 1);
    }

    void Crouch()//下蹲时的各种改变
    {
        isCrouch = true;
        xVelocity *= crouchSpeedFactor;
        cd.size = crouchSize;
        cd.offset = crouchOffset;
    }
    void StandUp()//从下蹲转变为站立时各种变化
    {
        isCrouch = false;
        xVelocity /= crouchSpeedFactor;
        cd.size = standSize;
        cd.offset = standOffset;
    }
}
```

顺便给每个函数添加了注释

现在，按下s键或者方向下键就会让角色的碰撞体 “蹲下" （移动速度变慢并且碰撞体高度减半）



测试一下确实可以通过高度为1的障碍了，但是在经过障碍的途中如果松开下蹲键，就会穿模卡在墙里，因为缺少了在通过障碍时需要持续下蹲的判断，这一部分放到后面再处理



---



### 四、跳跃

1.打开Edit - Project Setting - Physics 2D，将 y 轴重力改大一点，这里改为-50

2.先添加与跳跃有关的参数：

```
    [Header("跳跃参数")]
    public float jumpForce = 5f;
    public float jumpHoldForce = 3f;
    public float jumpHoldDuration = 0.1f;
    public float crouchJumpBoost = 3f;
    float jumpTime;
```

其中jumpTime 是允许长按跳跃加高的时间



3.添加判断各种状态的参数：

```
    [Header("按键状态")]
    public bool jumpPressed;
    public bool jumpHeld;
    public bool crouchHeld;

    [Header("角色状态")]
    public bool isCrouch;
    public bool isOnGround;
    public bool isJump;

    [Header("环境检测")]
    public LayerMask groundLayer;
```

同时可以更新一下之前的下蹲判断，用刚刚定义的状态参数



4.在Update（）中获取按键信息：

```
    void Update()//每帧调用
    {
        //GetButtonDown不能直接等于，FixedUpdate中的函数可能会检测不到Update中的变化
        if (Input.GetButtonDown("Jump")) jumpPressed = true;

        jumpHeld = Input.GetButton("Jump");
        crouchHeld = Input.GetButton("Crouch");
    }
```



5.添加判断是否在地面上：

```
	void PhysicsCheck()//检测是否在地面，并修改状态
    {
        isOnGround = cd.IsTouchingLayers(groundLayer);

    }
```

记得在FixedUpdate（）中调用，物理性质都推荐放在FixedUpdate中



6.添加跳跃函数：

```
    void MidAirMovement()//跳跃移动
    {
        if(jumpPressed && isOnGround && !isJump)//起跳
        {
            if(crouchHeld)//判断是否要蹲跳
            {
                rb.AddForce(new Vector2(0f, crouchJumpBoost), ForceMode2D.Impulse);
            }
            jumpTime = Time.time + jumpHoldDuration;
            rb.AddForce(new Vector2(0f, jumpForce), ForceMode2D.Impulse);
            jumpPressed = false;
            isJump = true;
        }

        else if(isJump)
        {
            if (jumpHeld) rb.AddForce(new Vector2(0f, jumpHoldForce), ForceMode2D.Impulse);
            if (jumpTime < Time.time) isJump = false;
        }

    }
```

精髓就是`jumpTime = Time.time + jumpHoldDuration;`，`if (jumpTime < Time.time) isJump = false;` 

这两句代码可以很好地对能否在空中持续加速进行判断



---



### 终、总结

这次的跳跃代码真的收获不少，Update和FixedUpdate可以理解成完全独立的两个函数

这一波折腾下来角色控制脚本 PlayerMovement 有点长了，这里先全部放上来记录一下，以便日后回顾

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    //组件
    private Rigidbody2D rb;
    private BoxCollider2D cd;

    [Header("移动参数")]
    public float speed = 10f;
    public float crouchSpeedFactor = 0.5f;
    float xVelocity;

    [Header("跳跃参数")]
    public float jumpForce = 5f;
    public float jumpHoldForce = 3f;
    public float jumpHoldDuration = 0.1f;
    public float crouchJumpBoost = 3f;
    float jumpTime;

    [Header("按键状态")]
    public bool jumpPressed;
    public bool jumpHeld;
    public bool crouchHeld;

    [Header("角色状态")]
    public bool isCrouch;
    public bool isOnGround;
    public bool isJump;

    [Header("环境检测")]
    public LayerMask groundLayer;

    //碰撞体尺寸
    private Vector2 crouchOffset;
    private Vector2 crouchSize;
    private Vector2 standOffset;
    private Vector2 standSize;

    void Start()
    {
        //获取组件
        rb = GetComponent<Rigidbody2D>();
        cd = GetComponent<BoxCollider2D>();

        //初始化
        standOffset = cd.offset;
        standSize = cd.size;
        crouchOffset = new Vector2(standOffset.x, standOffset.y * 0.5f);
        crouchSize = new Vector2(standSize.x, standSize.y * 0.5f);
    }


    void Update()//每帧调用
    {
        //GetButtonDown不能直接等于，FixedUpdate中的函数可能会检测不到Update中的变化
        if (Input.GetButtonDown("Jump")) jumpPressed = true;

        jumpHeld = Input.GetButton("Jump");
        crouchHeld = Input.GetButton("Crouch");
    }


    private void FixedUpdate()//每一段时间调用，unity默认0.02s
    {
        PhysicsCheck();
        GroundMovement();
        MidAirMovement();
    }

    void PhysicsCheck()//检测是否在地面，并修改状态
    {
        isOnGround = cd.IsTouchingLayers(groundLayer);

    }


    void GroundMovement()//移动的各种数值
    {
        xVelocity = Input.GetAxis("Horizontal");//-1f ~ 1f
        if (crouchHeld && !isCrouch && isOnGround) Crouch();
        else if ((isCrouch && !crouchHeld) || isJump) StandUp();


        //在FixedUpdate()中调用无需Time.deltatime
        rb.velocity = new Vector2(xVelocity * speed, rb.velocity.y);
        flipDirection();
    }

    void MidAirMovement()//跳跃移动
    {
        if(jumpPressed && isOnGround && !isJump)//起跳
        {
            if(crouchHeld)//判断是否要蹲跳
            {
                rb.AddForce(new Vector2(0f, crouchJumpBoost), ForceMode2D.Impulse);
            }
            jumpTime = Time.time + jumpHoldDuration;
            rb.AddForce(new Vector2(0f, jumpForce), ForceMode2D.Impulse);
            jumpPressed = false;
            isJump = true;
        }

        else if(isJump)
        {
            if (jumpHeld) rb.AddForce(new Vector2(0f, jumpHoldForce), ForceMode2D.Impulse);
            if (jumpTime < Time.time) isJump = false;
        }

    }

    void flipDirection()//改变角色图案方向
    {
        if (xVelocity > 0) transform.localScale = new Vector2 (1, 1);
        else if (xVelocity < 0) transform.localScale = new Vector2(-1, 1);
    }

    void Crouch()//下蹲时的各种改变
    {
        isCrouch = true;
        xVelocity *= crouchSpeedFactor;
        cd.size = crouchSize;
        cd.offset = crouchOffset;
    }

    void StandUp()//从下蹲转变为站立时各种变化
    {
        isCrouch = false;
        xVelocity /= crouchSpeedFactor;
        cd.size = standSize;
        cd.offset = standOffset;
    }
}
```

