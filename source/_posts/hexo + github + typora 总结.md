---
title: 搭建个人博客自我总结
date: 2021-07-04 18:00:56
---



### 	一、用Hexo在本地生成博客并部署（白嫖）到github

后半部分个性化博客的教学先不看：

[GitHub+Hexo 搭建个人网站详细教程](https://zhuanlan.zhihu.com/p/26625249)





### 	二、将博客的源代码也放到github托管



文章最后面还有作者引用的原文，两篇文章结合一起看：

[Hexo | 怎么去备份你的Hexo博客](https://www.jianshu.com/p/baab04284923)





### 	三、最后就是用我们的Typora来写作啦

快捷键学不学无所谓，typora工具栏全都有：

[Hexo博客写文章及基本操作](https://zhuanlan.zhihu.com/p/156915260)





### 四、应用next主题以及个性化

next和hexo好像还有版本适配的问题，我用旧版的next会把博客变成乱码，最终是这篇文章中的新版本适合我：

[hexo的next主题个性化配置](https://zhuanlan.zhihu.com/p/60424755 )





### 终、报错汇总

```
git g -d  
```

报错：**fatal:  unable to access 'h...://github.com/....github.io.git/**

原因是git的代理出现问题？解决方法：

```
git config --global --unset git.proxy
```



