---
title: 《Speed Down》Unity2d游戏开发（六）
date: 2021-07-15 18:22:45
categories: unity
tags:
  - unity
  - c#
  - awake
  - canvas
---



### 	始、前言

《Speed Down》开发已经接近尾声啦！最后再来完善一下，变成真正能玩的游戏！

<!--more-->

---



### 	一、分数统计

1.新建UI → Text 物体，发现自动创建了Canvas并将Text作为子物体添加

2.调整Text的位置大小等参数直到满意

3.新建空物体命名GameManage，新建脚本也命名GameManage并挂载到空物体GameManage上：

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
        score.text = Time.time.ToString("000");
    }
}
```

Awake会在所有的Start之前调用，适合用作游戏的初始化

返回unity就可以看到随时间增加分数



---



### 	二、失败界面

1.在Canvas下添加Panel并按自己喜欢布置界面，添加两个按钮用作重新开始游戏和退出游戏

2.在GameManage脚本中添加：

```
    public void RestartGame()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);
        Time.timeScale = 1;
    }
    
        public void Exit()
    {
        Application.Quit();
    }
```

3.给两个按钮添加刚刚新建的方法

按钮的功能已经设置好，其中退出游戏的功能需要在项目build之后才能看到效果。现在我们希望游戏失败界面在失败后出现

4.取消勾选Panel的标签上面的框，这样就平时就不会显示Panel

5.在GameManage中添加：

```
    public static void GameOver(bool dead)
    {
        if (dead)
        {
            Time.timeScale = 0f;
            instance.gameOverUI.SetActive(true);
        }
    }
```

注意：想要在其他脚本中调用方法，需要定义方法成static类型

6.在PlayerController脚本中修改PlayerDead方法：

```
    public void PlayerDead()
    {
        playerDead = true;
        GameManage.GameOver(playerDead);
    }
```

当角色死亡时就会弹出失败界面



---



### 	终、测试

最后测试了一下，发现重新开始游戏时不会将分数重置，需要将GameManage脚本中的`Time.time.ToString`改成`Time.timeSinceLevelLoad.ToString`，这样一来时间计数就会在场景重置时重置。
