---
title: 《Robbie Swifthand》Unity2d独立游戏学习（八）
date: 2021-09-13 10:08:28
categories:	unity
tags:
  - unity
  - c#
---



### 	始、前言

先解决之前遗留下来的问题：场景重置时重复生成Audio Manager，导致声音重复并且游戏卡顿。

<!--more-->

修改Audio Manager脚本：

```
    private void Awake()
    {
        if(current != null)
        {
            Destroy(gameObject);
            return;
        }
        current = this;
        //切换场景也不会销毁这个物体
        DontDestroyOnLoad(gameObject);
    }
```

在场景初始化时添加判断`if(current != null)`，当场景中已经存在此类物体时摧毁刚生成的新物体并且不再执行以下代码



---



### 	一、GameManager

1.新建物体GameManager

1.新建脚本GameManager（注意拼写，拼写正确时脚本图标会变成齿轮），挂载到GameManager物体下：

```
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    static GameManager instance;

    private void Awake()
    {
        if(instance != null)
        {
            Destroy(gameObject);
            return;
        }
        instance = this;

        DontDestroyOnLoad(this);
    }

    public static void PlayerDied()
    {
        //经过1.5s后重新加载场景
        instance.Invoke("RestartScene", 1.5f);
    }

    void RestartScene()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }

}
```

完成角色死亡时重新加载场景的代码



3.然后在PalyerHealth脚本中调用GameManager.PlayerDied();

```
   private void OnTriggerEnter2D(Collider2D collision)
    {
        if(collision.gameObject.layer == trapsLayer)
        {
            Instantiate(deathVFXPrefab, transform.position, transform.rotation);
            gameObject.SetActive(false);

            AudioManager.PlayDeathAudio();

           /* SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);*/
           //调用GameManager中的方法来实现场景重置
            GameManager.PlayerDied();
        }
    }

```



4.添加重启场景的动画效果

