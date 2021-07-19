---
title: 《Speed Down》Unity2d游戏开发（完）
date: 2021-07-16 18:02:46
categories: unity
tags:
  - unity
  - c#
  - collider
  - joint
  - trigger
  - prefab
  - canvas
---



### 	始、前言

”《Speed Down》Unity2d游戏开发“系列已经完结，这篇文章主要是复盘这次学习中遇到的问题和一些名词的解释，以供未来的我快速回顾，最后还会附上所有的脚本



<!--more-->

---



### 	一、素材

素材这块完全不用自己制作嘛，光是这个Assetstore就有挺多素材的，而且很多无限制商用的，有空要收集一些可以白嫖的网站



---



### 二、2D 复合碰撞体 (Composite Collider 2D)

2D 复合碰撞体组件是用于 2D 物理的碰撞体。与大多数碰撞体不同，此碰撞体没有定义固有的形状。相反，此碰撞体将合并所设置的 2D 盒型碰撞体 (Box Collider 2D) 或 2D 多边形碰撞体 (Polygon Collider 2D) 的形状。2D 复合碰撞体使用所有此类碰撞体的顶点（几何体），并将这些顶点合并为由 2D 复合碰撞体本身控制的新几何体。

2D 盒型碰撞体和 2D 多边形碰撞体组件具有 Used By Composite 复选框。勾选此复选框即可将这些碰撞体附加到 2D 复合碰撞体。这些碰撞体还与 2D 复合碰撞体附加到同一 2D 刚体。启用 Used by Composite 时，其他属性会从该组件中消失，因为这些属性现在由附加的 2D 复合碰撞体控制。



---



### 三、Pixels Per Unit（PPU）

在Inspector面板的图像设置中有一个Pixels Per Unit参数。它代表Unity的一个单位长度包含本图片多少个像素
 在Unity中创建的Cube等基本图形默认就是一个Unity单位长度，一个单位长度在unity世界坐标中代表1米
 例如默认值为100，则代表一个Unity单位包含本图片中100个像素



---



### 四、关节（joint）

关节组件可以添加至多个游戏对象中，而添加关节的游戏对象将通过关节连接在一起并且感觉连带的物理效果。需要注意的是：关节必须依赖于刚体组件。所有 2D 关节的名称都以“2D”结尾。名称不以“2D”结尾的关节是 3D 关节。2D 关节用于 2D 游戏对象，而 3D 关节用于 3D 游戏对象。



**3D关节**：

**链条关节（hinge joint）**：将两个物体以链条的形式绑在一起，当力量大于链条的固定力矩时，两个物体就会产生相互的拉力。

**固定关节（fixed joint）**：将两个物体永远以相对的位置固定在一起，即使发生物理改变，它们之间的相对位置也将不变。

**弹簧关节（spring joint）**：将两个物体以弹簧的形式绑定在一起，挤压它们会得到向外的力，拉伸它们将得到向里的力。

**角色关节（character joint）**：可以模拟角色的骨骼关节。

**可配置关节（configurable joint）**：可以模拟任意关节的效果。



**2D关节**：

**2D 距离关节 (Distance Joint 2D)** - 连接由刚体物理组件控制的两个游戏对象，使这两个对象保持一定距离。

**2D 固定关节 (Fixed Joint 2D)** - 将两个对象保持在相对于彼此的位置，确保对象始终以给定位置和角度偏移。例如，对象好像是采用刚性连接方式相连一样：对象不能彼此远离，不能相互靠近，也不能相对于彼此旋转。还可以使用此关节来创建刚度稍低的柔性连接。

**2D 摩擦关节 (Friction Joint 2D)** - 将刚体物理组件控制的两个游戏对象之间的线速度和角速度降低到零（即：此关节使对象减速并停止）。例如，一个平台在旋转但抵抗该运动。

**2D 铰链关节 (Hinge Joint 2D)** - 允许由刚体物理组件控制的游戏对象连接到空间中的一个点，使游戏对象可围绕该点旋转。例如，一把剪刀上的轴心。

**2D 相对关节 (Relative Joint 2D)** - 允许由刚体物理组件控制的两个游戏对象基于彼此的位置来保持相对位置。使用此关节可将两个对象保持相互偏移。例如，在空间射击游戏中，玩家身后有额外的排炮。

**2D 滑动关节 (Slider Joint 2D)** - 允许由刚体物理组件控制的游戏对象沿着空间中的一条线滑动（例如滑门）。

**2D 弹簧关节 (Spring Joint 2D)** - 允许由刚体物理组件控制的两个游戏对象就像通过弹簧连接在一起一样。

**2D 目标关节 (Target Joint 2D)** - 连接到指定目标，而不是像其他关节那样连接到另一个刚体对象。这是一种弹簧式关节，例如可将其用于拾取和移动处于重力作用下的对象。

**2D 车轮关节 (Wheel Joint 2D)** - 模拟车轮和悬架。



在关节这方面2D的花样比3D多



---



### 五、刚体（Rigidbody），碰撞器（Collider），触发器（Trigger）

要产生碰撞必须为游戏对象添加刚体（Rigidbody）和碰撞器，刚体可以让物体在物理影响下运动。碰撞体是物理组件的一类，它要与刚体一起添加到游戏对象上才能触发碰撞。如果两个刚体相互撞在一起，除非两个对象有碰撞体时物理引擎才会计算碰撞，在物理模拟中，没有碰撞体的刚体会彼此相互穿过。

**物体发生碰撞的必要条件**：

两个物体都必须带有碰撞器(Collider)，其中一个物体还必须带有Rigidbody刚体。

在unity3d中，能检测碰撞发生的方式有两种，一种是利用碰撞器，另一种则是利用触发器。

碰撞器：一群组件，它包含了很多种类，比如：Box Collider（盒碰撞体），Mesh Collider（网格碰撞体）等，这些碰撞器应用的场合不同，但都必须加到GameObjecet身上。

触发器：只需要在检视面板中的碰撞器组件中勾选IsTrigger属性选择框。

**触发信息检测**：

1.MonoBehaviour.OnTriggerEnter(Collider collider)当进入触发器

2.MonoBehaviour.OnTriggerExit(Collider collider)当退出触发器

3.MonoBehaviour.OnTriggerStay(Collider collider)当逗留触发器

**碰撞信息检测**：

1.MonoBehaviour.OnCollisionEnter(Collision collision) 当进入碰撞器

2.MonoBehaviour.OnCollisionExit(Collision collision) 当退出碰撞器

3.MonoBehaviour.OnCollisionStay(Collision collision) 当逗留碰撞器

**碰撞器和触发器的区别？**

碰撞器是触发器的载体，而触发器只是碰撞器身上的一个属性

当Is Trigger=false时，碰撞器根据物理引擎引发碰撞，产生碰撞的效果，可以调用OnCollisionEnter/Stay/Exit函数；

当Is Trigger=true时，碰撞器被物理引擎所忽略，没有碰撞效果，可以调用OnTriggerEnter/Stay/Exit函数。

也就是说在一次碰撞中，碰撞函数和触发函数只能二选一



---



### 六、预制体（Prefab）

什么是预制体：

在进行一些功能开发的时候, 我们常常将一些能够复用的对象制作成.prefab的预制体，然后将预制体存放到 Resources 目录之下, 使用时再动态加载到场景中并进行实例化. 例如 : 子弹、特效甚至音频等, 都能制作成预制体.

预制体英文名称即 Prefab 是一种资源类型 —— 存储在项目视图中的一种可重复使用的游戏对象.

预制体的优点：
预制体可以多次放入到多个场景中;
当你添加一个预制体到场景中, 就创建了它的一个实例;
所有的预制体实例链接到原始预制, 基本上等于就是它的克隆;
不管你的项目存在多少实例, 当你对预制体进行任何更改, 你将看到这些更改将应用于所有的实例上;



---



### 七、画布（Canvas）

Canvas (画布) 是所有 UI 组件的父物体 , 也就是说每一个 UI 组件都必须在 Canvas 下 , 作为 Canvas 的子物体 , 当你创建一个 UI 控件时 , 如果在 Hierarchy 视图没有 Canvas 组件的话 , unity会自动帮你创建一个 Canvas , 并将你想创建的 UI 控件置于 Canvas 下.

Canvas 的 Render Mode (渲染模式) 有三种 ：

1.**Screen Space - Overlay**

这种渲染模式表示 Canvas 下的所有的 UI 控件永远位于屏幕的前面 , 不管有没有相机 , UI元素永远在屏幕最前面

2.**Screen Space - Camera**

这种渲染模式 Canvas 和 摄像机之间有一定的距离 , 可以在摄像机和 Canvas 之间播放一些粒子特效

3.**World Space**

这种模式下 Canvas 就和普通的 3D 物体一样了 , 可以控制它的大小,旋转,缩放等 , 一般用来做血条



---



### 终、总结

作为unity回坑第一课收获还是蛮丰富的，以前一直没有做笔记的习惯，东西学了又忘，忘了还没地方复习，以后要养成写学习日记的习惯。

这次写的脚本都没有写注释，希望未来的我回来看的时候不会头大吧哈哈哈（这么简单的代码应该不至于）



---



### 	附录、脚本

1.角色控制脚本，控制角色移动和检测角色状态：

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

    public bool isOnGround;
    public float checkRadius;
    public LayerMask layer;
    public GameObject groundCheck;

    public bool playerDead;

    public float bounceH;
    

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        anim = GetComponent<Animator>();
    }

    void Update()
    {
        Movement();
        isOnGround = Physics2D.OverlapCircle(groundCheck.transform.position, checkRadius, layer);
        anim.SetBool("isOnGround", isOnGround);
    }

    void Movement()
    {
        xVelocity = Input.GetAxisRaw("Horizontal");
        rb.velocity = new Vector2(xVelocity * speed, rb.velocity.y);
        anim.SetFloat("speed", Mathf.Abs(rb.velocity.x));

        if(xVelocity != 0)
        {
            transform.localScale = new Vector3(xVelocity, 1, 1);
        }
    }

    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Fan"))
        {
            rb.velocity = new Vector2(rb.velocity.x, bounceH);
        }
        if (collision.gameObject.CompareTag("Spike"))
        {
            anim.SetTrigger("isDead");
        }
    }
    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.CompareTag("Spike"))
        {
            anim.SetTrigger("isDead");
        }
    }

    public void PlayerDead()
    {
        playerDead = true;
        GameManage.GameOver(playerDead);
    }

    private void OnDrawGizmosSelected()
    {
        Gizmos.color = Color.green;
        Gizmos.DrawWireSphere(groundCheck.transform.position, checkRadius);
    }
}
```



2.游戏管理脚本，涉及游戏重开和退出，以及控制界面：

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

using UnityEngine.UI;
using UnityEngine.SceneManagement;

public class GameManage : MonoBehaviour
{
    static GameManage instance;
    public Text score;

    public GameObject gameOverUI;

    private void Awake()
    {
        if(instance != null)
        {
            Destroy(gameObject);
        }
        instance = this;
    }

    void Update()
    {
        score.text = Time.timeSinceLevelLoad.ToString("000");
    }

    public void RestartGame()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
        Time.timeScale = 1;
    }

    public void Exit()
    {
        Application.Quit();
    }

    public static void GameOver(bool dead)
    {
        if (dead)
        {
            Time.timeScale = 0f;
            instance.gameOverUI.SetActive(true);
        }
    }
}
```



3.平台生成脚本：

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

    int spikeBallNum = 0;

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
}
```



4.平台上升脚本：

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



5.链锤画线脚本：

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



6.跳跃平台旋转动画脚本：

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

