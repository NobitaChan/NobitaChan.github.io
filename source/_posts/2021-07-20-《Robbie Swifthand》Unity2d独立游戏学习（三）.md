---
title: 《Robbie Swifthand》Unity2d独立游戏学习（三）
date: 2021-07-20 15:36:35
categories: unity
tags:
  - unity
  - c#
  - raycast
---





### 	始、前言

简单的角色移动已经完成了，这次来学习用Raycast 射线检测的方法让角色的状态检测变得更细致



<!--more-->

---



### 	一、Physics2D.Raycast 脚本API

```
public static RaycastHit2D Raycast (Vector2 origin, Vector2 direction, float distance= Mathf.Infinity, int layerMask= DefaultRaycastLayers, float minDepth= -Mathf.Infinity, float maxDepth= Mathf.Infinity);
```

参数

| origin    | 射线在 2D 空间中的起点。                    |
| --------- | ------------------------------------------- |
| direction | 表示射线方向的矢量。                        |
| distance  | 射线的最大投射距离。                        |
| layerMask | 过滤器，用于仅在特定层上检测碰撞体。        |
| minDepth  | 仅包括 Z 坐标（深度）大于或等于该值的对象。 |
| maxDepth  | 仅包括 Z 坐标（深度）小于或等于该值的对象。 |

返回**`RaycastHit2D`** 返回的投射数量。

描述：

向场景中的碰撞体投射射线。

从概念上说，*射线投射* 类似于从空间中的某个点朝特定方向发射一条光束。在该过程中，可以检测并报告与光束接触的任何对象。

函数返回一个 `RaycastHit` 对象，该对象引用了射线命中的碰撞体（如果未命中任何对象，则结果的碰撞体属性将为 NULL）。*`layerMask`* 可用于仅在特定层上有选择地检测对象（例如，这让您能够仅将检测应用于敌人角色）。

使用 `contactFilter` 的此方法重载可以按 `ContactFilter2D` 中提供的选项筛选结果。

对于确定视线、炮火击中的目标以及游戏中的许多其他目的来说，射线投射很有用。

此外，这还将检测位于射线起点的碰撞体。在这种情况下，射线从碰撞体内部开始，并且不与碰撞体表面交叠。这意味着无法计算碰撞法线，在这种情况下，返回的碰撞法线设置为正在测试的射线向量的倒数。这可轻松检测到，因为此类结果始终是 `RaycastHit2D` 分数为零。



---



### 二、脚部、头顶碰撞

1. 在`[Header("环境检测")]`新建几个需要用到的变量

```
    [Header("环境检测")]
    public LayerMask groundLayer;
    public float footOffset = 0.35f;
    public float headClearance = 0.5f;
    public float groundDistance = 0.2f;
```



2.重写一个射线检测的方法，包含射线可视化

```
    RaycastHit2D Raycast(Vector2 offset, Vector2 rayDirection, float length, LayerMask layer)//自己封装的射线检测方法
    {
        Vector2 pos = transform.position;
        RaycastHit2D hit = Physics2D.Raycast(pos + offset, rayDirection, length, layer);
        Debug.DrawRay(pos + offset, rayDirection, hit ? Color.red : Color.green);
        return hit;
    }
```



3.更改地面检测函数`PhysicsCheck()`

```
    void PhysicsCheck()//检测是否在地面，并修改状态
    {
        RaycastHit2D leftCheck = Raycast(new Vector2(-footOffset, 0.2f), Vector2.down, groundDistance, groundLayer);
        RaycastHit2D rightCheck = Raycast(new Vector2(footOffset, 0.2f), Vector2.down, groundDistance, groundLayer);
        isOnGround = leftCheck || rightCheck;

    }
```

保存回到unity，运行

角色碰撞体左右脚下方都出现了射线，并且会按照射线方向检测是否碰撞到地面，以此改变`isOnGround`，可以调整参数让检测距离更 “舒服”

下面来增加头顶的检测



4.在`[Header("角色状态")]`中增加头顶状态 `public bool isHeadBlocked;`



5.修改`PhysicsCheck()`：

```
    void PhysicsCheck()//检测是否在地面，并修改状态
    {
        RaycastHit2D leftCheck = Raycast(new Vector2(-footOffset, 0.2f), Vector2.down, groundDistance, groundLayer);
        RaycastHit2D rightCheck = Raycast(new Vector2(footOffset, 0.2f), Vector2.down, groundDistance, groundLayer);
        isOnGround = leftCheck || rightCheck;

        RaycastHit2D headCheck = Raycast(new Vector2(0f, cd.size.y), Vector2.up, headClearance, groundLayer);
        isHeadBlocked = headCheck;
    }
```

同样，修改`public`参数就可以调整检测距离（注意射线的长度不等于检测距离）



---



### 三、悬挂检测

判断悬挂需要三条射线，分别是头顶射线，眼部射线，垂直射线。

悬挂在边缘的条件：头顶射线**无**碰撞，眼部射线有碰撞，垂直射线**有**碰撞

1.在`[Header("环境检测")]`添加变量：

```
    [Header("环境检测")]
    //悬挂
    public float playerHeight = 2f;
    public float playerEyeHeight = 1.5f;
    public float grabDistance = 0.2f;
    public float reachOffset = 0.5f;
```



2.在`[Header("角色状态")]`添加状态：

```
	[Header("角色状态")]
    public bool isHanging;
```



3.在`PhysicsCheck()`中添加悬挂的各种射线生成以及悬挂状态判断：

```
    void PhysicsCheck()//物理检测，并修改状态
    {
        //悬挂系列射线
        float direction = transform.localScale.x;
        Vector2 grabDirection = new Vector2(direction, 0f);
        RaycastHit2D blockedCheck = Raycast(new Vector2(direction * footOffset, playerHeight), grabDirection, grabDistance, groundLayer);
        RaycastHit2D wallCheck = Raycast(new Vector2(direction * footOffset, playerEyeHeight), grabDirection, grabDistance, groundLayer);
        RaycastHit2D ledgeCheck = Raycast(new Vector2(direction * reachOffset, playerHeight), Vector2.down, grabDistance, groundLayer);
        if (!isOnGround && !blockedCheck && wallCheck && ledgeCheck && rb.velocity.y < 0f)
        {
            Vector3 pos = transform.position;
            pos.x += (wallCheck.distance - 0.05f) * direction;
            pos.y -= ledgeCheck.distance;
            transform.position = pos; 
            rb.bodyType = RigidbodyType2D.Static;
            isHanging = true;
        }
    }
```

其中`pos.x += (wallCheck.distance - 0.05f) * direction;`稍微调整一下悬挂的位置，可以按照图案的位置来调整



4.悬挂状态已经设置好了，现在我们希望在悬挂时按跳跃键会有一个固定的跳跃高度，并且会取消刚体的锁定

在`[Header("跳跃参数")]`中添加：

```
	[Header("跳跃参数")]
    public float hangingJumpForce = 15f;
```



5.在`MidAirMovement()`添加悬挂跳跃：

```
    void MidAirMovement()//空中移动
    {
        if (isHanging)//悬挂时两种选择
        {
            if (jumpPressed)
            {
                rb.bodyType = RigidbodyType2D.Dynamic;
                rb.AddForce(new Vector2(0f, hangingJumpForce), ForceMode2D.Impulse);
                //rb.velocity = new Vector2(rb.velocity.x, hangingJumpForce);
                isHanging = false;
            }
            if (crouchPressed)
            {
                rb.bodyType = RigidbodyType2D.Dynamic;
                crouchPressed = false;
                isHanging = false;
            } 
        }
    }
```

其中`crouchPressed`要在`[Header("按键状态")]`中设置好，并且在`Update()`中赋值，以此来和`crouchHeld`分开



---



### 	终、阶段总结

到此为止，已经完成了各种基础运动的逻辑

自我感觉有些状态的判断没有很好的归类，比如悬挂状态，应该和下蹲&站立一样新建一个悬挂的函数，在悬挂函数中完成悬挂的各种处理，最后使`isHanging = true`，这样代码应该会比较 ”整齐”
