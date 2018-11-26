---
title: Android应用软件设计-1
copyright: true

date: 2018-10-31 16:37:23
tags: [Android,Java]
categories: [Android]
password:
author: XQ
keywords: Android
description: Android Stdio
---

<center>Android应⽤软件设计</center>

## 1.主题概述

> 1.搭建android studio开发环境：安装android studio（以下简称as）和JDK、android SDK，主要进行安装和环境变量的配置。SDK可以选择自己下载配置，或通过as内部工具android SDK manager进行管理。
>
> 2.构建一个新的应用，按照要求设置包名和入口程序，并实现一个基本的图片展示功能：该任务主要为了入门并且初步锻炼我们的android应用开发能力，按照要求建立应用是规范化开发过程的第一步，并且Android Stdio也提供了快速设定以便于我们开始一个新项目。Android应用的入口activity是通过设置manifest里的activity标签来决定的。图片展示通过ImageView来实现，可以在layout里设置ImageView的src来展示，也可以通过在activity生命周期中调用setImageResource()的方法来实现。
>
> 3.将应用签名打包：创建了key，签名。生成release版本的app，并安装测试成功。

<!-- more -->

## 2.假设

> 本次试验只是安装、熟悉开发环境，完成开发APP的第一步，简单地显示了本订餐系统的Logo。

## 3.实现或证明

1.代码已上传至[Github][1]。

2.e1所生成的apk安装包。**见附图**

3.效果图。**见附图**

4.步骤:

```
1.新建工程，输入应用名字，公司域名，工程目录，点击下一步。
2.API尽量选择低版本用于兼容低版本系统，一般选择API15。
3.可以选择Empty Activity，No Activity。
4.输入活动名称与对应布局的名称，点击完成即可生成新工程的目录。
5.在Layout中添加ImageView控件，并设置约束。在drawable文件夹添加图片，并在ImageView中导入。布局见附图
6.在虚拟机中试运行。如不能运行需要调试程序
7.在Build中选择Generate Signed Bundle/APK.效果图见附图
```

## 4.结论

对主题的总结，结果评论，发现的问题，或你的建议和看法。如Android程序调试方法之间的优缺点比较（文字、图标、代码辅助等）

```
1.本次实验是第一次接触安卓开发，首先熟悉了Android Stdio创建的工程的目录结构，然后在书本与视频教程学到AS如何进行程序的启动，所以需要对manifest文件、java文件、xml文件三者组合起来使用，在res中文件是存储静态数据用的。
2.在添加布局中尽量使用代码进行添加。在新建活动时使用手动添加代码有助于熟悉安卓代码编程。
3.第一次图标添加实验不涉及调试，方法代码也是不多的，作为安卓开发实验第一课，很激动。
```

## 5.参考

## 参考书籍

```
【1】《Head First Android开发》
【2】《Android第一行代码》
【3】《Head First Java》
【4】《Java编程思想》
```

## 参考链接

【1】[Android的视频教程][2]

【2】[提供帮助的社区][3]

[1]:	https://github.com/xq773939719/XQ-Android-SCOS
[2]:	https://www.bilibili.com/video/av18008899
[3]:	https://www.jianshu.com/