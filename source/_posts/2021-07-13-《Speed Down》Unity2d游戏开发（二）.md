---
title: 《Speed Down》Unity2d游戏开发（二）
date: 2021-07-13 15:50:11
categories: unity
tags:
  - unity
  - c#
  - joint
  - collider
---



### 	始、前言

上一次已经完成了场景搭建，那么这次的主要任务就是制作跳跃平台



<!--more-->

---



### 	一、普通平台

1.选中pixel adventure 1 → assets → traps → falling platform 设置pixels per unit为16

2.拖拽第一张图案到场景中，命名为BasePlatform

3.新建BasePlatform的动画，将四个图案拖拽进动画中并调整采样率

4.为BasePlatform添加碰撞器box collider 2d 并调整碰撞体积



---



### 	二、旋转平台

1.前面的操作与上述普通平台相同，设置大小，命名，新建动画，添加碰撞器

2.为了实现平台被碰撞时发生翻转，添加组件hinge joint 2d，可以设置旋转角度和速度等

自动添加了rigidbody 2d，但是不取消也不会掉下来，为什么呢？

猜测是joint关节会固定在背景里，超过一定的力才会 “脱臼”



---



### 	三、弹跳平台

1.找到pixel adventure 1 → assets → traps → fan ，设置大小，命名，添加碰撞器

2.这里新建两个动画，一个是静止时的动画，一个是启动时的动画（弹跳）

3.新建脚本，实现碰撞时平台切换到弹跳动画：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class FanRun : MonoBehaviour
{
    Animator animator;
    void Start()
    {
        animator = GetComponent<Animator>();
    }
    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Player"))
        {
            animator.Play("Fan_run");
        }
    }
}
```

当标签为Player的物体碰撞到此平台时就会切换到启动动画啦



---



### 	四、摆动链锤

1.新建空物体SpikeBall，分别添加两个子物体pixel adventure 1 → assets → traps → spiked ball → 轴心和大锤

2.调整父子物体的相对位置

3.给大锤添加组件Distance Joint 2D，给轴心添加rigid body 2d并拖拽到大锤的链接体

4.给大锤添加碰撞体circle collider 2d



---



### 	五、脚本实现平台上升

新建脚本：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlatformUp : MonoBehaviour
{
    Vector3 movement;
    public float speed;
    void Start()
    {
        movement.y = speed;
    }
    void Update()
    {
        MovePlatform();
    }
    void MovePlatform()
    {
        transform.position += movement * Time.deltaTime;
    }
}
```

并挂载到所有的平台建筑下，这样就实现了平台的持续上升



---



### 	六、设置平台天花板

平台总不能一直上升，要设置一个天花板，当平台到达天花板时就摧毁物体

1.新建空物体TopLine移动到上方合适的位置，作为天花板

2.在平台的上升代码中添加判断到达天花板的检测：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlatformUp : MonoBehaviour
{
    Vector3 movement;
    public float speed;
    GameObject topLine;

    void Start()
    {
        movement.y = speed;
        topLine = GameObject.Find("TopLine");
    }
    void Update()
    {
        MovePlatform();
    }

    void MovePlatform()
    {
        transform.position += movement * Time.deltaTime;
        if(transform.position.y > topLine.transform.position.y)
        {
            Destroy(gameObject);
        }
    }
}
```

完成！平台到位自动销毁



---



### 	终、总结

平台的制作到这就结束了！用到了几个joint组件，十分有趣

