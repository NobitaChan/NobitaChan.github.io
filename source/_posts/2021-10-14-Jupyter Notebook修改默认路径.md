---
title: Jupyter Notebook修改默认路径
date: 2021-10-14 18:50:56
categories:	琐碎
tags:
  - jupyter
  - windows
---



### 	始、修改默认路径

1.打开Anaconda Prompt，输入`jupyter notebook --generate-config`

2.打开上一步生成的配置文件：

`C:\Users\Administrator\.jupyter\jupyter_notebook_config.py`

其中C:\Users\Administrator就是`Jupyter notebook`的默认路径：

3.打开`jupyter_notebook_config.py`，搜索`#c.NotebookApp.notebook_dir = ''`（214行），删除井号修改成你希望设置的目录路径，这里我修改成`c.NotebookApp.notebook_dir = 'D:\DeskTopD\JupyterProject'`

4.在win开始菜单中找到`jupyter notebook`快捷图标，鼠标右键>>属性>>快捷方式>>目标

删除最后的 `"%USERPROFILE%/"` ，如果是通过`Anaconda`控制台打开`jupyter notebook`则无需更改此项

