---
title: 学它丫的游戏引擎
date: 2025-06-21 09:49:51
categories:
    - cv
tags: 
    - 游戏开发
---

# 前言

做此笔记，记录一下Unity的学习过程，也算是一种变相的监督。

# Unity简介

一个跨平台的游戏引擎，使用C#编写游戏逻辑，内置物理引擎（碰撞、刚体、布料）。

资金或年收入20w美金以下的开发者（企业）使用personal版本是免费的。

竞品有最近比较火热的开源引擎[Godot](https://godotengine.org/)。

Unity官方有不少免费教程（或者收费教程也有），这里我将学这个入门 [Ruby's Adventure：2D 初学者](https://learn.u3d.cn/tutorial/unity-ruby-adventure)

<img src="https://raw.githubusercontent.com/czp1623-g/PicBed/pic/img/843fc84b-ee81-447d-9155-b26c8aadb2c7______________27.png.400x0x1.webp" alt="img" />



## 专有名词

- Texture Type：纹理类型，比如Sprite(2D and UI)就是一种纹理类型
- Sprite：精灵，通常是一张图片经由Unity处理后得到的一个可绘制的对象



## 主要组成

### 编辑器

- Scene：实时的场景编辑器，支持拖拉拽。
- Hierarchy：与Scene联动，它将Scene中的所有游戏对象以目录树方式展现出来。
- Project：管理当前项目中的所有Resource，包括图像、音频、scene、C#脚本等。
- Console：看运行日志的地方。
- Game：可以理解为预览，玩家视角。
- Inspector：管理游戏对象、Resource等的属性的面板。
- Animator：在这里编排动画



### Sprite

**精灵**是在游戏中绘制的图像。**Unity** 无法使用你直接为 Ruby 导入的 **.png 图像文件**，而是需要将此文件转换为可使用的格式。

![image-20250621105418651](https://raw.githubusercontent.com/czp1623-g/PicBed/pic/img/image-20250621105418651.png)

精灵可以选择sprite mode，用single或者multiple（还有个polygon不知道是啥），选multiple的时候可以在Sprite Editor中自由裁剪图像而产生多个sprite



### Input Manager

Unity帮忙封装了一下input，将键盘、鼠标、手柄等设备输入的信号封装成可以直接使用的UnityEngine.lnput对象。

核心概念：

1. 输入轴(Input Axes)

    将物理输入（如键盘按键、手柄摇杆）映射为逻辑上的“轴”，输出一个 **[-1, 1] 范围内的值**，渐进的

2. 输入类型

    按键 Key：键盘上的物理键，比如空格键、shift键

    按钮 Button：手柄、触摸屏上的按键

    鼠标/摇杆轴 Mouse/：连续输入



![image-20250621163926416](https://raw.githubusercontent.com/czp1623-g/PicBed/pic/img/image-20250621163926416.png)



### Tilemaps - 瓦片地图

世界设计。**瓦片地图**将世界作为一个网格，你可以在其中为每个网格单元格设置不同的**精灵**。

![image-20250621224357244](https://raw.githubusercontent.com/czp1623-g/PicBed/pic/img/image-20250621224357244.png)







#### Tile Palette

美taɪl 美ˈpælət

瓦片调色板，一个可视化工具，管理瓦片，可以用它来绘制Tilemap。

可以根据自己喜好，调整Tile Palette的布局（即绘制调色板，注意不是绘制tilemap）

![image-20250623222000604](https://raw.githubusercontent.com/czp1623-g/PicBed/pic/img/image-20250623222000604.png)



### Order in Layer

层显示优先级

可以通过修改sprite renderer的该属性来控制game object的显示优先级，数值越大，优先级越高，与其他低优先级的game object重叠时覆盖在上方。

**如果game object的layer order相同呢？**

这时候根据Camera Settings来决定谁覆盖谁，比如下面配置是根据game object的y轴（垂直方向）来绘制，也就是谁y属性值更小，谁更应该在“前方”。

![image-20250623230534960](https://raw.githubusercontent.com/czp1623-g/PicBed/pic/img/image-20250623230534960.png)

**那么问题又来了，这个y属性值怎么来的，它的参考系是？**

有两种方案，一个是取sprite的center，另一个是取sprite的pivot（轴心）

![image-20250623231130017](https://raw.githubusercontent.com/czp1623-g/PicBed/pic/img/image-20250623231130017.png)

pivot默认是取底部中心，当然也可以自定义pivot位置。通过sprite editor可以直接修改。

![image-20250623231518484](https://raw.githubusercontent.com/czp1623-g/PicBed/pic/img/image-20250623231518484.png)

## C#

跟Java很类似，都是面向对象编程。

### controller

UnityEngine

- transform：对应游戏对象的Transform组件，可以控制对象的位置

MonoBehaviour

- Start()：仅在首次调用Update方法前调用，是异步的吗？会不会出现Start跟Update一起执行的情况？
- Update()：每一帧都会调用

